## Testrapport Azureservers

- Is de CentOS server aangemaakt op Azure cloud?

![](https://i.gyazo.com/10746e44a5d0360fed957fa9e7917267.png)

- Is de Windows 2012 server aangemaakt op Azure cloud? 

![](https://i.gyazo.com/eb4400fe3e529dcc87a042394cc86842.png)

- Kan ik connecteren van op elke pc aan de CentOS en Windows 2012 server? 

![](https://i.gyazo.com/753c9b66f8a6fe28c73ef1d80ca9b58d.png)

![](https://i.gyazo.com/21246c6024f62d2e02c84ae1d18778c6.png)



----------

Uitvoerder(s) test: Robby en Siebert

Uitgevoerd op: 04/03/2016

## Testrapport LAMPstack

Testplan werkte volledig, enkel probleem met connecteren aan nieuwe VM aangezien de originele VM (image) met SSH ingesteld was met keys. 

Ook belangrijk om op te letten bij spelling bij aanmaak resourcegroup(netwerk) en VM.

- Is Apache geinstalleerd en kan je de status controlleren?
    
    	sudo yum clean all
    	sudo yum -y update
    	sudo yum -y install httpd 
    	sudo systemctl start httpd
    	sudo systemctl enable httpd
    	sudo systemctl status httpd
    	sudo systemctl stop httpd

Iets als dit wordt weergegeven

![](https://i.gyazo.com/40e97bffc9d7b361a04fdf8792ebf636.png)

- Is MariaDB geinstalleerd en kan je de status controlleren?

    	yum update -y
    	yum install mariadb-server
    
    	systemctl start mariadb.service
    	systemctl enable mariadb.service
    
    	systemctl is-active mariadb.service

		mysql -u root -p

Iets als dit wordt dan weergegeven

![](https://i.gyazo.com/1040e936fd14bc4824b13146c6e1cdf7.png)

- Is php geinstalleerd en kan je de status controlleren? 

		locate php
als dit veel entries toont, bvb gelinkt aan je wordpress, dan weet je dat php werkt en correct is geinstalleerd.

![](https://i.gyazo.com/f1e44580c8c5d6df601bdd1fb0654db6.png)

- Heb je de VM voorbereid om image van te nemen?

		sudo waagent -deprovision -force

Hierna via Azure CLI

    azure config mode arm
    azure vm stop –g <your-resource-group-name> -n <your-virtual-machine-name>
    azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>

- Heb je Azure CLI geïnstalleerd? 

*Vanaf hier is Azure CLI nodig, dit is te downloaden op [AzureCLIGitHub](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-install.md "Download Azure CLI")*

*Herstart je computer en open de command prompt. Login op je Azure. Instructies [hier](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-connect.md "connect Azure in CLI")*

Als Azure CLI correct is geïnstalleerd kan je door

    azure -help

controleren of het werkt.

![](https://i.gyazo.com/a35f7a048bb85e5a1ee5c7384f31e1d1.png)

- Heb je image genomen?

		azure vm capture –g <your-resource-group-name> -n <your-virtual-machine-name> -p <your-vhd-name-prefix> -t <your-template-file-name.json>

- Heb je resourcegroup aangemaakt voor het nieuwe netwerk?

		centralus = westeurope indien je in europa zit

		azure group create <your-new-resource-group-name> -l "centralus"
    
	     azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"
    
	    azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>
    
	    azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"
    
	    azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

- Heb je de nieuwe VM aangemaakt met nieuwe user en pw?

		azure group deployment create –g <your-new-resource-group-name> -n <your-new-deployment-name> -f <your-template-file-name.json>

	    info:Executing command group deployment create
	    info:Supply values for the following parameters
	    vmName: mynewvm
	    adminUserName: myadminuser
	    adminPassword: ********
	    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

Iets gelijkend aan dit wordt dan getoond

    + Initializing template configurations and parameters
    + Creating a deployment
    info:Created template deployment "dlnewdeploy"
    + Waiting for deployment to complete
    data:DeploymentName : mynewdeploy
    data:ResourceGroupName  : mynewrg
    data:ProvisioningState  : Succeeded
    data:Timestamp  : 2015-10-29T16:35:47.3419991Z
    data:Mode   : Incremental
    data:NameType  Value

- Kan je connecteren aan de nieuwe VM mbv het ip adres en een connector zoals bv. mobaXterm? 

![](https://i.gyazo.com/753c9b66f8a6fe28c73ef1d80ca9b58d.png)

- Kan je wordpress deployen op de LAMP stack?

Log in on mysql root 

	mysql -u root -p

First create a new database that WordPress can control

	CREATE DATABASE wordpress;

Create a new mysql account user 

wordpressuser = accountname
password = password

	CREATE USER wordpressuser@localhost IDENTIFIED BY 'password';

Link the user to the database

	GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED BY 'password';

Flush the privileges so that MySQL knows about the recent privilege changes that we've made:

	FLUSH PRIVILEGES;

Exit mysql

	exit

-----

Now first we need to install php --> but in this case we have it already installed. 

	sudo yum install php-gd

Restart apache

	sudo service httpd restart

Download WordPress
	
	cd ~
	wget http://wordpress.org/latest.tar.gz

> Note: if wget not installed

> sudo yum install wget

Extract the file 

	tar xzvf latest.tar.gz

Transfer the unpacked files to Apache's document root

	sudo rsync -avP ~/wordpress/ /var/www/html/

Add folder to store uploaded files

	mkdir /var/www/html/wp-content/uploads

Assign the correct ownership and permissions to the files and folder

	sudo chown -R apache:apache /var/www/html/*

---

Move to apache root directory

	cd /var/www/html


Copy the sample to the default configuration file location

	cp wp-config-sample.php wp-config.php


open the file in a texteditor

	nano wp-config.php

> Note: if nano isn't installed
> 
> sudo yum install nano


Fill in the parameters 

    // ** MySQL settings - You can get this info from your web host ** //
    /** The name of the database for WordPress */
    define('DB_NAME', 'wordpress');
    
    /** MySQL database username */
    define('DB_USER', 'wordpressuser');
    
    /** MySQL database password */
    define('DB_PASSWORD', 'password');

-----

Go in your browser to your IP-address

server_domain_name_or_IP = ip-address

	http://server_domain_name_or_IP

Fill in what WordPress wants

![](https://i.gyazo.com/5308542d55ea9a3c22b5e13255f1c1dd.png)

----------

Uitvoerder(s) test: Robby en Siebert

Uitgevoerd op: 11/03/2016

## Testrapport LAMPstack script
-  Heb je CLI? 

*Vanaf hier is Azure CLI nodig, dit is te downloaden op [AzureCLIGitHub](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-install.md "Download Azure CLI")*

*Herstart je computer en open de command prompt. Login op je Azure. Instructies [hier](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-connect.md "connect Azure in CLI")*

Als Azure CLI correct is geïnstalleerd kan je door

    azure -help

controleren of het werkt.

-  Ingelogd op Azure? 

![](https://i.gyazo.com/6ca21e0a9679c6504120b4a744c470c6.png)

-  Werkt het script? 

![](https://i.gyazo.com/30313df81af90df6a817b70aa2b56363.png)
![](https://i.gyazo.com/3384a76b8c4e3ccf2469bedb0226f906.png)
![](https://i.gyazo.com/4bd8b61d38c02b6a71c879972eba5b24.png)

-  Kan je connecteren aan de VM? 

![](https://i.gyazo.com/21246c6024f62d2e02c84ae1d18778c6.png)

-  Kan je wordpress deployen op de LAMP stack? 

Log in on mysql root 

	mysql -u root -p

First create a new database that WordPress can control

	CREATE DATABASE wordpress;

Create a new mysql account user 

wordpressuser = accountname
password = password

	CREATE USER wordpressuser@localhost IDENTIFIED BY 'password';

Link the user to the database

	GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED BY 'password';

Flush the privileges so that MySQL knows about the recent privilege changes that we've made:

	FLUSH PRIVILEGES;

Exit mysql

	exit

-----

Now first we need to install php --> but in this case we have it already installed. 

	sudo yum install php-gd

Restart apache

	sudo service httpd restart

Download WordPress
	
	cd ~
	wget http://wordpress.org/latest.tar.gz

> Note: if wget not installed

> sudo yum install wget

Extract the file 

	tar xzvf latest.tar.gz

Transfer the unpacked files to Apache's document root

	sudo rsync -avP ~/wordpress/ /var/www/html/

Add folder to store uploaded files

	mkdir /var/www/html/wp-content/uploads

Assign the correct ownership and permissions to the files and folder

	sudo chown -R apache:apache /var/www/html/*

---

Move to apache root directory

	cd /var/www/html


Copy the sample to the default configuration file location

	cp wp-config-sample.php wp-config.php


open the file in a texteditor

	nano wp-config.php

> Note: if nano isn't installed
> 
> sudo yum install nano


Fill in the parameters 

    // ** MySQL settings - You can get this info from your web host ** //
    /** The name of the database for WordPress */
    define('DB_NAME', 'wordpress');
    
    /** MySQL database username */
    define('DB_USER', 'wordpressuser');
    
    /** MySQL database password */
    define('DB_PASSWORD', 'password');

-----

Go in your browser to your IP-address

server_domain_name_or_IP = ip-address

	http://server_domain_name_or_IP

Fill in what WordPress wants

![](https://i.gyazo.com/5308542d55ea9a3c22b5e13255f1c1dd.png)

----------

Uitvoerder(s) test: Robby en Siebert

Uitgevoerd op: 04/03/2016

## Testrapport WISA stack

- Kan je connecteren met de server? 

![](https://i.gyazo.com/21246c6024f62d2e02c84ae1d18778c6.png)

- Is IIS geinstalleerd?

cmdlet PowerShell command to install all of IIS 8.5

	import-module servermanager

	add-windowsfeature web-server -includeallsubfeature

- Is MySQL of SQLServer geinstalleerd (of zit het al bij je OS)?

In onze test zit het in het OS ingewerkt

- Is ASP.net geinstalleerd?

ASP.NET through CMD

1. Make sure you are running as admin in cmd

1. At the command prompt, type the following: dism /online /enable-feature /featurename:netfx3

1. Wait for the command to complete. It could take several minutes.

1. Close the command prompt window.

![](https://i.gyazo.com/34cd3e21fc018e18c09ee741ddcb34b8.png)

- Kan je bij localhost het IIS startscherm zien? Ja

![](https://i.gyazo.com/c9639649fc6d0eb9ff25618660b3f972.png)
----------

Auteurs testplan: Robby Den Haese, Siebert Timmermans

## Testrapport WISA stack script
- Heb je Azure CLI voor PowerShell?

*Vanaf hier is Azure CLI nodig, dit is te downloaden op [AzureCLIGitHub](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-install.md "Download Azure CLI")*

*Herstart je computer en open de command prompt. Login op je Azure. Instructies [hier](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-connect.md "connect Azure in CLI")*

Als Azure CLI correct is geïnstalleerd kan je door

    azure -help

controleren of het werkt.


- Ben je ingelogd op Azure via je PowerShell? Ja

![](https://i.gyazo.com/9b633f5a2f59a7c76d9c12e0972eb66f.png)

- Is de PowerShell directory aangemaakt en is het script hierin geplaatst? Ja

![](https://i.gyazo.com/b6979eee45a150898ae3296f59b941e6.png)

- Is het certificaat gedownload en is dit hierin geplaatst? Ja

![](https://i.gyazo.com/509ba332e41873910d6644eefaa6e7c7.png)

- Zijn deze modules geimplementeerd? Ja

![](https://i.gyazo.com/830669cc1d1f98271ec4d0bdbe647ef0.png)

- Krijg je geen errors bij runnen van script? Neen

![](https://i.gyazo.com/fe110fdca12322ee0bce17161b106046.png)

- Kan je connecteren met de server? 

![](https://i.gyazo.com/21246c6024f62d2e02c84ae1d18778c6.png)

- Is ASP.net geinstalleerd? Ja

![](https://i.gyazo.com/34cd3e21fc018e18c09ee741ddcb34b8.png)

- Kan je bij localhost het IIS startscherm zien?

![](https://i.gyazo.com/c9639649fc6d0eb9ff25618660b3f972.png)

----------

Uitvoerder(s) test: Robby en Siebert

Uitgevoerd op: 10/03/2016

### Testplan JavaEEStack


- Is Tomcat8 geïnstalleerd en kan je de status controleren?

		yum install wget
		cd /opt
		wget http://www-eu.apache.org/dist/tomcat/tomcat-8/v8.0.32/bin/apache-tomcat-8.0.32.tar.gz
		tar xzf apache-tomcat-8.0.32.tar.gz
		echo "export CATALINA_HOME=\"/opt/apache-tomcat-8.0.32\"" >> ~/.bashrc
		source ~/.bashrc
		cd /opt/apache-tomcat-8.0.32
		./bin/startup.sh

		tail -f logs/catalina.out

Iets als dit wordt weegegeven:

![](https://i.gyazo.com/85057b691b25ddcd422bf13f4ad632d5.png)


- Is MariaDB geïnstalleerd en kan je de status controleren? 

    	yum update -y
    	yum install mariadb-server
    
    	systemctl start mariadb.service
    	systemctl enable mariadb.service
    
    	systemctl is-active mariadb.service

		mysql -u root -p

Iets als dit wordt dan weergegeven

![](https://i.gyazo.com/1040e936fd14bc4824b13146c6e1cdf7.png)

- Is Java OpenJDK geïnstalleerd en kan je de status controleren?

		yum install java-1.8.0
		java -version

Iets als dit wordt weergegeven

![](https://i.gyazo.com/0e63a379a88b078a78583fbfdb0ef4c4.png)

- Heb je de VM voorbereid om image van te nemen?

		sudo waagent -deprovision -force

Hierna via Azure CLI

    azure config mode arm
    azure vm stop –g <your-resource-group-name> -n <your-virtual-machine-name>
    azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>

- Heb je Azure CLI geïnstalleerd? 

*Vanaf hier is Azure CLI nodig, dit is te downloaden op [AzureCLIGitHub](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-install.md "Download Azure CLI")*

*Herstart je computer en open de command prompt. Login op je Azure. Instructies [hier](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-connect.md "connect Azure in CLI")*

Als Azure CLI correct is geïnstalleerd kan je door

    azure -help

controleren of het werkt.

![](https://i.gyazo.com/a35f7a048bb85e5a1ee5c7384f31e1d1.png)

- Heb je image genomen?

		azure vm capture –g <your-resource-group-name> -n <your-virtual-machine-name> -p <your-vhd-name-prefix> -t <your-template-file-name.json>

- Heb je resourcegroup aangemaakt voor het nieuwe netwerk?

		centralus = westeurope indien je in europa zit

		azure group create <your-new-resource-group-name> -l "centralus"
    
	     azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"
    
	    azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>
    
	    azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"
    
	    azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

- Heb je de nieuwe VM aangemaakt met nieuwe user en pw?

		azure group deployment create –g <your-new-resource-group-name> -n <your-new-deployment-name> -f <your-template-file-name.json>

	    info:Executing command group deployment create
	    info:Supply values for the following parameters
	    vmName: mynewvm
	    adminUserName: myadminuser
	    adminPassword: ********
	    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

Iets gelijkend aan dit wordt dan getoond

    + Initializing template configurations and parameters
    + Creating a deployment
    info:Created template deployment "dlnewdeploy"
    + Waiting for deployment to complete
    data:DeploymentName : mynewdeploy
    data:ResourceGroupName  : mynewrg
    data:ProvisioningState  : Succeeded
    data:Timestamp  : 2015-10-29T16:35:47.3419991Z
    data:Mode   : Incremental
    data:NameType  Value

- Kan je connecteren aan de nieuwe VM mbv het ip adres en een connector zoals bv. mobaXterm? 

![](https://i.gyazo.com/1e0b6992e4064aecbf99e6e392a0792e.png)  

### Testplan JavaEEStack script

-  Heb je CLI? 

*Vanaf hier is Azure CLI nodig, dit is te downloaden op [AzureCLIGitHub](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-install.md "Download Azure CLI")*

*Herstart je computer en open de command prompt. Login op je Azure. Instructies [hier](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-connect.md "connect Azure in CLI")*

Als Azure CLI correct is geïnstalleerd kan je door

    azure -help

controleren of het werkt.

-  Ingelogd op Azure? 

![](https://i.gyazo.com/6ca21e0a9679c6504120b4a744c470c6.png)

-  Werkt het script? Merk op dat dit hetzelfde is als LampStack, enkel veranderen we hier $jsonName = 'JavaEEStack.json'

![](https://i.gyazo.com/30313df81af90df6a817b70aa2b56363.png)
![](https://i.gyazo.com/3384a76b8c4e3ccf2469bedb0226f906.png)
![](https://i.gyazo.com/4bd8b61d38c02b6a71c879972eba5b24.png)

-  Kan je connecteren aan de VM? Via ssh verbinding naar het toegewezen adres van Azure, in dit geval 40.118.26.167

![](https://i.gyazo.com/12fa079661d77b77f978f1875739a3af.png)


----------

Uitvoerder(s) test: Davy De Cock en Mike Lobbezoo

Uitgevoerd op: 06/03/2016

