# Cheat sheet Opdracht 1: Archiveringssysteem #

----------

Als referentie gebruiken wij [deze site.](http://lifeandshell.com/installing-jenkins-on-centos-7/)

## Installatie Jenkins ##
    yum install -y net-tools
    yum update && yum upgrade
    yum install -y wget
    sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
    sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
    sudo yum install -y jenkins
    sudo yum install -y java
    sudo service jenkins start of stop of restart
    sudo chkconfig jenkins on
    yum install firewalld
    firewall-cmd --zone=public --add-port=8080/tcp --permanent
    Wanneer je problemen zou hebben met dit commando, kan je best eerst de machine eens heropstarten met dit commando:
    shutdown -r now
    
Navigeer naar je browser en typ ip:8080 of dns naam:8080

## Autobuilding ##

1. Navigeer naar je Jenkins webinterface
2. Voeg de git en github extensies toe via de webinterface (manage jenkins -> manage plugins) herstart hierna Jenkins
3. Pas de plug ins aan (manage jenkins -> git -> pad naar Git Bash)
4. Voeg users toe (Settings -> GitHub -> add credentials)
5. Jenkins job met autobuilding maken -> create new job, voeg git repository link toe en vink build when change is pushed aan.

## Jenkins met GitHub connecteren ##
1. Zorg dat je 8080 poort open staat, Jenkins luistert hier namelijk naar.
2. Installeer githubAuthentication plugin
3. Configure Jenkins -> Configure system -> GitHub -> Advanced -> Manage additional GitHub actions -> Convert login & password to token
4. Blauwe sleutel -> Create token -> Apply -> refresh browser
5. Selecteer credentials bij github optie
6. Ga naar GitHub Settings van je repository en add de webhook Jenkins (Github Plugin)
7. Geef je publiek IP:8080 mee of je DNS naam:8080

## Jenkins GitHub Authenticatie ##
1. Navigeer naar Global Security Configuration via je webinterface
2. Enable security
3. Vul gegevens in naar eigen wens, client ID en Client Secret valt te vinden bij je GitHub applicatie
4. Geef de nodige rechten aan de users