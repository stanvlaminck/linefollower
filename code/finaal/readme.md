#include "SerialCommand.h"
#include "EEPROMAnything.h"
#include <EEPROM.h>
#include "BluetoothSerial.h" // Voor Bluetooth-ondersteuning



#define motor1PWM1 18
#define motor1PWM2 19
#define motor2PWM1 4
#define motor2PWM2 16
#define Baudrate 115200

// Bluetooth-instantie
BluetoothSerial BTSerial;

// Functieprototypes
void onSet();
void onDebug();
void onCalibrate();
void onUnknownCommand(char* command);
void onRun();
void onCmd();
void IRAM_ATTR handleInterrupt(); // Interrupt-handler

SerialCommand sCmd(BTSerial); // Bluetooth-seriële poort voor SerialCommand
bool debug = false;
bool programEnabled = false; // Variabele voor programma-status
bool run;
unsigned long previous, calculationTime;

const int sensor[] = {26, 25, 33, 32, 35, 34, 39,36};

// Configuratie van interrupt en LED
const int INTERRUPT_PIN = 17;
const int LED_BUILTIN = 2;


struct param_t {
    unsigned long cycleTime;
    int black[8];
    int white[8];
    int power;
    float diff;
    float kp;
    float ki;
    float kd;
} params;

int normalised[8];
float debugPosition;
float iTerm, lastErr;

#define EEPROM_SIZE sizeof(param_t)



void setup() 
{
   
    // Initialiseer de seriële poorten
    BTSerial.begin(Baudrate);
    BTSerial.println("Initializing...");
    
    if (!BTSerial.begin("ESP32_Bochtje007")) {  // Initialiseer Bluetooth
        BTSerial.println("Bluetooth initialization failed!");
        while (1); // Stop als Bluetooth niet werkt
    }
   BTSerial.println("Bluetooth is ready to pair");

    // Commando's configureren
    sCmd.addCommand("set", onSet);
    sCmd.addCommand("debug", onDebug);
    sCmd.addCommand("calibrate", onCalibrate);
    sCmd.addCommand("run", onRun);
    sCmd.addCommand("cmd", onCmd); // Geeft een lijst van mogelijke commando's
    sCmd.setDefaultHandler(onUnknownCommand);

    EEPROM.begin(EEPROM_SIZE);
    EEPROM.get(0, params);
    EEPROM.end();

    // Configuratie van interrupt en LED
    pinMode(INTERRUPT_PIN, INPUT_PULLUP);
    pinMode(LED_BUILTIN, OUTPUT);
    attachInterrupt(digitalPinToInterrupt(INTERRUPT_PIN), handleInterrupt, CHANGE);

    BTSerial.println("Ready");
}






void loop() {

    unsigned long currentTime = millis();

    // Update LED-status op basis van programma-status
    digitalWrite(LED_BUILTIN, programEnabled);

    // Controleer of het programma mag draaien
    if (!programEnabled) 
     {
        return; // Sla de rest van de loop over als de switch laag is
     }

     // Hoofdprogramma
    unsigned long startCalculation = micros(); // Starttijd van de berekening

    float position = 0;
    unsigned long current = micros();
    sCmd.readSerial(); // Luisteren naar seriële commando's via Bluetooth

    if (current - previous >= params.cycleTime) previous = current;
    {
        int normalised[8];
        for (int i = 0; i < 8; i++) {
            constrain(map(analogRead(sensor[i]), params.black[i], params.white[i], 0, 1000), 0, 1000);       
        }
        

        int index = 0;
        for (int i = 1; i < 8; i++) if (normalised[i] < normalised[index]) index = i;

        if (index == 0) position = -30;
        else if (index == 7) position = 30;
        else {
            int sNul = normalised[index];
            int sMinEen = normalised[index - 1];
            int sPlusEen = normalised[index + 1];
            float b = sPlusEen - sMinEen;
            b = b / 2;
            float a = sPlusEen - b - sNul;
            position = -b / (2 * a);
            position += index;
            position -= 3.5;
            position *= 9.525;
        }

        debugPosition = position;

        // PID-regeling
        float error = -position;
        float output = error * params.kp;
        iTerm += params.ki * error;
        iTerm = constrain(iTerm, -510, 510);
        output += iTerm;
        output += params.kd * (error - lastErr);
        lastErr = error;

        int powerLeft = 0;
        int powerRight = 0;

        output = constrain(output, -510, 510);
        if (run) {
            if (output >= 0) {
                powerLeft = constrain(params.power + params.diff * output, -255, 255);
                powerRight = constrain(powerLeft - output, -255, 255);
                powerLeft = powerRight + output;
            } else {
                powerRight = constrain(params.power - params.diff * output, -255, 255);
                powerLeft = constrain(powerRight + output, -255, 255);
                powerRight = powerLeft - output;
            }
        }

         analogWrite(motor1PWM1, powerLeft > 0 ? powerLeft : 0);
         analogWrite(motor1PWM2, powerLeft < 0 ? -powerLeft : 0);
         analogWrite(motor2PWM1, powerRight > 0 ? powerRight : 0);
         analogWrite(motor2PWM2, powerRight < 0 ? -powerRight : 0);
    }

    calculationTime = micros() - startCalculation; // Bereken de berekeningstijd
}





void IRAM_ATTR handleInterrupt() 
{
    programEnabled = !programEnabled; // Wissel de program-status
    BTSerial.print("Program status changed: ");
    BTSerial.println(programEnabled ? "Enabled" : "Disabled");
}




