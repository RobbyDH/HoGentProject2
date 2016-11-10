## Testplan labo 1, Console verbinden met Switch

```
Switch> show clock
*0:0:19.573 UTC Mon Mar 1 1993
Switch# clock set 15:08:00 Feb 28 2016
Switch# show clock
*15:8:18.105 UTC Sun Feb 28 2016
```

Bij stap 4 hebben we enige problemen ondervonden doordat Tera Term al open stond voordat de Switch werd opgestart. 

Door dat Tera Term al open stond kon hij geen connectie vinden met de Switch en moest de Switch herstart worden. 

Eens dat gebeurt was, hebben we geen problemen meer ondervonden bij dit labo.

## Testplan labo 2, 2 Switches met 2 PCs verbinden, via ethernet
Geen problemen ondervonden bij dit labo.

## Testplan labo 3, Netwerk met switch en router
We hadden de router verbonden met een straight-trough naar een END-device, en er was daardoor geen rechtstreekse verbinding mogelijk met de router. Nadien hebben we de router en de end-device verbonden via een cross-over cable en het probleem was opgelost. 

Nadien bleek een switch niet meer te werken en was het ook niet meer mogelijk te pingen van PC-A naar PC-B. Alles was correct ingesteld en werkte, buiten de kapotte switch device.

## Testplan labo 4, Basic Static Route Configuration
Bij het maken van de opstelling gingen we er eerst van uit dat we bij het pingen al meteen verbinding konden maken met de andere pc’s. Dit bleek echter niet waar te zijn omdat de statische route table niet ingesteld was. 

Na het configureren van de “ip route” konden de pc’s buiten hun netwerk pingen naar andere pc’s van een andere router. Een goeie tip is om altijd twee keer na te kijken ofdat de pc’s de juiste ip’s hebben toegewezen gekregen hebben. Wij hebben hier enige problemen mee mogen ervaren.

In de fysieke opstelling van labo 4 ging alles feilloos buiten het feit dat 1 PC een foutieve netwerkkaart had. 

In de volgende deelopdracht zullen we elke stap uit het testplan opvolgen in het testrapport!
----------

Uitvoerder(s) test: Mike Lobbezoo, Davy De Cock

Uitgevoerd op: 28/02/2016

Github commit:  COMMIT HASH
