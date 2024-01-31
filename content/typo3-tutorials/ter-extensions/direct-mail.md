+++
title = "direct_mail"
date = 2024-01-09T20:50:54+01:00
aliases = ["direct-mail.html"]
+++

Für die Extension `direct_mail` kannst du einen CronJob einrichten, damit z.B. alle 5 Minuten überprüft wird, ob ein neuer Newsletter vorhanden ist und auch gleich versendet wird.

Unter Linuxsystemen gibt es dafür den Befehl `crontab -e` mit dem man die jeweiligen CronJobs verwalten kann. Nicht jeder Provider lässt diesen Befehl zu oder erlaubt das direkte Editieren dieser Datei und ihr müsst die CronTabs über das vom Provider zur Verfügung gestellte Konfigurationstool erstellen.

In dieser Datei könnt ihr nun die Zeile eintragen, die ihr bitte aus der `direct_mail` Doku entnehmt. Überwiegend sollte hier die Zeile für eine locale-Installation interessant sein.

Normalerweise sollte das Thema CronJob nun erledigt sein, aber bei mir kamen noch ein Haufen Fehler:

```
"Bad interpreter /usr/bin/php"
```

Das kann 2 Ursachen haben. Entweder gibt es diese Datei nicht oder es liegt an dem CRLF am Ende jeder einzelnen Zeile in der Datei selbst.

Mit dem Befehl `which php` könnt ihr herausfinden welche php-Datei ausgeführt wird. Der angezeigte Pfad muss mit dem Pfad in der ersten Zeile der `dmail.crontab` übereinstimmen. Wenn nicht, bitte ändern.
Besteht der Fehler immer noch, dann wird es an dem CRLF liegen. Führt dazu bitte folgenden Befehl aus:

```
perl -e 'while (<>) {s/\r//;print}' dmailerd.cronphp> dmailerd.cronphp.neu
```

Danach könnt ihr die `dmailerd.cronphp` löschen und mit `mv dmailerd.cronphp.neu dmailer.cronphp` die neue Datei wieder in den originalen Dateinamen umbenennen. Das ist wichtig, da sonst eure Zeile im Crontab nicht mehr funktioniert.

```
"SAFE MODE restriction" blablabla
```

Je nach Server haben die Daten, die per FTP hochgeladen wurden einen anderen Besitzer als Dateien, die per Extensionmanager hochgeladen wurden. So war es bei mir der Fall, dass das `dmailerd` Script keine Berechtigung hatte auf bestimmte Dateien der `static_info_table` zuzugreifen.

Mit einem einfachen `chown -R [WWWUSER].[WWWGRUPPE] ./typo3conf/ext` war das Problem dann auch beseitigt. Den `[WWWUSER]` und die `[WWWGRUPPE]` müsst ihr selbst herausfinden, da jeder Server anders konfiguriert sein kann.

```
"another process is already running"
```

Dazu braucht ihr nur in den `./typo3temp` Ordner zu gehen und nach einer Datei zu suchen die ungefähr so heißt: `dmailerd.cronphp.lock`. Diese müsst ihr dann nur noch löschen und der cronjob sollte endlich funktionieren.
