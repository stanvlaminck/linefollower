# Sensoren proof of concept

const int sensorPins[8] = {26, 25, 33, 32, 35, 34, 39, 36}; // GPIO's voor de QTR-8A
int sensorValues[8]; // Opslag voor sensorwaarden

void setup() {
  Serial.begin(115200); // Start seriële communicatie
  analogReadResolution(12); // Gebruik maximale ADC-resolutie (12-bit)
}

void loop() {
  // Lees de waarde van elke sensor
  for (int i = 0; i < 8; i++) {
    sensorValues[i] = analogRead(sensorPins[i]);
  }

  // Print de waarden naar de seriële monitor
  Serial.print("Sensorwaarden: ");
  for (int i = 0; i < 8; i++) {
    Serial.print(sensorValues[i]);
    Serial.print("\t"); // Tabs voor leesbaarheid
  }
  Serial.println(); // Nieuwe regel

  delay(1000); // Wacht 100 ms
}


