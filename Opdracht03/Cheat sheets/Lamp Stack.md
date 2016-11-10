Lamp stack opzetten 
------

#OPMERKING#

In de VM die gaat gebruiken als image mag je GEEN SSH instellen met public en private keys. Dit wordt onthouden wanneer je de image achteraf gebruikt, waardoor eerst een SSH reset nodig is via Azure CLI.


Creating Apache
----- 

	sudo yum clean all
	sudo yum -y update
	sudo yum -y install httpd 

Er is geen firewall precies dus hier geen rekening mee houden

Apache start 

	sudo systemctl start httpd
	sudo systemctl enable httpd
	sudo systemctl status httpd
	sudo systemctl stop httpd

Opmerking : voor systemctl moet je root zijn 

	sudo -s

miss later vn toepassing ( niet zeker):

	/usr/bin/mysql_secure_installation


MariaDB installeren
----

	yum update -y
	yum install mariadb-server

Verifieren dat mariaDb is opertioneel

	systemctl start mariadb.service
	systemctl enable mariadb.service

kijken of alles aan het lopen is

	systemctl is-active mariadb.service

Na installeren doe: 

	mysql -u root -p

geef passwoord als dat nodig is en je zou iets in deze aard moeten zien:

	Welcome to the MariaDB monitor.  Commands end with ; or \g.
	Your MariaDB connection id is XXXX
	Server version: 5.5.X


	Copyright (c) 2000, 2014, Oracle, Monty Program Ab and others.


	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.


	MariaDB [(none)]> 

voor password:

	mysql_secure_installation

PHP installeren
-----

	yum install php php-mysql php-gd php-pear -y


Image maken met Azure
------

	sudo waagent -deprovision -force

dit maakt de image zo general mogelijk

Azure CLI
------

*Vanaf hier is Azure CLI nodig, dit is te downloaden op [AzureCLIGitHub](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-install.md "Download Azure CLI")*

*Herstart je computer en open de command prompt. Login op je Azure. Instructies [hier](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-connect.md "connect Azure in CLI")*

Zorg ervoor dat je in resource manager mode zit

	azure config mode arm

Stop de VM die reeds gedeprovisioned is

	azure vm stop –g <your-resource-group-name> -n <your-virtual-machine-name>

Generalizeer de VM

	azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>

Neem nu de image en een local file template

	azure vm capture –g <your-resource-group-name> -n <your-virtual-machine-name> -p <your-vhd-name-prefix> -t <your-template-file-name.json>

Om de image te dupliceren hebben we nu eerst een netwerkconfiguratie nodig. We doen dit door een nieuwe resourceGroup aan te maken. 

ResourceGroup voor nieuw netwerk aanmaken
----

centralus = westeurope indien je in europa zit

	azure group create <your-new-resource-group-name> -l "centralus"
    
    azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"
    
    azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>
    
    azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"
    
    azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"


Om de NIC te verkrijgen

	azure network nic show <your-new-resource-group-name> <your-nic-name>

Deployment van de nieuwe VM
----

Via de json en resource groep kan je nu een nieuwe VM aanmaken

	azure group deployment create –g <your-new-resource-group-name> -n <your-new-deployment-name> -f <your-template-file-name.json>

Hierna wordt gevraagd om een user met pw aan te maken, geef ook de NIC mee die we opgevraagd hadden

**Opmerking**: paswoord moet volgende bevatten (6-72 character, uppercase character, lowercase character, number or special character) 

    info:Executing command group deployment create
    info:Supply values for the following parameters
    vmName: mynewvm
    adminUserName: myadminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

Iets gelijkend aan dit zou moeten getoond worden

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
    
    
    data:------------------  ------------  -------------------------------------
    
    data:vmName  Stringmynewvm
    
    
    data:vmSize  StringStandard_D1
    
    
    data:adminUserName   Stringmyadminuser
    
    
    data:adminPassword   SecureString  undefined
    
    
    data:networkInterfaceId  String/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
    info:group deployment create command OK

Verifieer of de creatie gelukt is door te connecteren via SSH naar het IP-adres

Om het IP-adres op te vragen

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>


Script
-----------

###Prerequisites
* You own an Azure subscription and you are logged in into the Azure CLI. If you don't have the Azure CLI installed, check
[AzureCLIGitHub](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-install.md "Download Azure CLI")

* If you don't know how to login to azure, check [this](https://github.com/Azure/azure-content/blob/master/articles/xplat-cli-connect.md "connect Azure in CLI").

Don't forget to restart your computer after the installation, afterwards you can work with the Azure CLI in both cmd and PowerShell.

###Script code

	#Variables:
	$newResourceGroup = ""
	$zone = ""
    $vnetName = "" 
    $subnetName = ""
    $ipName = ""  
    $nicName = ""
    $deploymentName = ""
    $jsonName = "  .json"    
    
    #Creation of new ResourceGroup which contains the network settings
    azure group create $newResourceGroup -l "$zone"
    
    #Creation of vnet for the network    
    azure network vnet create $newResourceGroup $vnetName -l "$zone"
    
    #Creation of subnet for the network
    azure network vnet subnet create $newResourceGroup $vnetName $subnetName -a 10.0.0.0/8
    
    #Creation of ip for the network   
    azure network public-ip create $newResourceGroup $ipName -l "$zone"

    #Creation of nic for the network
    azure network nic create $newResourceGroup $nicName -k $subnetName -m $vnetName -p $ipName -l "$zone"
    
    #Creation of the Virtual Machine with resourcegroup(network) deploymentname, and json image as parameters
    azure group deployment create –g $newResourceGroup -n $deploymentName -f $jsonName -g $newResourceGroup
    
Choose your network configuration by filling in the variables, after that, simply run this code in to your CLI

You will be prompted to enter your new VMname, AdminUser and AdminPW

Warning: password must have (6-72 characters, uppercase character, lowercase character, number or special character)

If you want to use this script as a batch file, open it up in notepad and save as "scriptname.bat"



Install WordPress
--------

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


-----
Source: [GitHub Azure Linux VM Image Capturing](https://github.com/Azure/azure-content/blob/master/articles/virtual-machines/virtual-machines-linux-capture-image-resource-manager.md)

Source: [Installation WordPress](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-centos-7 "Installation WordPress")

Authors: Robby en Siebert



