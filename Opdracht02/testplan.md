# Testplan taak 1: Deelopdracht 1, labo's

## Testplan labo 1, Console verbinden met Switch ##

![](https://i.gyazo.com/fb123152afadfd913ce47e4e64a4a042.png)

1. Verbind de Switch met de PC via een console kabel
2. Gebruik Tera Term om via de console te kunnen werken, te downloaden op [Tera Term](http://logmett.com/index.php?/download/free-downloads.html "Tera download")
3. Kies de Serial radio button die verbonden is met de COM-poort
4. Hou er rekening mee dat Tera Term eerst verbonden moet zijn voordat de switch opgestart wordt
5. Via show version kan je de IOS versie van de switch bekijken bij "System image file"
6. Configuring the clock
	1. Switch> show clock
	2. Switch> enable (privileged EXEC)
	3. Switch# clock set 15:08:00 Feb 28 2016
	4. Switch# show clock

## Testplan labo 2, 2 Switches met 2 PCs verbinden, via ethernet ##

![](https://i.gyazo.com/27c237b3782bb8c26034821e4beaad5a.png)

1. Verbind de Ethernetkabel aan de Switch 1 op F0/1 met F0/1 op Switch 2
2. Verbind de PC-A via de NIC poort en de F0/6 poort op Switch 1
3. Verbind de PC-B via de NIC poort en de F0/18 poort op Switch 2
4. Check of lichtje groen zijn
5. Configuring IP
	1. Navigeer naar de netwerkconnectie die je net gelegd hebt Netwerk en Internet
	2. Rechterklik om aan de properties te geraken
	3. Kies de properties van IPv4
	4. Vul het IP-adres in, 192.168.1.10 met subnet 255.255.255.0
6. Verify de connectivity
	1. open het cmd.exe
	2. bekijk het IP-adres met ipconfig /all
	3. ping 192.168.1.11
	4. Als dit niet werkt, troubleshoot of probeer opnieuw
7. Configure en verify Switch
	1. Gebruik Tera Term om te connecteren tussen Switch en PC A
	2. Switch> enable
	3. Switch# configure terminal - toegang tot configuration
	4. Switch(config)# hostname S1 - verander hostname van Switch
	5. S1(config)# no ip domain-lookup - tegenhouden van ongewenste DNS lookups
	6. Passwords toevoegen
		1. S1(config)# enable secret class 
		2. S1(config)# line con 0 
		3. S1(config-line)# password cisco 
		4. S1(config-line)# login 
		5. S1(config-line)# exit 
		6. S1(config)#
	7. MOTD banner toevoegen
		1. S1(config)# banner motd # 
		2. Enter TEXT message. End with the character '#'. Unauthorized access is strictly prohibited and prosecuted to the full extent of the law. #
		3.  S1(config)# exit 
		4.  S1#
	8. De configuratie bewaren
		1. S1# copy running-config
		2.  startup-config Destination filename [startup-config]? [Enter]
		3.   Building configuration... [OK] 
		4.   S1#
	9. Tonen van de current configuratie
		1. S1# show running-config
	10. Display IOS
		1. S1# show version
	11. Display status van geconnecteeerde interfaces van de switch
		1. S1# show ip interface brief
8. Doe hetzelfde voor S2, met het andere IP adres

Appendix initializeren en reloaden van switch

1. Kijken of VLANs zijn toegevoegd op de switch
	1. Switch# show flash 
2. Deleten van VLAN file
	1. Switch# delete vlan.dat 
	2. Delete filename [vlan.dat]?
	3. Enter tweemaal
3. Cleanen van de startup config file
	1. Switch# erase startup-config
	2. Erasing the nvram filesystem will remove all configuration files! Continue? [confirm] 
	3. [OK] 
	4. Erase of nvram: complete
4. reloaden van de switch
	1. Switch# reload
	2. enter voor confirmatie
	3. Je kan prompt krijgen om running config te saven voor de reload, kies hier no
5. Bypassen van de initial config dialoog
	1. Kies hier nee


		
## Testplan labo 3, Netwerk met switch en router ##

![](http://i.gyazo.com/2223d265aa24e50f9aab4ce46970f8ca.png)

Verbinden van de Devices

1. PC-A verbinden met F0/6 van S1
2. F0/5 van S1 verbinden met G0/1 van R1
3. G0/0 van R1 verbinden met PC-B

Opmerking

----------

Indien het een router met end device is heb je crossover cable nodig, zodat communicatie mogelijk is. Hierbij volstaat een lan cable niet, dit is enkel een passthrough.

Configureren van de devices en veriferen van de connectie

IP adressen toewijzen aan de PC's 

1. PC-A IP: 192.168.1.3,  Subnet Mask: 255.255.255.0, Default gateway: 192.168.1.1
2. PC-B  IP: 192.168.0.3,  Subnet Mask: 255.255.255.0, Default gateway: 192.168.0.1
3. ping van PC-B naar PC-A
4. Voor de instructies hiervoor zie vorige labo's

Configureren van de router

1. privileged mode
	1. Router> enable
2. config mode
	1. Router# conf t
3. device name geven
	1. Router(config)# hostname R1
4. disable DNS lookup voor ongewenste lookups
	1. R1(config)# no ip domain-lookup 
5. Passwoord voor enable -> class
	1. R1(config)# enable secret class
6. cisco als console passwoord
	1. R1(config)# line con 0 
	2. R1(config-line)# password cisco 
	3. R1(config-line)# login 
	4. R1(config-line)# exit 
7. cisco als vty password + enable login
	1. R1(config)# line vty 0 4 
	2. R1(config-line)# password cisco 
	3. R1(config-line)# login 
	4. R1(config-line)# exit 

8. Encrypteren van de clear text passwoorden
	1. R1(config)# service password-encryption

9. Banner aanmaken
	1. R1(config)# banner motd #
	2.  Enter TEXT message.  End with the character '#'.   
	3.  Unauthorized access prohibited! 
	3. # 
	4. R1(config)#

10. Configureer en activeer beide interfaces van de router
	1. R1(config)# int g0/0 
	2. R1(config-if)# description Connection to PC-B. 
	3. R1(config-if)# ip address 192.168.0.1 255.255.255.0 
	4. R1(config-if)# no shut
	5. R1(config-if)# int g0/1 
	6. R1(config-if)# description Connection to S1. 
	7. R1(config-if)# ip address 192.168.1.1 255.255.255.0 
	8. R1(config-if)# no shut 
	9. R1(config-if)# exit 
	10. R1(config)# exit

11. Saven van de running config naar de startup config
	1. R1# copy running-config startup-config
	2. Enter

12. Clock zetten, zie labo 1

13. Ping van PC B naar PC A, zie labo 2

Appendix labo 2: Initializeren en reloaden van de router

1. Connecteer aan de router
	1. Router> enable
2. Erase de startup config file van de nonvolatile RAM
	1. Router# erase startup-config
	2. enter
3. Reloaden van de router
	1. Router# reload
	2. enter
	3. bij prompt kies no om de running config niet te saven
4. Bypassen van de initial config dialog
	1. Would you like to enter the initial configuration dialog? [yes/no]: no 
5. Terminaten van autoinstall programma
	1. Would you like to terminate autoinstall? [yes]: yes 

Initializen van de Switch en reloaden

Zie labo 2 voor instructies


## Testplan labo 4, Basic Static Route Configuration##

![](http://i.gyazo.com/a12abf26b5f1b9751ddb2378b3f12752.png)

Verbinden van de devices

1. PC1 met een switch verbinden
2. Switch van PC1 met Fa0/0 van R1 verbinden
3. S0/0/0 van R1 met S0/0/0 van R2 verbinden
4. S0/0/1 van R2 met S0/0/1 van R3 verbinden
5. PC2 verbinden met een switch 
6. Switch van PC2 met Fa0/0 van R2 verbinden
7. PC3 verbinden met een switch
8. Switch van PC3 met Fa0/0 van R3 verbinden

---------------------
Op de routers naar global configuration mode en configureer de basis commands: 

1. hostname
2. no ip domain-lookup
3. enable secret

zie labo 3


configureer de console en de virtual terminal line passwords op elke router

1. password
2. login

zie labo 3

voeg de logging synchronous commando toe 

1. Router(config-­line)#logging synchronous


Hierdoor gaat de console geen pushberichten sturen

*Voorbeeld van interruption*

1. R1(config)#interface fastethernet 0/0
2. R1(config-­if)#ip address 172.16.3.1 255.255.255.0
3. R1(config-­if)#no shutdown
4. R1(config-­if)#descri
5. *Mar 1 01:16:08.212: %LINK-­3-­UPDOWN: Interface FastEthernet0/0, changed state to up
6. *Mar 1 01:16:09.214: %LINEPROTO-­5-­UPDOWN: Line protocol on 8. Interface FastEthernet0/0, changed state to upption
7. R1(config-­if)#

*Na logging synchronous*

1. R1(config)#interface fastethernet 0/0
2. R1(config-­if)#ip address 172.16.3.1 255.255.255.0
3. R1(config-­if)#no shutdown
4. R1(config-­if)#description
5. *Mar 1 01:28:04.242: %LINK-­3-­UPDOWN: Interface FastEthernet0/0, changed state to up
6. *Mar 1 01:28:05.243: %LINEPROTO-­5-­UPDOWN: Line protocol on Interface
7. FastEthernet0/0, changed state to up
8. R1(config-­if)#description <-­-­ Keyboard input copied after message

Voeg logging synchronous toe aan de console en virtual terminal lines op alle routers

1. R1(config)#line console 0
2. R1(config-­line)#logging synchronous
3. R1(config-­line)#line vty 0 4
4. R1(config-­line)#logging synchronous

Voeg het exec-timeout commando toe op de console en virtual terminal lines 

Zet timer voor de sessie te terminaten wanneer er gedurende een bepaalde tijd niks gebeurt

*Voorbeeld*

1. Router(config-­line)#exec-­timeout minutes [seconds]

Voeg exec-timeout 0 0 toe aan de console en virtual terminal lines op alle routers

1. R1(config)#line console 0
2. R1(config-­line)#exec-­timeout 0 0
3. R1(config-­line)#line vty 0 4
4. R1(config-­line)#exec-­timeout 0 0

-----------------

Indien je de IP adressen op R1 geconfigureerd, verwijder dan al interface commands. 

Op R1 van de privileged exec mode, geef de debug ip routing command 

1. R1#debug ip routing
2. IP routing debugging is on

Toont welke routes worden toegevoegd, gewijzigd of verwijderd.

Ga in de interface configuratie mode van R1 zijn LAN interface.

1. R1#configure terminal
2. Enter configuration commands, one per line. End with CNTL/Z.
3. R1(config)#interface fastethernet 0/0

Configureer het IP adres 

1. R1(config-­if)#ip address 172.16.3.1 255.255.255.0
2. is_up: 0 state: 6 sub state: 1 line: 1 has_route: False

Als je nu op enter klikt zou de cisco IOS debug output je moeten informeren dat er een nieuwe route is maar deze is *false*. In andere woorden er is een nieuwe route toegevoegd aan de routing table.

Vul het juiste commando in om de route in de route table te installeren.

1. is_up: 1 state: 4 sub state: 1 line: 1 has_route: False
2. RT: add 172.16.3.0/24 via 0.0.0.0, connected metric [0/0]
3. RT: NET-­RED 172.16.3.0/24
4. RT: NET-­RED queued, Queue size 1
5. RT: interface FastEthernet0/0 added to routing table
6. %LINK-­3-­UPDOWN: Interface FastEthernet0/0, changed state to up
7. is_up: 1 state: 4 sub state: 1 line: 1 has_route: True
8. %LINEPROTO-­5-­UPDOWN: Line protocol on Interface FastEthernet0/0, chan
ged state to up
9. is_up: 1 state: 4 sub state: 1 line: 1 has_route: True
10. is_up: 1 state: 4 sub state: 1 line: 1 has_route: True

Vul een commando in om te zien of de nieuwe route in de routing table staat. 

1. R1# show ip route

Ga in de interface configuratie mode voor R1 zijn WAN interface verbonden met R2

1. R1#configure terminal
2. Enter configuration commands, one per line. End with CNTL/Z.
3. R1(config)#interface Serial 0/0/0


Configureer het IP adres

1. R1(config-­if)#ip address 172.16.2.1 255.255.255.0
2. is_up: 0 state: 0 sub state: 1 line: 0 has_route: False


Geef het clock rate commando op R1

1. R1(config-­if)#clock rate 64000
2. is_up: 0 state: 0 sub state: 1 line: 0 has_route: False

Geef het commando om zeker te zijn dat de interface is volledig geconfigureerd. 

1. R1(config-if)# no shutdown

Indien het mogelijk is ga op een andere pc voor R2 zo kun je de debug output bekijken op R1 terwijl je veranderingen maakt op R2.

1. R2#debug ip routing
2. IP routing debugging is on

1. R2#configure terminal
2. Enter configuration commands, one per line. End with CNTL/Z.
3. R2(config)#interface serial 0/0/0

Configureer IP adres

1. R2(config-­if)#ip address 172.16.2.2 255.255.255.0
2. is_up: 0 state: 6 sub state: 1 line: 0

Het commando om zeker te zijn dat de interface volledig is geconfigureerd.

1. R2(config-if)# no shutdown

Kijk of de nieuwe route in de routing table van R1 en R2 staat

1. R1# show ip route

1. R2# show ip route


Zet het debugging uit op beide routers 

1. R1(config-­if)#end
2. R1#no debug ip routing
3. IP routing debugging is off
4. Herhaal voor R2
----------------------

Vervolledig nu alle andere interfaces van R2 en R3 volgens de aansluiting met de ip adressen

-------------------

Configureren van de IP-adressen op de host PC's

1. PC1 IP-adres: 172.16.3.10/24, default gateway of 172.16.3.1
2. PC2 IP-adres: 172.16.1.10/24, default gateway of 172.16.1.1
3. PC3 IP-adres: 192.168.2.10/24, default gateway of 192.168.2.1

------------------

Controleren of alles werkt

Gebruik het Ping commando om: 
1. PC1 naar default gateway
2. PC2 naar default gateway
3. PC3 naar default gateway

Indien 1 van deze niet werkt moet je de fout zoeken

1. Router R2 ping naar R1 172.16.2.1
2. Router R2 ping naar R3 192.168.1.1

Indien 1 van deze niet werkt moet je de fout zoeken

1. PC3 naar PC1
2. PC3 naar PC2
3. PC2 naar PC1
4. Router R1 naar Router R3 

Deze gaan nog niet werken omdat routers alleen naar direct met elkaar verbonden netwerken kan communiceren, omdat ze nog niet statisch verbonden zijn.

----------

Check status van de interfaces:

1. R2# show ip interface brief

Toon de routing table voor alle 3 de routers:

1. R1# show ip route
2. R2# show ip route
3. R3# show ip route

We zien dat niet alle IP-adressen erin staan dit komt doordat de routers niet met static of dynamic routing zijn geconfigureerd. Hierdoor kennen de routers enkel de direct verbonden netwerken.

We kunnen static routes toevoegen om de overige verbindingen te maken.

--------

Gebruik de volgende syntax om static routes met de volgende hop te configureren: 

1. Router(config)# ip route network-­address subnet-­mask ip-­address

Op R3 configureer een static route naar 172.16.1.0 en maak gebruik van Serial 0/0/1 interface van R2 als volgende hop adres. 

1. R3(config)#ip route 172.16.1.0 255.255.255.0 192.168.1.2
2. R3(config)#

Met show ip route kan je zien of deze toegevoegd is

Configureer op R2 de static route om het 192.168.2.0 netwerk te bereiken

1. R2(config)#ip route 192.168.2.0 255.255.255.0 192.168.1.1
2. R2(config)#

Met show ip route kan je zien of deze toegevoegd is

Op R3 configureer de static route met de Serial 0/0/0 interface van R3 als de exit interface

1. R3(config)# ip route 172.16.2.0 255.255.255.0 Serial0/0/1
2. R3(config)#

Met show ip route kan je zien of deze toegevoegd is

Gebruik show running config om te zien dat de static routes geconfigureerd zijn op R3

1. R3#show running-­config
2. Op R2 configureer een static route 
1. R2(config)# ip route 172.16.3.0 255.255.255.0 Serial0/0/0
2. R2(config)#

Met show ip route kan je zien of deze toegevoegd is

We zien dat R2 nu naar alle netwerken kan navigeren. Dit wil niet zeggen dat de andere routers dit ook kunnen.

Ping PC2 naar PC1, hier zien we dat dit niet lukt doordat R1 geen terugroute heeft van 172.16.1.0

----------

Configureren van een Default Static Route

We gebruiken de Default Static Route om niet alles statisch te moeten ingeven over de 2 routers. Door router 1 default te maken kan hij ipv rechtstreeks naar het statische adres gewoon naar R2 sturen die dan verder doorstuurt.

Configureer R1 met een default route

1. R1(config)#ip route 0.0.0.0 0.0.0.0 172.16.2.2
2. R1(config)#

Controleer via show ip route

We zien dat we kunnen pingen nu van PC2 naar PC1  maar nog niet van PC3 naar PC1 

-----------------

Configureren van Summary Static Route op R3

In plaats van nog een statische route in te geven, en aangezien de netwerken zo dicht bij elkaar liggen kunnen we een Summary Static Route gebruiken. Hierdoor wordt de routing table niet te groot en zijn we efficiënter. 

1. R3(config)#ip route 172.16.0.0 255.255.252.0 192.168.1.2

Via show ip terug controleren

Verwijder de static routes op R3

1. R3(config)#no ip route 172.16.1.0 255.255.255.0 192.168.1.2
2. R3(config)#no ip route 172.16.2.0 255.255.255.0 Serial0/0/0

Met show ip terug controleren

Nu kunnen we normaal wel pingen van PC3 naar PC1.

----------------------

Auteurs testplan: Robby Den Haese, Siebert Timmermans


