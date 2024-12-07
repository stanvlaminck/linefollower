# Instructable

Een instructable is een stappenplan - zonder verdere uitleg - hoe je vertrekkend van de bill of materials en gebruik makend van de technische tekeningen de robot kan nabouwen. Ook de nodige stappen om de microcontroller te compileren en te uploaden staan beschreven.  

Instructable: Maak je eigen Line-Follower Robot
Met deze gids maak je een eenvoudige line-following robot met een ESP32 en andere componenten. We beginnen met het printen van de mechanische onderdelen en monteren vervolgens alles op het chassis.

Benodigdheden
QTR-8A Reflectance Sensor Array (Pololu)
ESP32
DRV8833 Motor Driver
Diswitch
2 DC-motorenp
Batterijhouder
3D-geprinte onderdelen (wielhouders, chassis) – te vinden in de GitHub-repository.
T-strip of sterke lijm
Dubbelzijdige tape
Elektronische bedrading
Stap 1: Print de mechanische onderdelen
Download de STL-bestanden van de "Mechanische Onderdelen"-map in de GitHub-repository en print deze met een 3D-printer. Zorg ervoor dat je de wielhouders en het chassis correct print en dat de afmetingen kloppen.

Stap 2: Monteer de onderdelen
2.1 Wielhouders plaatsen
Plaats de wielhouders aan de achterkant van het chassis.
Gebruik lijm of T-strip om ze stevig tegen het chassis te bevestigen.
Laat de lijm drogen voordat je verdergaat.
2.2 Motoren monteren
Schuif de motoren in de wielhouders.
Zorg ervoor dat de motorassen vrij kunnen draaien en goed zijn uitgelijnd.
2.3 Batterijhouder bevestigen
Bevestig de batterijhouder aan de bovenkant van het chassis met een T-strip of sterke lijm.
Zorg ervoor dat de batterijhouder stevig vastzit.


Stap 3: Plaatsen van elektronica
3.1 QTR-8A sensor bevestigen
Bevestig de QTR-8A Reflectance Sensor Array met dubbelzijdige tape aan de voorkant van het chassis.
Zorg ervoor dat de sensoren naar de grond gericht zijn en een paar millimeter boven het oppervlak zweven.
3.2 Dipswitch en breadboard
Monteer de dipswitch op een breadboard.
Sluit de dipswitch aan op de ESP32 volgens het schema in de GitHub-repository.


Stap 4: Aansluiten van bedrading
4.1 Motoren verbinden
Verbind de motoren met de juiste pinnen van de DRV8833 dubbele H-brug.
Volg het bedradingsschema in de GitHub-repository om ervoor te zorgen dat alles correct is aangesloten.
4.2 DRV8833 aansluiten op ESP32
Verbind de DRV8833 motorcontroller met de ESP32 volgens het schema in de GitHub-repository.
Sluit ook de voedingsdraden aan op de batterijhouder.


Stap 5: Controleer de assemblage
Bekijk de foto van het afgewerkte project in de GitHub-repository om te controleren of alles correct is gemonteerd.
Zorg ervoor dat alle draden goed zijn aangesloten en dat niets los zit.


Stap 6: Laat je robot tot leven komen!
Nu je de hardware klaar hebt, kun je de software uploaden naar de ESP32 om je robot de lijn te laten volgen. Zorg ervoor dat de instellingen van de QTR-8A zijn aangepast aan jouw lijnvolgeromgeving.

Gefeliciteerd! Je hebt nu een volledig functionele line-following robot. 🚗💨

Voor vragen of problemen kun je terugkijken naar de GitHub-repository. Veel succes!