void onSet() {
    char* parameter = sCmd.next();
    char* value = sCmd.next();

     if (strcmp(parameter, "power") == 0) 
     {
        params.power = atoi(value); // Zorg dat de waarde juist wordt omgezet naar een integer
        BTSerial.print("Power set to: ");
        BTSerial.println(params.power);
    }

    if (strcmp(parameter, "cycle") == 0) 
    {
        long newCycleTime = atol(value);
        float ratio = ((float)newCycleTime) / ((float)params.cycleTime);
        params.ki *= ratio;
        params.kd /= ratio;
        params.cycleTime = newCycleTime;
    
    } 
    
    
    else if (strcmp(parameter, "diff") == 0) params.diff = atof(value);
    else if (strcmp(parameter, "kp") == 0) params.kp = atof(value);
    else if (strcmp(parameter, "ki") == 0) {
        float cycleTimeInSec = ((float)params.cycleTime) / 1000000;
        params.ki = atof(value) * cycleTimeInSec;
    } else if (strcmp(parameter, "kd") == 0) {
        float cycleTimeInSec = ((float)params.cycleTime) / 1000000;
        params.kd = atof(value) / cycleTimeInSec;
    }

    EEPROM.begin(EEPROM_SIZE);
    EEPROM.put(0, params);
    EEPROM.commit();
    EEPROM.end();
}




void onDebug() {
    BTSerial.print("cycle time: ");
    BTSerial.println(params.cycleTime);
    BTSerial.print("power: ");
    BTSerial.println(params.power);
    BTSerial.print("kp: ");
    BTSerial.println(params.kp);
    BTSerial.print("ki: ");
    BTSerial.println(params.ki);
    BTSerial.print("kd: ");
    BTSerial.println(params.kd);
    BTSerial.print("Diff: ");  // Voeg diff toe
    BTSerial.println(params.diff);

    // Voeg calculationTime toe
    BTSerial.print("Calculation time (µs): ");
    BTSerial.println(calculationTime);


    // Voeg de sensorwaarden toe aan de debugoutput
    BTSerial.println("Sensor readings:");
    for (int i = 0; i < 8; i++) 
    {
        BTSerial.print("Sensor ");
        BTSerial.print(i);
        BTSerial.print(": ");
        BTSerial.println(analogRead(sensor[i])); // Lees en toon de waarde van elke sensor
     }

     // Voeg de genormaliseerde waarden toe
     BTSerial.print("Normalised readings: ");
    for (int i = 0; i < 8; i++) {
        BTSerial.print(normalised[i]); // Toon de genormaliseerde waarde
        if (i < 7) BTSerial.print(", "); // Voeg komma's toe tussen de waarden
    }

    // Toon de positie
    BTSerial.print("Position: ");
    BTSerial.println(debugPosition);

}





void onUnknownCommand(char* command) 
{
  
    BTSerial.print("unknown command: \"");
    BTSerial.print(command);
    BTSerial.println("\"");
}


void onCalibrate() {
    char* param = sCmd.next();
    if (strcmp(param, "black") == 0) {
        BTSerial.print("start calibration black...");
        for (int i = 0; i < 8; i++) params.black[i] = analogRead(sensor[i]);
        BTSerial.println("done");
    } else if (strcmp(param, "white") == 0) {
        BTSerial.print("start calibration white...");
        for (int i = 0; i < 8; i++) params.white[i] = analogRead(sensor[i]);
        BTSerial.println("done");
    }

    EEPROM.begin(EEPROM_SIZE);
    EEPROM.put(0, params);
    EEPROM.commit();
    EEPROM.end();
}

void onRun() {
    char* parameter = sCmd.next();
    if (strcmp(parameter, "on") == 0) {
        iTerm = 0;
        run = true;
    } else run = false;
}

void onCmd() {
    BTSerial.println("-------Possible commands:-------");
    BTSerial.println("calibrate white");
    BTSerial.println("calibrate black");
    BTSerial.println("set cycle 'x' (<10000 µs)");
    BTSerial.println("set power 'x' (0...255)");
    BTSerial.println("set diff 'x' (0...1)");
    BTSerial.println("set kp 'x'");
    BTSerial.println("set ki 'x'");
    BTSerial.println("set kd 'x'");
}












// comandos voor seriele monitor : set cycle 2600 , calibrate black, calibrate white, debug "krijg je waardes te zien van w en b" 

/*
set cycle <x>
Stel de cyclus tijd in (in microseconden, <10000 µs).

set power <x>
Stel het motorvermogen in (tussen 0 en 255).   

set diff <x>
Stel het verschil in (tussen 0 en 1). (2 - 5)

set kp <x>
Stel de proportionele constante (kp) in. (0.1 - 0.3)

set ki <x>
Stel de integrale constante (ki) in.

set kd <x>
Stel de afgeleide constante (kd) in. (0.1 - 1)

calibrate black
Calibreer de sensoren voor de zwarte lijn.                                    ok

calibrate white
Calibreer de sensoren voor de witte lijn.                                    ok

debug
Toon de huidige instellingen van cyclus tijd, vermogen, kp, ki, en kd.        ok

run on
Zet het programma aan en start de motorbesturing.                              ok

run off
Zet het programma uit en stop de motorbesturing.                                ok

cmd
Geeft een lijst van de beschikbare commando's.                                    ok
*/

 






