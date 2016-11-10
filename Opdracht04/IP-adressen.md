#IP-adressen#
----

##Aantal hosts 

Lage kost: 

- Productieomgeving: 19 x 2 + 4 = 42 Hosts 
- Vergaderzaal/Patchkast: 8 x 2 + 4 = 20 Hosts
- Kantoor1: 46 x 2 + 4 = 96 Hosts
- Kantoor2: 17 x 2 + 4 = 38 Hosts

**Totaal: 196 Hosts**

Middel/Hoge kost: 

- + 1 wifi access point

**Totaal: 198 Hosts**

-------
## C-klasse zonder subnetten

Gebruikte klasse: Klasse C

Aantal subnetten: geen 
 
Totale mogelijke hosts: 256 - 2(netwerkadres en broadcastadres) = 254 

Subnetmask = 255.255.255.0

Range: 192.168.1.0 -> 192.168.1.255 

| netwerk adres | subnetmask | CIDR | Adresrange | Broadcast | #hosts | 
|:--|:--|:--|:--|:--|:--|
| 192.168.1.0 | 255.255.255.0 | /24 | 192.168.1.1 - 192.168.1.254 | 192.168.1.255 | 254 | 

----------
## C-klasse met 2 subnetten

Gebruikte klasse: klasse C 

aantal subnetten: 2 (meerdere lukken niet aangezien we dan tekort hebben aan hosts om in de subnetten te reserveren)

###1ste subnet

Naam: Vergaderzaal/Patchkast/Kantoor1

Aantal hosts nodig: 80 Hosts

Aantal hosts om te gebruiken: 128 - 2 = 126

Subnet Mask: 255.255.255.128 /25

Range: 192.168.1.0 - 192.168.1.127

###2de subnet

Naam: Productie/Kantoor2

Aantal hosts nodig: 116 Hosts

Aantal hosts om te gebruiken: 128 - 2 = 126

Subnet Mask: 255.255.255.128 /25

Range: 192.168.1.128 - 192.168.1.255


| Nr. Subnet | Naam subnet | netwerk adres | subnetmask | CIDR | Adresrange | Broadcast | #hosts | 
|:--|:--|:--|:--|:--|:--|:--|:--|
|1|Vergaderzaal/Patchkast/Kantoor1| 192.168.1.0 | 255.255.255.128 | /25 | 192.168.1.1 - 192.168.1.126 | 192.168.1.127 | 126 | 
|2|Productie/Kantoor2| 192.168.1.128 | 255.255.255.128 | /25 | 192.168.1.129 - 192.168.1.254 | 192.168.1.255 | 126 | 

------------

##B-klasse met 4 subnetten Lage kost

Hier is nog veel uitbreiding mogelijk. Elk kantoor is opgedeeld per subnet

###1ste subnet

Naam: Productievoorbereiding

Aantal hosts nodig: 42 Hosts

Aantal hosts om te gebruiken: 64 - 2 = 62

Subnet Mask: 255.255.255.192 /26

Range: 172.16.0.0 - 172.16.0.63

###2de subnet

Naam: Vergaderzaal/Patchkast

Aantal hosts nodig: 20 Hosts

Aantal hosts om te gebruiken: 32 - 2 = 30

Subnet Mask: 255.255.255.224 /27

Range: 172.16.0.64 - 172.16.0.95

###3de subnet

Naam: Kantoor1

Aantal hosts nodig: 96 Hosts

Aantal hosts om te gebruiken: 128 - 2 = 126

Subnet Mask: 255.255.255.128 /25

Range: 172.16.0.96 - 172.16.0.223

###4de subnet

Naam: Kantoor2

Aantal hosts nodig: 38 Hosts

Aantal hosts om te gebruiken: 64 - 2 = 62

Subnet Mask: 255.255.255.192 /26

Range: 172.16.0.224 - 172.16.1.31

| Nr. Subnet | Naam subnet | netwerk adres | subnetmask | CIDR | Adresrange | Broadcast | #hosts | 
|:--|:--|:--|:--|:--|:--|:--|:--|
|1|Productievoorbereiding| 172.16.0.0 | 255.255.255.192 | /26 | 172.16.0.1 - 172.16.0.62 | 172.16.0.63 | 62 | 
|2|Vergaderzaal/Patchkast| 172.16.0.64 | 255.255.255.224 | /27 | 172.16.0.65 - 172.16.0.94 | 172.16.0.95 | 30 | 
|3|Kantoor1| 172.16.0.96 | 255.255.255.128 | /25 | 172.16.0.97 - 172.16.0.222 | 172.16.0.223 | 126 |
|4|Kantoor2| 172.16.0.224 | 255.255.255.192 | /26 | 172.16.0.225 - 172.16.1.30 | 172.16.1.31 | 62 |  

------------

##B-klasse met 4 subnetten Middel/Hoge kost

Hier is nog veel uitbreiding mogelijk. Elk kantoor is opgedeeld per subnet

###1ste subnet

Naam: Productievoorbereiding

Aantal hosts nodig: 42 Hosts

Aantal hosts om te gebruiken: 64 - 2 = 62

Subnet Mask: 255.255.255.192 /26

Range: 172.16.0.0 - 172.16.0.63

###2de subnet

Naam: Vergaderzaal/Patchkast

Aantal hosts nodig: 18 Hosts

Aantal hosts om te gebruiken: 32 - 2 = 30

Subnet Mask: 255.255.255.224 /27

Range: 172.16.0.64 - 172.16.0.95

###3de subnet

Naam: Kantoor1

Aantal hosts nodig: 98 Hosts

Aantal hosts om te gebruiken: 128 - 2 = 126

Subnet Mask: 255.255.255.128 /25

Range: 172.16.0.96 - 172.16.0.223

###4de subnet

Naam: Kantoor2

Aantal hosts nodig: 40 Hosts

Aantal hosts om te gebruiken: 64 - 2 = 62

Subnet Mask: 255.255.255.192 /26

Range: 172.16.0.224 - 172.16.1.31

| Nr. Subnet | Naam subnet | netwerk adres | subnetmask | CIDR | Adresrange | Broadcast | #hosts | 
|:--|:--|:--|:--|:--|:--|:--|:--|
|1|Productievoorbereiding| 172.16.0.0 | 255.255.255.192 | /26 | 172.16.0.1 - 172.16.0.62 | 172.16.0.63 | 62 | 
|2|Vergaderzaal/Patchkast| 172.16.0.64 | 255.255.255.224 | /27 | 172.16.0.65 - 172.16.0.94 | 172.16.0.95 | 30 | 
|3|Kantoor1| 172.16.0.96 | 255.255.255.128 | /25 | 172.16.0.97 - 172.16.0.222 | 172.16.0.223 | 126 |
|4|Kantoor2| 172.16.0.224 | 255.255.255.192 | /26 | 172.16.0.225 - 172.16.1.30 | 172.16.1.31 | 62 |  

--------
Uitvoerder(s) : Robby Den Haese, Siebert Timmermans

Uitgevoerd op: 27/04/16



