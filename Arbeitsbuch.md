# Konzept

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

## Docker starten