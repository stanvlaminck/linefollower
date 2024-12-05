# start/stop interrupt proof of concept
olatile int interruptCounter = 0; // Telt het aantal interrupt-events
portMUX_TYPE mux = portMUX_INITIALIZER_UNLOCKED; // Voor thread-veiligheid

void IRAM_ATTR handleInterrupt() {
  // Interrupt-service routine
  portENTER_CRITICAL_ISR(&mux);
  interruptCounter++;
  portEXIT_CRITICAL_ISR(&mux);
}

void setup() {
  Serial.begin(115200);
  delay(1000); // Wacht een moment voor de seriële verbinding

  Serial.println("Interrupt Test Start");

  // Definieer de GPIO-pin als input en koppel een interrupt
  pinMode(15, INPUT_PULLUP); // Gebruik GPIO 15 (pas dit aan naar de gewenste pin)
  attachInterrupt(digitalPinToInterrupt(15), handleInterrupt, CHANGE);

  // Gebruik andere GPIO-pinnen als je meerdere interrupts wilt testen
  // pinMode(4, INPUT_PULLUP);
  // attachInterrupt(digitalPinToInterrupt(4), handleInterrupt, FALLING);
}

void loop() {
  static int lastInterruptCounter = 0;

  // Controleer of er nieuwe interrupts zijn verwerkt
  if (interruptCounter != lastInterruptCounter) {
    portENTER_CRITICAL(&mux);
    int currentCount = interruptCounter;
    portEXIT_CRITICAL(&mux);

    Serial.print("Interrupt Count: ");
    Serial.println(currentCount);

    lastInterruptCounter = currentCount;
  }
  
  delay(100); // Kleine vertraging om CPU-gebruik te beperken
}
