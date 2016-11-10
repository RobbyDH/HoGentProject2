#WISA stack setup
------

IIS 8.5 through PowerShell
----------

cmdlet PowerShell command to install all of IIS 8.5

	import-module servermanager

	add-windowsfeature web-server -includeallsubfeature

*note: you must be logged in as administrator to run the cmdlet*

If you want management tools for roles and features, add the IncludeManagementTools parameter to the code snippet

SQL Server
----------
Seeing it's not easily possible to install SQL Server or MySQL through PowerShell, we decided to use the Azure image of Windows Server 2012 R2 with SQL Server 2016 preinstalled

This will also make our scripting process more efficiënt

ASP.NET through CMD
----------
1. Make sure you are running as admin in cmd

1. At the command prompt, type the following: dism /online /enable-feature /featurename:netfx3

1. Wait for the command to complete. It could take several minutes.

1. Close the command prompt window.

#WISA stack script
------

This script guides you through your VM creation in your PowerShell

## Prerequisites ##
For this script to work, you need to;

1. Be logged in to Azure, see "Logging in to Azure" if you need help
1. mkdir C:\Users\<youruser>\Documents\WindowsPowerShell\Modules -> store your modules here
2. Make a map into this folder for your module and in this folder, save your script with the same name, save it as a .psm1
3. Import the module through Import-Module <name of your module> in PowerShell
4. In your modules folder, make another folder for the certificate and paste the certificate in there. Import this module like shown in step 3

The certificate is available for download here; [WindowsRMCertificatePowerShell](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)

