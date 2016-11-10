Java EE stack opzetten 
------

##OPMERKING

In de VM die gaat gebruiken als image mag je GEEN SSH instellen met public en private keys. Dit wordt onthouden wanneer je de image achteraf gebruikt, waardoor eerst een SSH reset nodig is via Azure CLI.

Opmerking : voor root geef je het volgende commando in: 

	sudo -s


MariaDB installeren
----

	yum update -y
	yum install mariadb-server

Verifieren dat mariaDB is opertioneel

	systemctl start mariadb.service
	systemctl enable mariadb.service

kijken of alles aan het lopen is

	systemctl is-active mariadb.service

password instellen voor mariaDB:

	mysql_secure_installation

Na installeren doe: 

	mysql -u root -p

geef passwoord en je zou iets in deze aard moeten zien:

	Welcome to the MariaDB monitor.  Commands end with ; or \g.
	Your MariaDB connection id is XXXX
	Server version: 5.5.X


	Copyright (c) 2000, 2014, Oracle, Monty Program Ab and others.


	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.


	MariaDB [(none)]> 



Java (OpenJDK 1.8.0) installeren
-----

	yum install java-1.8.0

Java installatie controle + versie (Tomcat 8 requires java SE7 of hoger) 
	
	java -version

Tomcat 8 installeren
-----
Installeren van wget (dit is nodig voor tomcat te downloaden via internet)
	
	yum install wget

Navigeer naar /opt
	
	cd /opt

Downloaden van Tomcat 8 - core versie (http://tomcat.apache.org/download-80.cgi)

	wget http://www-eu.apache.org/dist/tomcat/tomcat-8/v8.0.32/bin/apache-tomcat-8.0.32.tar.gz

Uitpakken van .tar.gz file

	tar xzf apache-tomcat-8.0.32.tar.gz


Configureer de Tomcat root (CATALINA_HOME variable) in het CentOs systeem met de volgende 2 commando's

	echo "export CATALINA_HOME=\"/opt/apache-tomcat-8.0.32\"" >> ~/.bashrc
	
	source ~/.bashrc

Starten van Tomcat 8:

	cd /opt/apache-tomcat-8.0.32
	./bin/startup.sh

Enkele commando's om te testen of Tomcat online is:

	wget http://localhost:8080
    
	tail -f logs/catalina.out
	**output Server startup in xxx ms**



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



-----------
Source: [GitHub Azure Linux VM Image Capturing](https://github.com/Azure/azure-content/blob/master/articles/virtual-machines/virtual-machines-linux-capture-image-resource-manager.md)

Auteurs: Mike Lobbezoo en Davy De Cock
