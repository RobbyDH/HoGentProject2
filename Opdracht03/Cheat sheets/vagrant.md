Vagrant Cheatsheet 
------
##Opmerking
Alvorens je begint met de vagrant installatie, installeer de virtualbox Guest Additions.

Installeren van Vagrant
------
Navigeer naar volgende link en selecteer je besturingssysteem.
[https://www.vagrantup.com/downloads.html](https://www.vagrantup.com/downloads.html "Vagrant downloaden")


Configureren van VirtualHardware
----
* Disable audio
* Disable USB
* Ensure Network Adapter 1 is set to NAT
* Add this port-forwarding rule: [Name: SSH, Protocol: TCP, Host IP: blank, Host Port: 2222, Guest IP: blank, Guest Port: 22]

Configureren van je virtueel systeem
----
Maak een gebruiker aan met naam "vagrant" en stel het password in: 
	
	useradd vagrant
	passwd vagrant 

pas de vagrant config file aan zodat vagrant toegang krijgt tot sudo, zonder dat het password prompt telkens opnieuw verschijnt

	visudo

voeg de vagrant gebruiker toe in de 

	vagrant ALL=(ALL) NOPASSWD:ALL

om te testen of het gelukt is kan je gebruik maken van volgend commando
	
	sudo pwd

als hij het root directory print zonder het password prompt dan weet je dat het geslaagd is.


Installeren van de vagrant key
-----------

Om de vagrant commando's te laten communiceren met de server via een ssh verbinding maken we gebruik van een vagrant key. Met volgende commando's kun je deze klaarzetten:

	mkdir -p /home/vagrant/.ssh
	chmod 0700 /home/vagrant/.ssh
	wget --no-check-certificate \ https:/?raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub \ -O /home/vagrant/.ssh/authorized_keys
	chmod 0600 /home/vagrant/.ssh/authorized_keys
	chown -R vagrant /home/vagrant/.ssh

Installeren en configureren van de OpenSSH Server
-------

Eerst installeren we de OpenSSH server:

	yum install openssh-server

Vervolgens gaan we de config file van de ssh server aanpassen:
	
	sudo vi /etc/ssh/sshd_config

Haal volgende lijn uit commentaar:

	AuthorizedKeysFile %h/.ssh/authorized_keys

Restart de OpenSSH server:

	sudo service sshd restart

Vagrant .box file maken + testen
-----

Als eerste is het de bedoeling om de VirtualBox machine te compresseren en het maken van een metadata.json file

	vagrant package --base JavaEEStack

Vagrant gaat de virtuele machines controleren op basis van de naam JavaEEStack en een SSH verbinding maken met de machine:

	→ vagrant package --base JavaEEStack
	[JavaEEStack] Attempting graceful shutdown of VM...
	[JavaEEStack] Forcing shutdown of VM...
	[JavaEEStack] Clearing any previously set forwarded ports...
	[JavaEEStack] Exporting VM...
	[JavaEEStack] Compressing package to: /Users/tbird/code/personal/virtual_boxes/package.box

Om de vagrant .box te testen voeren we volgende commando's uit, welke een virtuele machine gaan herstellen en vervolgens up and running zetten:
	
	vagrant box add JavaEEStackVagrant package.box
	vagrant init JavaEEStackVagrant
	vagrant up

Connecteer met de machine gemaakt door vagrant:

	vagrant ssh
_______________

Source: Vagrant: Up and Running -- Mitchell Hashimoto

Source: [Creating a Custom Box from Scratch](http://www.skoblenick.com/vagrant/creating-a-custom-box-from-scratch/) -- Ryan Skoblenick

Auteurs: Mike Lobbezoo en Davy De Cock
