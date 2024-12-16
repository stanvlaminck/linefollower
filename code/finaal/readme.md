Deze code is een programma voor een ESP32-microcontroller waarmee een line-following robot bestuurd wordt. Het maakt gebruik van Bluetooth voor communicatie en bevat functionaliteit om sensoren te kalibreren, parameters aan te passen, en het programma te debuggen. Hieronder een overzicht van de belangrijkste onderdelen en functionaliteiten:

Functionaliteiten
Bluetooth-communicatie:

De ESP32 communiceert via Bluetooth. Het apparaat heet standaard "ESP32_Bochtje007".
Gebruikers kunnen commando's sturen om instellingen te wijzigen of acties te starten.
Motorbesturing:

Twee motoren worden aangestuurd via PWM (Pulse Width Modulation) op 4 pinnen (motor1PWM1, motor1PWM2, motor2PWM1, motor2PWM2).
De richting en snelheid van de motoren worden geregeld op basis van de PID-regelkring.
PID-regelkring:

De robot bepaalt zijn positie ten opzichte van een zwarte lijn met behulp van sensoren.
Op basis van de afwijking (error) wordt een output berekend met een PID-regelaar (Proportioneel, Integrerend, Differentiërend).
De output beïnvloedt de snelheid van de linker- en rechtermotor om de robot bij te sturen.
Sensorinput:

De robot gebruikt 8 sensoren (sensor[]) om een lijn te volgen.
Waarden van de sensoren worden genormaliseerd (tussen 0 en 1000) en gebruikt om de positie van de lijn te bepalen.
Commando's:

De gebruiker kan parameters zoals motorkracht, PID-waarden (kp, ki, kd), en cyclus tijd aanpassen via commando's.
Er zijn ook commando's om de sensoren te kalibreren voor zwart (calibrate black) en wit (calibrate white).
Debuggegevens kunnen opgevraagd worden, zoals huidige instellingen, sensorwaarden, en de berekende positie.
Interrupt:

Een externe interrupt op pin INTERRUPT_PIN schakelt het programma aan of uit (programEnabled).
Belangrijke Structuren en Variabelen
params: Bevat de instellingen van de robot, zoals PID-parameters, motorkracht, en kalibratiewaarden voor de sensoren.
EEPROM: Wordt gebruikt om instellingen persistent op te slaan, zodat deze behouden blijven na herstarten.
iTerm en lastErr: Worden gebruikt door de PID-regelaar om integrale en afgeleide termen te berekenen.
Hoofdprogramma (loop)
Bluetooth-commandoverwerking:

Leest binnenkomende Bluetooth-commando's met sCmd.readSerial().
Sensorwaarden normaliseren:

Lees analoge waarden van de sensoren.
Map deze waarden naar een genormaliseerd bereik (0-1000).
Positiebepaling:

Berekent de positie van de lijn t.o.v. de robot.
PID-regeling:

Bereken de correctie-output om de motorsnelheid aan te passen.
Motorsturing:

Pas PWM-signalen toe op de motorpinnen om de robot bij te sturen.
Debug-mogelijkheden
Het debug-commando geeft uitgebreide informatie over:

PID-parameters
Sensorwaarden en genormaliseerde waarden
Huidige positie van de lijn
Berekeningstijd van de PID-regelaar
Gebruik
De robot kan worden bediend en geconfigureerd via Bluetooth. Typ bijvoorbeeld:

set cycle 3000 om de cyclus tijd in te stellen.
calibrate black en calibrate white om de sensoren te kalibreren.
run on om de robot te laten starten met het volgen van de lijn.
Deze code biedt een volledig werkend systeem voor een line-following robot, met ondersteuning voor aanpassingen en debugging via Bluetooth.






