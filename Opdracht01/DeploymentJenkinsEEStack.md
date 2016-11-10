# Hoe applicatie Deployen van github -> Jenkins -> JavaEEStack Server #

----------

Als referentie gebruiken wij een project dat online te vinden is op: [DemoJenkinsEEDeploy](https://github.com/Dafken/DemoJenkinsEEDeploy.git)

## Voorbereiding ##
1. Volg de configuratie / cheatsheet, zodat je hier geen problemen meer hebt met bepaalde instellingen
2. JavaEEStack server moet up and running zijn, zodat jenkins de mogelijkheid heeft om de applicatie te deployen. In dit geval is dit [JavaEEStackServer](213.119.235.111:805)
3. Je moet een extra rolename toevoegen bij tomcat-users.xml welke te vinden is in tomcat-apache / conf folder.

        <user username="admin" password="admin" roles="manager-script" />
4. Nadat al deze stappen klaar zijn mag je niet vergeten volgende plug-in te installeren: [Deploy Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Deploy+Plugin)

Als je deze stappen doornomen hebt ben je klaar om Jenkins te configureren voor het gebruik van de applicatie.

## Jenkins job ##
1. Nadat alle verwijzingen en dergelijke goed ingegesteld zijn bij de voorbereiding, gaan we bij ons jenkins job bepaalde aspecten toevoegen. Als eerste gaan we bij de build sectie klikken op "Add build step". Hierna gaan we gebruik maken van de optie "Invoke top-level Maven Targets". Nu gaan we bij het veldje Goals volgende intypen:

        clean package
    Dit gaat ervoor zorgen dat alle vorige restanten verwijderd worden, en de code opnieuw compiled wordt
2. Als volgende stap gaan we een extra "Post-build action" toevoegen. Deze keer maken we gebruik van "Publish JUnit test result report". Het project zal automatisch unit tests runnen en genereert de XML test reporten in een folder genaamd "surefire-reports". Vandaar dat we het volgende ingeven bij "Test report XMLs" veld:

        **/target/surefire-reports/*.xml
    Merk op dat we gebruik maken van tweemaal een **, dit is een best practice zodat de configuratie robuuster wordt. Met andere woorden: Jenkins heeft de mogelijkheid om de directory te vinden, onafhankelijk van de manier hoe we Jenkins configured hebben om de source code te runnen.
3. Opnieuw gaan we gebruik maken van de "Post-build action", maar deze keer voegen we een "Archive the artifacts" toe. Dit gaat ervoor zorgen dat Jenkins een copy stored van de build die we maken, wat ons later toelaat deze te downloaden van de build result pagina. Om dit te bereiken maken we gebruik van het volgende:
    
        **/target/*.war
    Dit gaat een .war file storen op onze Jenkins welke later kan deployed worden op JavaEEStackServer.
4. Nu al deze stappen doorlopen zijn is het tijd om de instellingen te overlopen welke ervoor zorgen dat het project automatisch deployed wordt naar een JavaEEStackServer naar keuze. Opnieuw voegen we een "Post-build action" toe. Deze keer gaan we gebruik maken van "Deploy war/ear to a container". Als eerste gaan we de specifiek meegeven om welk soort file het gaat en waar deze te vinden is.
    WAR/EAR files

        **/*.war
    Context path
    
        DemoJavaEEStack
    Dit gaat ervoor zorgen dat we op onze JavaEEStackServer naar DemoJavaEEStack kunnen surfen via de tomcat pagina. Met andere woorden in dit geval is dit 213.119.235.111:805/DemoJavaEEStack.
  
    Containers
    Manager user name
    
        admin
  
    Manager password
    
        admin
  
    Tomcat URL
    
        http://213.119.235.111:805

    Manager User name en password zijn de credentials van de tomcat-user welke we bij de voorbereiding aangemaakt hebben. In dit geval:
    
        <user username="admin" password="admin" roles="manager-script" />

5. Nadat alles configured is mogen we de Jenkins job afsluiten door te klikken op Save. Als alles goed gaat, wordt deze automatisch gebuild vanaf als er iets aangepast wordt op github en daarna direct gedeployed op onze JavaEEStackServer.

Auteur: Davy De Cock / Mike Lobbezoo