Note: the scripts can not always be imported if your settings do not allow it.
See [ExecutionPolicies](https://technet.microsoft.com/en-us/library/ee176961.aspx "ExecutionPolicies") for more.

###Logging in to Azure###

To log in to Azure, follow the template below

Get-AzurePublishSettingsFile will forward you to a download, download it and import to the correct path

    PS C:\WINDOWS\system32> Get-AzurePublishSettingsFile
    PS C:\WINDOWS\system32> Import-AzurePublishSettingsFile "C:\Users\<youruser>\Downloads\Azure Pass-3-9-2016-credentials.publishsettings"
    
    
    Id  : 873fba73-a414-4861-bf40-6158d5fafc09
    Name: Azure Pass
    Environment : AzureCloud
    Account : F61E7766AEC39DF95F12A8DCA91773097D640DE9
    State   :
    Properties  : {[Default, True]}



    PS C:\WINDOWS\system32> Select-AzureSubscription -SubscriptionName "Azure Pass"
    PS C:\WINDOWS\system32>

## The script ##

This script is made so that it guides you throughout the creation. Call the Script by typing the name of it. then type a "-" and press tab to see the possibilities. By continually doing this you will set up your VM like you want it.

It's also possible to simply press enter after calling the script, but it won't be possible to tab between possibilities then.

The code should be made clear by the comments in case you want to adapt something to your liking.

    function Create-WISA
    {
    Param
    (
        # The name that will be given to your storage account
        [Parameter(Mandatory=$true, HelpMessage='What is the storage name?')]
        [String]$StorageName,
        # The name of the subscription you want to use, if you're not sure what your subscription to use consult the portal or run the 'Get-AzureSubscription' cmdlet.
        [Parameter(Mandatory=$true, HelpMessage='What is your subscription name?')]
        [String]$SubscriptionName,
        # The location where the service will be hosted
        [Parameter(Mandatory=$true, HelpMessage='Specify the location for example: Japan West (hint: use tab)')]
        [ValidateSet('West Europe','Central US','East US','East US 2','US Gov Iowa','US Gov Virginia','North Central US','South Central US','West US','North Europe','East Asia','Southeast Asia','Japan East','Japan West','Brazil South','Australia East','Australia Southeast','Central India','South India','West India')]
        [String]$Location,
                
        # The built-in administrator account name.
        [Parameter(Mandatory=$true, HelpMessage='Specify the administrators username')]
        [String]$adminUserName,
        # The built-in administrator password.
        [Parameter(Mandatory=$true, HelpMessage='Specify the administrators password')]
        [String]$adminPassword,
        # The name of the virtual machine.
        [Parameter(Mandatory=$true, HelpMessage='Specify the name of the Virtual Machine')]
        [String]$VirtualMachineName,
        # The name of the service on which the virtual machine will run.
        [Parameter(Mandatory=$true, HelpMessage='Specify a new or existing service name')]
        [String]$ServiceName,
        # The size of the machine. The larger you make this, the larger the costs will be. This can be changed later.
        # If you install the extensions you can consult a performance check to see if you should downscale to reduce costs.
        [Parameter(Mandatory=$true, HelpMessage='Specify the size of the Virtual Machine')]
        [ValidateSet('ExtraSmall','Small','Medium','Large','ExtraLarge','A5','A6','A7','A8','A9','Basic_A0','Basic_A1','Basic_A2','Basic_A3','Basic_A4','Standard_D1','Standard_D2','Standard_D3','Standard_D4','Standard_D11','Standard_D12','Standard_D13','Standard_D14')]
        [String]$Size


    )

    Begin
    {
    }
    Process
    {

        
        
        # These variables don't change often hence why they are made static. 
        # When another mayor version releases it's very likely that you'll have to edit these.

        #The windows version from which we'll be taking the most recent version
        #[string]$WindowsVersion = “SQL Server 2016 CTP3.3 Evaluation on Windows Server 2012 R2”
            
		#Control whether or not storage exists, if not it creates it
        $CreateStorage = Get-AzureStorageAccount |Where-Object {$_.StorageAccountName -eq $StorageName}
        if($CreateStorage -eq $null){New-AzureStorageAccount -StorageAccountName $StorageName -Location $Location}
        Set-AzureSubscription -SubscriptionName $SubscriptionName -CurrentStorageAccountName $StorageName

        #Creating the VM
        #$WindowsImage = Get-AzureVMImage | where { $_.ImageFamily -eq $WindowsVersion } | sort -Property PublishedDate -Descending
        $WindowsImage = "fb83b3509582419d99629ce476bcb5c8__SQL-Server-20140SP1-12.0.4100.1-Ent-ENU-Win2012R2-cy15su05"
		
		#VMCreator with all parameters
        $VirtualMachineCreator = New-AzureVMConfig -Name $VirtualMachineName -InstanceSize $Size -ImageName $WindowsImage `
        | Add-AzureProvisioningConfig -Windows -AdminUsername $adminUserName -Password $adminPassword `
        | New-AzureVM -ServiceName $ServiceName

        #Starting the VM
        Start-AzureVM -ServiceName $ServiceName -Name $VirtualMachineName 
        
        #Install the WinRMCertificate this module has to be imported. If you dont know how read the manual.
        Install-WinRMCertificateForVM -ServiceName $ServiceName -Name $VirtualMachineName


        #Preparing the remote session to the VM
        :outer while ($true)
        {
            if ((Get-AzureVM -ServiceName $ServiceName -Name $VirtualMachineName).Status -eq "ReadyRole")
            {
                $Uri = Get-AzureWinRMUri -ServiceName $ServiceName -Name $VirtualMachineName 

                $SecurePassword = ConvertTo-SecureString $adminPassword -AsPlainText -Force
                $AdminCredentials = New-Object System.Management.Automation.PSCredential($adminUserName, $SecurePassword)

                $session = New-PSSession -ConnectionUri $Uri -Credential $AdminCredentials

                break :outer
            }

            "Waiting until the VirtualMachine is Ready"
            sleep (10)
        }



  
        :outer while ($true)
        {
            if ((Get-AzureVM -ServiceName $ServiceName -Name $VirtualMachineName).Status -eq "ReadyRole")
            {
            $ScriptBlockContent = {
			# IIS installation code block
			import-module servermanager
			add-windowsfeature web-server -includeallsubfeature
			
			# ASP.NET installation
			Get-WindowsFeature *asp* | Install-WindowsFeature
			
			

               }
                Invoke-Command -Session $session -ScriptBlock $ScriptBlockContent

                break :outer

            

            
            }
            "Waiting until the VirtualMachine is Ready"
            sleep (20)
        }
		}


    
    End
    {
    }
    }


----
Authors: Robby and Siebert
