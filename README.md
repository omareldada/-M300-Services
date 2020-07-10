 M300-Services

## Omar El Dada

### Lernthemen
### Linux
### Virtualisierung
### Vagrant
	Vagrant up: startet VM's aus Vagrantfile
	Vagrant halt: stopt VM's
	Vagrant destroy: zerstört VM's
	Vagrant ssh [vmname]: Verbindet per ssh auf VM
### Versionsverwaltung / GIT
	git add -A : Änderungen im aktuellen Verzeichnis zum Zwischenspeicher hinzufügen
	git commit : Änderungen auf dem lokalen Git speichern
	git push : Lokales Git auf Github synchronisieren
	git clone : Github Repo herunterladen
	git init : Repo initialisieren
	
## Vagrant File bearbeiten
Anschliessend kann das Vagrant File nach Belieben verändert werden. Ich entschied mich für die Vagrant Cloud Box **generic/ubuntu1804**, da es sich bei dieser Version um eine reativ neue handelt. Zu empfehlen wäre ebenso die Box **generic/ubuntu1810**.

Die Linie sollte dementsprechend wie folgt aussehen:

```
config.vm.box = "generic/ubuntu1804"
```
&#160;

Da unsere VM direkt vom LAN erreichbar sein sollte (Bridged), muss die folgende Linie auskommentiert werden:

```
#config.vm.network "public_network"
```
&#160;

Zusätzlich kann dann noch eine statishce IP, sowie das gebdridge Interface ausgewählt werden:

```
config.vm.network "public_network", ip: "10.71.13.4"
```
&#160;

Da all unsere Befehle in einer separten Bash Skript geschrieben werden, muss dieses im Vagrant File definiert werden. Dieses Skript wird dann per SSH ins Guest System hochgeladen und ausgeführt.

```
config.vm.provision "shell", path: "bootstrap.sh"
```
&#160;	

## Bash Skript erstellen
Damit die VM weis, um welche Sprache es sich bei dem Skript handelt, muss in der ersten Linie des Skripts `#!/bin/bash` stehen. Gefolgt von `sudo su`, denn so muss nicht vor jedem Befehl manuell als SuperUser (`sudo`) ausgeführt werden.

&#160;

## VM erstellen
Die virtuelle Maschiene wird dem Befehl ``vagrant up`` erstellt. Hier zu ist es wichtig, dass das Vagrant File im betreffenden Ordner vorhanden ist, und zugleich keine Fehler aufweist. Um das Vagrant File nach Fehler zu überprüfen, kann der Befehl ``vagrant validate`` angewendet werden.

## Sicherheit

### Firewall (UFW)
UFW steht für "Uncomplicated Firewall" und ist Heutzutage in vielen Linux / Unix Distros die vorinstallierte Firewall. 
Die Firewall des Opache Servers lässt nur die Ports 22 und 1194 zu. Die Ports 80 und 443 sind geschlossen, da diese nur vom internen VPN aus erreicht werden können. Alle restlichen Ports werden von der Firewall blockiert. Ist ist also grundsätzlich sinnlos zu versuchen  mit einem anderen Port aud die FW zuzugreifen.


### Lokale Berechtigungen
Damit die Verwaltung auf dem Opache Server einfacher und zugleich sicherer wird, wurde ein zusätzlicher benutzer erstellt. Nur dieser (und der Root) darf sicherheitsrelevante Daten wie die Schlüssel und co. anschauen und bearbeiten. Ebenfalls ist er der einzige User, welcher Apache SSL Certs erstellen und verwalten darf.

Hier lässts sich anmerken, dass alle Zertifikate in der Regel auf einem dazu dedizierten Server verwaltet werden. Dieser Server sollte strengstens beobachtet werden und die Zertifikate und notwendigen Schlüssel sollten nur über einen sicheren ssh Tunnel an die Server geschickt werden (z.B, per SCP)

### OpenVPN (Verschlüsselungen etc.) 
Obwohl OpenVPN als relativ sicher gilt, ist dies stark von der Konfiguration von OpenVPN abhängig. Standardmässig benutzt OpenVPB "BL-CBC" als Verschlüsselungsalgorythmus. Da dieser als sehr veraltet gilt und zusätzlich schon geknackt wurde, empfielt es sich AES mit einer akzeptablen Bit Länge zu verwenden (256Bit oder mehr). Ich benutzte hierfür AES-256-GCM.

Ebenso benutzt OpenVPN SHA1 als Hashwert. Auch bei diesem empfiehlt es sich die Bit lange um ein vielfaches zu erhöhen. Ich entschied ich mich hierfür für SHA512.

Damit der Kanal, über den die Zertifikate ausgetauscht werden, zusätzlich gesichert wird, wird ein PSK (Pre-shared-key) TLS Key verwendet.

## Mark Down
## Systemsicherheit
	Vagrant ssh
## Lernschritte
	Momentan habe ich einiges über Vagrant gelernt. Ich finde es momentan einfach einbisschen schwierig durchzukommen.
	Meistens bin ich hintendrinnen und muss mich beeilen.