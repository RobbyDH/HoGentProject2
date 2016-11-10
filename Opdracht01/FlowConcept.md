## Proof-of-concept Flow ##

1. De gebruiker logt in via de front-end webapplicatie op de gitrepository waar hij een project kan uploaden.

2. Dit kan een Java, .NET, PHP, ... project zijn. Jenkins luistert (via poort 8080) naar de github repository voor changes in een repository.

3. De Jenkins server waar de applicatie gebuild wordt detecteerd een project op de repository en haalt deze binnen met een pull request.

4. Jenkins build de applicatie en voert een post-build process uit. Dit houdt in dat Jenkins de gebuilde applicatie upload naar een server.

5. De applicatie is meteen bereikbaar op de server.

6. De gebruiker kan naar het serveradres surfen om het resultaat de bekijken.


Auteurs Mike Lobbezoo & Davy De Cock
