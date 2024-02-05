+++
title = "scheduler"
date = 2024-01-09T20:35:39+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["scheduler.html"]
+++

Die System-Extension `scheduler` listet alle im System registrierten Tasks auf und kann diese entsprechend starten, bearbeiten, löschen und den aktuellen Status des jeweiligen Tasks wiedergeben.

In diesem Tutorial will ich euch zeigen, wie ihr selbst einen Task anlegen könnt. Als Beispiel habe ich mir einen einfachen Pinger ausgedacht, der jede viertel Stunde überprüft, ob ein Server im Internet noch erreichbar ist.

Wer mehr über die Entstehung der Extension `scheduler` erfahren will, sollte mal bei [typo3blogger](https://typo3blogger.de/vom-standalone-cli-zum-scheduler/) vorbeischauen.

Eine englische Dokumentation ist in der Extension selbst vorhanden.

## Scheduler installieren

Die Extension ist ab TYPO3 4.3.0 schon im Core enthalten und muss nur noch über den Extensionmanager aktiviert werden. Der `scheduler` kommt mit 2 Extension Einstellungen:

Maximum lifetime
: Falls ein Task mal länger dauern sollte oder "abstürzt". Wie lange soll der Task noch laufen bevor er beenden wird und damit nicht mahr als "Running" in der Liste der Tasks erscheint.

Enable logging
: Der Standardwert ist, dass es für jeden Start und Stop eines Tasks einen Eintrag im Backend-Log gibt.

## Installation überprüfen

In der linken Navigation befindet sich nun ein neuer Menüpunkt `Planer`. In der Selectbox könnt ihr nun auf `Konfiguration prüfen` wechseln und erhaltet eine Warnung dafür, dass der Planer noch nie ausgeführt wurde, eine Fehlermeldung, dass der User `_cli_scheduler` nicht existiert und eine OK-Meldung darüber, dass der Webuser Script ausführen darf.

### Last run (letzte Ausführung)

Da wir die Extension frisch installiert haben sollte jedem klar sein, warum hier eine Warnung erscheint, dass der Planer noch nie einen Task ausgeführt hat. Laut engl. Doku kann die Warnmeldung einfach ignoriert werden.

### Backend-Benutzer für den Planer

Wenn ihr im Backend eingeloggt seid und einen Task manuell startet, dann wird der aktuelle BE-User dazu verwendet. Damit ihr euch aber nicht jedes Mal einloggen müsst, um einen Task zu starten, benötigen wir einen nur-so-da-Backend-Benutzer, unter dem dann der Task ausgeführt werden kann. Dieser User ist fest vorgegeben und heißt:

`_cli_scheduler`

Dieser User benötigt keine speziellen Rechte es sei denn ihr bzw. eure Tasks müssen auf die `TCEmain` zugreifen, dann muss auch dieser User dieses Recht erhalten.

Legt also jetzt diesen User an und dann weiter zum Server

## Server einrichten

Das wird schon etwas schwieriger, denn wir benötigen einen SSH-Zugang zu unserem Server. Solche Zugänge werden meist aber nur von teureren Webpaketen angeboten. Als Alternative könnt ihr es auch mal mit der phpshell versuchen. Das ist zwar kein echter Shellzugriff, aber damit könnt ihr zumindest mal versuchen einen Cronjob anzulegen. Einige Provider bieten auch eine Cronjob-Verwaltung im Hauseigenen Verwaltungstool an wie z.B. jweiland.de. Dort können Cronjobs sehr einfach eingerichtet, bearbeitet und gestartet werden.

Damals musste für jeden Task, der ausgeführt werden sollte, ein solcher Cronjob-Eintrag auf dem Server eingerichtet werden. Dank des Systems hinter scheduler brauchen wir jetzt nur noch einen globalen Cronjob einzurichten und können dann aber beliebig viele Tasks in TYPO3 selbst anlegen, die dann alle auf den globalen Cronjob zugreifen.

## PHP Version abfragen

Der einzurichtende Befehl ist ein PHP-Script. Wir müssen herausfinden wo sich unsere php.exe/php/php5-Datei befindet. Unter Linux/Unix kann dieser Befehl helfen:

```shell
which php
```

oder eben

```shell
which php5
```

In meinem Fall lautete die Antwort:

```shell
/usr/bin/php5
```

## Cronjob einrichten

Unter Linux werden Cronjobs mit folgendem Befehl bearbeitet:

```shell
crontab -e
```

Dieser Befehl erstellt für den aktuellen Benutzer Cronjobs. Ich würde hier empfehlen, diesen Cronjob für den www-user einzurichten.

Wir müssen uns nun Gedanken darüber machen, was wir für Tasks im scheduler einrichten wollen, denn es macht keinen Sinn den Cronjob auf 5 Minuten einzustellen, wenn wir, wie in unserem Falle, nur einen Task ausführen lassen wollen, der nur alle 15 Minuten gestartet wird.

In der englischen Dokumentation wird folgendes Scenario beschrieben: Stellen Sie sich vor Sie haben ein paar Tasks, die jede viertel Stunde und andere Tasks, die alle 10 Minuten ausgeführt werden sollen. Dann sollten Sie den Cronjob nicht auf alle 10 Minuten, sondern auf alle 5 Minuten stellen. Warum? Bei einem 10 Minuten Rhythmus haben wir folgende Ausführzeiten:

```
8:00 - 8:10 - 8:20 - 8:30
```

Alle Tasks, die für den 15-Minuten-Rhythmus konfiguriert wurden, werden nun 5 Minuten später ausgeführt, da es für 8:15 kein Intervall gab. Deshalb wird hier ein 5-Minuten-Intervall vorgeschlagen.

In unserem Fall reicht ein Cronjob mit einem Intervall von 15 Minuten völlig aus:

```shell
*/15 * * * * /usr/bin/php5 /var/www/cms/typo3/cli_dispatch.phpsh scheduler
```

Hier seht ihr auch endlich warum wir den php-Pfad benötigten.

Für Windows-Server gibt es einen eigenen Abschnitt in der englischen Originaldokumentation.

## Der erste Test

Die Extension scheduler bringt zwei Test-Tasks mit. Zum Testen müssen wir nun im Backend unter `Planer` per Selectbox auf `Geplante Tasks` wechseln. Hier können wir mit `Task hinzufügen` einen neuen Task erstellen. Alle Tasks haben mindestens folgende Settings:

Deaktivieren
: Haken rein und der Task ist deaktiviert.

Klasse
: Hier könnt ihr auswählen, welchen Task ihr einrichten wollt.

Typ
: Soll der Task nur einmal zu einem bestimmten Termin oder "wiederkehrend" an Hand eines Intervalls gestartet werden.

Start/Stop/Häufigkeit
: Je nach Typ erscheinen diese Felder, die eigentlich selbsterklärend sein sollten.

ALLE Felder, die ich hier nicht aufgelistet sind, gehören zum Task selbst. So gibt es bei dem einen Task ein zusätzliches E-Mail-Feld, weil Dieser mit jeder Ausführung eine E-Mail sendet und der Schlafen-Task hat ein eigenes Feld für wie lange er schlafen soll. Auch in unserer Extension werde ich zeigen, wie ihr eigene Felder Eurem Task hinzufügen könnt.

Nach einem Klick auf Speichern landet ihr wieder bei der Auflistung in der Euer erstellter Tasks nun auch erscheinen sollte.
