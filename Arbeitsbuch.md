# Use-Case
Die Firma TBZLight ist ein zukunftsorientiertes Unternehmen, erkennt die Notwendigkeit einer zuverlässigen und effizienten Backup Speicherung für das Management ihres firmeninternen Minecraft-Servers. Der Minecraft-Server spielt eine entscheidende Rolle als Plattform für kreative Zusammenarbeit und fördert die Teamkultur innerhalb der Firma. Im zusammenhang mit dem neuen Open workring space wird der Sever der Gruppenbildung weiter helfen. Angesichts dieser Bedeutung steht die Firma vor der Herausforderung, nicht nur die Ressourcen des Servers optimal zu nutzen, sondern auch vor unerwarteten Datenverlusten geschützt zu sein.

Der Einsatz unseres speziellen Minecraft Server-Backup-Systems präsentiert sich als unverzichtbare Lösung für TechCraft Solutions


## Umgebungs Visualisierung 

![Alt text](<Netzwerk Plan.png>)

Der Inhalt meines auftrages besteht darin, einen Backup Server für meinen Minecraft Server zu erstellen. Das Konzept wird auf Multipass ausgeführt. Die Backupdaten, also der Minecraft Spielstand wird mittels Duplicati auf dem Mega.nz gespeichert. Docker habe ich als zusatz genommen, damit ich die Daten Redundant speichern kann und es nicht nur im .NZ ist.

## VM via Multipass erstellen

Zuerst muss man Multipass installieren und alles vorbereiten. 

![Alt text](<Step 1,2-1.png>)

Danach kann ich sagen, was meine Multipass VM für eigenschaften haben sollte. Danach als die VM erstellt werden konnte, habe ich diese dierekt mal gestartet.
```yaml
multipass launch -c2 -d 70G -m 4G -n m143 docker 
```
```yaml
multipass.exe shell m143
```

-c2 = anzahl Kernel der VM \
-d 70G = Grösse der Virtuelen Festplatte \
-m 4G = Anzahl Gb Ram von VM \
-n m143 = Name der VM \
docker = Docker soll auf der VM noch installiert werden

# Docker vorbereiten

Bevor ich irgend etwas mit Docker wirkich anfange, wollte ich zuerst ein *sudo arp-get update* machen um alles ausgeführte zu aktuallisieren um auf dem neusten Stand zu sein. 

![Alt text](<Step 4 .png>)

Nachdem alles aktuallisiert hat, installiere ich noch zusätzliche Paktete für Docker. Zum einen hol ich mir die benötigten Zertifikate und dazu noch die KEYS 
```yaml
sudo apt-get install ca-certificates curl gnupg
```
```yaml
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

![Alt text](<Step 5,6.png>)


Bei dem nächsten befehl musste man das Repository erstellen und es zu der apt source (liste) hinzufügen. 
```yaml
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

![Alt text](<Step 7.png>)

Jetzt noch diese Plugins installieren und dann ist man fast fertig mit der installation. Diese Zusatzprogramme werden wir noch brauchen. 
```yaml
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
![Alt text](<Step 8-2.png>)

Um sicher zu gehen, dass alles nötige funktioniert hat und richtig installiert wurde, kann man noch diesen Befehl ausführen(Optional):
```yaml
Docker --help
```

Der letzte schritt der installierung ist es die Ordner für die Daten zu erstellen, die in der Datenbank dan schlussendlich gespeichert werden.

```yaml
mkdir minecraft_data 

mkdir -p duplicati/appdata

mkdir -p duplicati/backups

mkdir -p duplicati/source
```
Dublicati



Wenn du dein Docker jetzt starten willst, musst du noch das Compose File mit den Konfigs von Duplicati und dem Minecraft Server erstellen. Die Anleitung wie ich dies gemacht habe wird hier verlinkt. Genauso kann man sich einfach das NANO file von mir kopieren. 

https://docs.linuxserver.io/images/docker-duplicati/#usage

```yaml
version: "3.7"
services:
  mc:
    image: itzg/minecraft-server
    tty: true
    stdin_open: true
    ports:
      - "25565:25565"
    environment:
      EULA: "TRUE"
    volumes:
      - /home/ubuntu/minecraft_data:/data
  duplicati:
    image: lscr.io/linuxserver/duplicati:latest
    container_name: duplicati
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /home/ubuntu/duplicati/appdata/config:/config
      - /home/ubuntu/duplicati/backups:/backups
      - /home/ubuntu/duplicati/source:/source
      - /home/ubuntu/minecraft_data:/minecraft_data
    ports:
      - 8200:8200
    restart: unless-stopped
