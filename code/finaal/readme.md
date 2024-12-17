# Overzicht: Line-Following Robot Code  

Dit programma is ontworpen voor een ESP32-microcontroller en bestuurt een line-following robot. Het maakt gebruik van Bluetooth-communicatie, bevat een PID-regelkring voor motorbesturing, en biedt functionaliteit om sensoren te kalibreren en parameters aan te passen.  

## Functionaliteiten  

### Bluetooth-communicatie  
- **Apparaatnaam:** Standaard ingesteld op "ESP32_Bochtje007".  
- **Interactie:** Gebruikers kunnen via Bluetooth commando's sturen om instellingen te wijzigen, sensoren te kalibreren, en acties te starten.  

### Motorbesturing  
- Aansturing van twee motoren via PWM (Pulse Width Modulation).  
- PWM-signalen worden toegepast op vier pinnen: `motor1PWM1`, `motor1PWM2`, `motor2PWM1`, en `motor2PWM2`.  
- De snelheid en richting van de motoren worden geregeld door een PID-regelaar.  

### PID-regelkring  
- De positie ten opzichte van een zwarte lijn wordt bepaald met sensoren.  
- Een PID-regelaar (Proportioneel, Integrerend, Differentiërend) berekent een correctie-output om de motorsnelheid aan te passen en de robot bij te sturen.  

### Sensorinput  
- De robot gebruikt een array van 8 sensoren.  
- Sensorwaarden worden genormaliseerd (bereik: 0-1000) en gebruikt om de lijnpositie te bepalen.  

### Commando's  
- **Parameteraanpassing:** Gebruikers kunnen motorkracht, PID-waarden (`kp`, `ki`, `kd`), en de cyclustijd aanpassen.  
- **Kalibratie:** Kalibreer sensoren voor zwart (`calibrate black`) en wit (`calibrate white`).  
- **Debugging:** Vraag instellingen, sensorwaarden, en lijnpositie op via Bluetooth.  

### Interrupt-functionaliteit  
- Externe interrupt op `INTERRUPT_PIN` schakelt het programma in of uit (`programEnabled`).  

---

## Belangrijke Structuren en Variabelen  

- **`params`:** Bevat PID-parameters, motorkracht, en kalibratiewaarden.  
- **EEPROM:** Slaat instellingen op zodat deze behouden blijven na een herstart.  
- **`iTerm` en `lastErr`:** Variabelen voor de integrale en afgeleide termen in de PID-regelaar.  

---

## Hoofdprogramma (loop)  

### Bluetooth-commandoverwerking  
- Leest binnenkomende Bluetooth-commando's met `sCmd.readSerial()`.  

### Sensorwaarden normaliseren  
- Analoge waarden van sensoren worden genormaliseerd naar een bereik van 0-1000.  

### Positiebepaling  
- Berekent de lijnpositie ten opzichte van de robot.  

### PID-regeling  
- Berekent een correctie-output op basis van de foutwaarde (error) om de motorsnelheid bij te sturen.  

### Motorsturing  
- PWM-signalen worden toegepast op de motorpinnen om richting en snelheid te regelen.  

---

## Debug-functionaliteit  
- Uitgebreide debuggegevens zijn beschikbaar via Bluetooth:  
  - Huidige PID-parameters  
  - Sensorwaarden (ongekalibreerd en genormaliseerd)  
  - Berekende lijnpositie  
  - PID-berekeningstijd  

---

## Gebruik  

De robot kan worden bediend en geconfigureerd via Bluetooth. Enkele voorbeelden van commando's:  
- **`set cycle 3000`**: Stel de cyclustijd in.  
- **`calibrate black`** en **`calibrate white`**: Kalibreer de sensoren.  
- **`run on`**: Start het lijnvolgprogramma.  

Deze code biedt een volledig systeem voor een line-following robot, inclusief aanpasbare instellingen en uitgebreide debugmogelijkheden.  



