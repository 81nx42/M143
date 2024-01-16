# Konzept

![Alt text](<Netzwerk Plan.png>)

Der Inhalt meines auftrages besteht darin, einen Backup Server für meinen Minecraft Server zu erstellen. Das Konzept wird auf Multipass ausgeführt. Die Backupdaten, also der Minecraft Spielstand wird mittels Duplicati auf dem Mega.nz gespeichert. Docker habe ich als zusatz genommen, damit ich die Daten Redundant speichern kann und es nicht nur im .NZ ist. 

## VM via Multipass erstellen

Zuerst muss man Multipass installieren und alles vorbereiten. 

![Alt text](<Step 1,2-1.png>)

Danach kann ich sagen, was meine Multipass VM für eigenschaften haben sollte. Danach als die VM erstellt werden konnte, habe ich diese dierekt mal gestartet.

*multipass launch -c2 -d 70G -m 4G -n m143 docker* 

*multipass.exe shell m143*


-c2 = anzahl Kernel der VM \
-d 70G = Grösse der Virtuelen Festplatte \
-m 4G = Anzahl Gb Ram von VM \
-n m143 = Name der VM \
docker = Docker soll auf der VM noch installiert werden

# Docker 

Bevor ich irgend etwas mit Docker wirkich anfange, wollte ich zuerst ein *sudo arp-get update* machen um alles ausgeführte zu aktuallisieren um auf dem neusten Stand zu sein. 

![Alt text](<Step 4 .png>)

Nachdem alles aktuallisiert hat, installiere ich noch zusätzliche Paktete für Docker. Zum einen hol ich mir die benötigten Zertifikate und dazu noch die KEYS 
*sudo apt*

![Alt text](<Step 5,6.png>)



