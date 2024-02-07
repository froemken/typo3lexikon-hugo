+++
title = "Installation"
date = 2024-01-06T19:11:24+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["installation.html"]
+++

## TYPO3 installieren und einrichten

Damit TYPO3 auf Eurem Server läuft, muss Euer Webserver ein paar Bedingungen erfüllen. So müssen PHP und eine MySQL-Datenbank in bestimmten minimalen Versionen zur Verfügung stehen. Schaut Euch dazu auch mal die Datei INSTALL.md an, die ich für diese Dokumentation als Basis verwendet habe.

## Anforderungen an den Server

Damit TYPO3 auf Eurem Hostingpaket läuft, muss dieser eine PHP-Umgebung und eine MySQL-Datenbank anbieten. Folgende Minimalanforderungen für TYPO3 7 LTS entnehme ich der INSTALL.md, die den TYPO3-Sourcen beigefügt ist:

- Webserver mit installierter PHP-Umgebung (Apache, Nginx, IIS und andere)
- PHP-Version von 5.5 bis 7
- MySQL von 5.5 bis 5.6 oder eine andere kompatible Datenbank wie z.B. MariaDB. Der strict-Modus darf nicht aktiv sein.
- Mehr als 200 MB freier Webspeicherplatz

## PHP-Umgebung

Es wird ein `memory_limit` von mindestens 64 MB gefordert. Ich selbst empfehle hier mindestens 100 MB. Auf meiner Entwicklungsumgebung habe ich sogar 256 MB zugewiesen.

Die `max_execution_time` sollte mindestens 30 Sekunden betragen. Empfohlen werden 240 Sekunden. Jedoch entstand dieser hohe Wert nur auf Grund des Extension Managers, der für das Abholen der TYPO3-Extensions aus dem TER schon mal etwas länger brauchen kann. Für den Produktiveinsatz sollten 60 Sekunden völlig ausreichend sein.

Der Wert register_globals muss deaktiviert sein.

## Erforderliche PHP-Erweiterungen

Die folgenden Erweiterungen sind normalerweise in den PHP Standardpaketen bereits vorhanden:

- fileinfo
- filter
- hash
- openssl
- pcre >= 8.30
- session
- soap
- SPL
- standard
- xml
- zip
- zlib

Je nach PHP-Paket könnte es sein, dass Ihr folgende Erweiterungen separat installieren müsst:

- gd
- json
- mysqli

## TYPO3 herunterladen

Die erste Anlaufstelle ist der Downloadbereich von TYPO3. Hier werden Euch die aktuellsten Versionen zur Verfügung gestellt, die aktiv mit Sicherheitsupdates versorgt werden. Ladet Euch die gewünschte Version herunter und ladet sie dann mit Hilfe von SFTP, FTPS oder auch SCP in Eurer Hostingpaket wieder hoch. Achtet beim Upload bitte auf eine sichere Verbindung, damit Eure Passwörter verschlüsselt übertragen werden können. Über 80 % der gehackten Webseiten basieren auf Ausspähen von Passwörtern ungesicherter FTP-Verbindungen.

Falls Euer Hostingpaket einen SSH-Zugang zur Verfügung stellt, empfehle ich den Download der TYPO3-Sourcen mit Hilfe von wget oder auch curl. Schaut mal auf https://get.typo3.org/ vorbei. Die aktuellste TYPO3-Version könnt Ihr dann mit folgendem Befehl direkt auf dem Server herunterladen:

```shell
wget get.typo3.org/current --content-disposition
```

## TYPO3 entpacken

Meldet Euch auf Eurem Server mit Hilfe einer SSH-Verbindung an. Falls eine solche Verbindung nicht zur Verfügung steht, müsst Ihr TYPO3 lokal auf Eurem Rechner entpacken und alle Datei auf den Server hochladen (das kann schon mal was dauern).

Entpackt TYPO3 CMS eine Ebene höher Eures DocumentRoots:

```shell
/var/www/site/htdocs/ $ cd ../var/www/site/ $ tar xzf typo3_src-7.6.x.tar.gz
```

Auf diese Weise kann TYPO3 auch für mehrere Webauftritte verwendet werden.

## Einrichten einer TYPO3-Instanz

Wechselt nun zurück in Euer DocumentRoot und erstellt die 3 benötigten SymLinks:

