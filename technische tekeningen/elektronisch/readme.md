Elektrischschema
Ik heb een schema gemaakt met een ESP32-microcontroller, een QTR-8A-sensor, een motor driver (waarschijnlijk een L298N) en een paar DC-motoren. Hier is een kort overzicht van de belangrijkste onderdelen en enkele opgenomen:

Belangrijke componenten:
ESP32 :
Dit is de hoofdmicrocontroller die de verwerking doet. Zorg ervoor dat de voedingsupply stabiel is.

QTR-8A :
Dit is een array van reflectiesensoren voor lijndetectie. Controleer of VCC en GND goed zijn aangesloten en dat de uitgangen correct zijn verbonden met de GPIO-pinnen van de ESP32.

Motoraandrijving (L298N) :
Deze verbindt met de ESP32 en het bestuur van de motoren. Zorg ervoor dat de signaalpinnen van de ESP32 goed zijn aangesloten op de juiste besturingspinnen van de L298N.

DC-motoren :
Zorg ervoor dat ze geschikt zijn voor de spanning en stroom die door de driver wordt geleverd.

DIP-schakelaar :
Dit kan worden gebruikt om verschillende modi van instellingen te selecteren. Zorg ervoor dat deze goed is geconfigureerd en dat de ESP32 de instellingen kunnen lezen.
Overwegingen:
Voedingsbron : Controleer of de voedingsbron kan omgaan met de stroomvereisten van de motoren.

Aansluitingen : Controleer alle draadverbindingen om kortsluiting te voorkomen.