```
Nachdem du das alles installiert hast, solltest du bereit sein, Jetzt Docker zu starten.

## Debug in Vorbereitung

Wäred des aufsetzten, sties ich auf ein paar probleme, die mir die Installation von Docker nicht gerade erleichtert hat. Um den Aufbau der Doku trotzdem flüsslig zu halten, werde ich in diesem Absatz mich auf das Debugging beziehn und aufzeigen wie etwas gelöst habe, dass nicht funktionierte.

Als ich das erste mal probiert habe, den Docker zu starten bekam ich einen Fehler. Bei der Installation, hatte es das Server.jar file nicht heruntergeladen.
![Alt text](<Doku Debug.png>)

Als erstes habe ich alle Docker Files gelöscht und nochmal installiert. Dies funktionierte jedoch wieder nicht und ich bekam eine Fehlermeldung. 

Der richtige Lösungsansatz war es, wieder alles zu löschen.
Jetzt musste ich selber das File Server.jar von hand installieren. Nachdem ich die Docker Files wieder installiert habe, hat die automatische Installation den Server.jar teil übersprungen, da das file schon existierte. Danach funktionierte alles wieder
## Docker Starten

Um Docker zum ersten mal zu starten, füge diesen code ein:

```yaml
sudo docker compose up -d
```
*-d = Um Docker im hintergrund laufen zu lassen*

Wenn alles geklappt hat, sollte dein Screen jetzt so aus sehen 

![Alt text](<Sudo compose up.png>)

Danach führe *Multipass list* aus um die IP von M143 zu sehen. Speichere dir diese IP. Danach gehe zu deinem Compose file und **kopiere von ganz unten den Port**. Danach gehst du auf safari und verbindest dich mit diesen angaben. 

![Alt text](<Duplicati IP.png>)


## Speicherort einrichten MEGA.NZ

Nun um unsere minecraft Daten auch speichern zu können, brauchen wir dafür einen Speicherort. Ich habe mich persönlich für mega.nz entschieden, da es am umkompliziertesten ist. 

Erstelle einen Backup ordner und bennene ihn *minecraft_backup*


![Alt text](image-2.png)

Jetzt musst du deine Cloud nur noch mit deinem Backup system verbinden so das die Server Daten alle auf dem Mega.nz Ordner gespeichert werden. Gehe dafür auf Duplicati und füge eine sicherung hinzu. Fülle nun die Felder so wie im gezeigten Bilder aus. 

![Alt text](<Password Duplicati backup-1.png>)

![Alt text](<Verbindung zu mega.nz.png>)

Jetzt musst du nurnoch den richtigen Ordner wählen, wo das gespeichert werden sollte, und dann kannst du gelich mal den Server in Betrieb nehmen und testen ob es funktioniert. 

![Alt text](image-3.png)

## Minecraft Server starten und Testen

Mache wieder *Sudo docker compose up -d* und melde dich bei Minecraft an. Und verbinnde dich auf einen Server. Gib nun als Server IP deine IP ein, die du schon bei Duplicati benutzt hast.

## Beweise das es Funktioniert

Um zu zeigen, dass es den Spielinhalt wirklich gespeichert hat, kann ich auf meinen Mega Ordner gehen und sehen ob sich dort Dateien befinden.

Wie gut auf dem Bild zu erkennen ist, hat Duplicati automatisch die Spielinhalte gespeichert, so wie erhofft. 

![Alt text](<Server Daten.png>)

Auch habe ich dies noch In-game getestet. Ich habe in Minecraft einen Block gebaut. Danach habe ich mich aus und wieder eingeloggt um zu sehen ob er noch dort steht. Und ja er war noch da, was hies der Server hat ein Backup gemacht.