```shell
cd htdocsln -s ../typo3_src-7.6.x typo3_srcln -s typo3_src/index.phpln -s typo3_src/typo3
```

Je nach Hoster können keine SymLinks auf Dateien erzeugt werden. Im Falle der index.php könnt Ihr diese auch aus dem typo3_src-Verzeichnis in Euer DocumentRoot kopieren.

## Du nutzt den Apache?

Sofern Dein Hoster den Apache als Webserver einsetzt solltest Du noch die .htaccess-Datei aktivieren. Rufe dazu folgendes Kommando auf:

```shell
cp typo3_src/_.htaccess .htaccess
```

## Der Erstaufruf

Rufe nun die frisch eingerichtete Webseite mit Deinem Lieblingsbrowser auf. Dadurch, dass noch keine Konfiguration vorliegt, leitet Dich TYPO3 sofort an den Installationsassisstenten weiter. Hier wirst Du nun Step by Step durch den weiteren Installatiosprozess geleitet.Nach Abschluss des Assisstenten wurde eine Konfigurationsdatei geschrieben, die Du unter typo3conf/LocalConfiguration.php findest. Allein durch die Existenz dieser Datei kannst Du den Assisstenten nicht mehr aufrufen und ladest bei dem Versuch direkt im Installtool.

## TYPO3 Schritt für Schritt

### Schritt 1: Die Datenbankanbindung

Tragt hier die Zugangsdaten zu der Datenbank ein. Nach einem Klick auf Weiter wird zunächst überprüft, ob TYPO3 eine Verbindung zur Datenbank herstellen kann. Wenn nicht, dann habt Ihr immer noch die Möglichkeit die Daten noch einmal einzutragen bzw. zu korrigieren. Wenn die Überprüfung erfolgreich war landet Ihr bei Schritt 2.

### Schritt 2: Eine Datenbank anlegen/auswählen

Bei den meisten Hostern werdet Ihr wohl über das Konfigurationstool Eures Hosters eine Datenbank für TYPO3 anlegen müssen. Bitte achtet darauf, dass TYPO3 eine eigene leere Datenbank benötigt, ansonsten kann es Euch passieren, dass nach einem Klick auf "COMPARE DATABASE" alle TYPO3 fremden Tabellen weg sind.

Falls Ihr Herr über Euren eigenen Webserver seid und mit dem zuvor angegebenen Benutzernamen Datenbanken erstellen dürft, dann könnt Ihr hier in dieser Maske die Datenbank auch direkt anlegen lassen.

### Schritt 3: Was soll installiert werden?

In diesem Schritt steht Ihr vor der Wahl, was installiert werden soll:

#### Blank Package

Nach Auswahl dieser Option erhaltet Ihr ein absolut leeres TYPO3-System. Hier ist keine Seite eingerichtet, kein Template vorhanden, keine Frontendbenutzer. Einfach nichts. Wenn Ihr Eure eigene Webseite erstellen wollt ist dies die richtige Anlaufstelle.

#### Introduction Package

Nach Auswahl dieser Option erhaltet Ihr ein komplett fertig eingerichtetes TYPO3-System. Wie oben bereits geschildert sind Seiten, Templates und vieles mehr schon eingerichtet und Ihr könnt TYPO3 sofort in Aktion erleben. Für Personen, die TYPO3 erst kennen lernen wollen genau die richtige Wahl. Ich als Entwickler wähle diese Option sehr häufig, um z.B. Extensions zu testen und zu entwickeln.

### Schritt 4: Farbe und Passwort

Der Step von Schritt 3 zu Schritt 4 kann je nach System schon mal bis zu 40 Sekunden benötigen. Also nicht gleich den Browser schließen, sondern ruhig mal ein Minütchen warten.

Schritt 4 erwartet ein Passwort. Dieses Passwort gilt sowohl für den Zugang zum Installtool, als auch für den Login zum TYPO3-Backend. Das Benutzername für's Backend ist: admin.

Es muss nicht immer Orange sein. Deshalb gibt es unten einen Farbwähler, mit dem Ihr die von TYPO3 eingerichtete Beispielwebseite einfärben könnt. Mit Hilfe des senkrechten Balkens rechts neben der Farbpalette könnt Ihr die Helligkeit der Farbpalette einstellen.

Danach könnt Ihr Euch die Webseite im Frontend anzeigen lassen. Um ins Backend zu kommen müsst Ihr ein "/typo3" hinten an Eure Domain hängen.
