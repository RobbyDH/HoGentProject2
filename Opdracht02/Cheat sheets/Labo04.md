# Cheat Sheet Labo 4

IOS berichten tegenhouden die naar de console of telnet zouden kunnen gaan. Om dit tegen te houden kan je het commando **logging
synchornous** gebruiken. Op die manier word je keyboard input niet verstoort door telnet of console.

Ga naar de console verbinding: **line console 0**

Ga naar een “virtual line”: **line vty 0 4**

Stel een time-out in. Als er geen input gedecteerd wordt tijdens het meegeven interval zal de EXEC faciliteit de huidige verbinding

verder werken: **exec-timeout minuten seconden.**

Bekijk een routing table terwijl je interfaces aan het aanpassen bent (ip addressen toekennen of een interface uit of aan zetten): **debug
ip routing.**

Bekijk de huidige ip addressen van een bepaald device: **show ip route.**

Stel een clock speed in: **clock rate XXXXX (in bytes).**

Voeg een ip route toe: **ip route xxx.x.x.x xxx.xxx.xxx.x xxx.xxx.x.x (ip address 1 = bestemmings addres, ip address 2 = subnet mask, ip
address 3 het addres van de router waarmee je de huidige router verbind).**

