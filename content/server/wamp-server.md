+++
title = "Installation WAMP Server"
linkTitle = "WAMP-Server"
date = 2024-01-09T20:59:18+01:00
slug = "wamp-server"
aliases = ["wamp-server.html"]
+++

Warum einen WAMP-Server selbst aufbauen? Ganz einfach, weil der WAMP-Server von `apachefriends` nicht für den produktiven Einsatz verwendet werden soll.

Warum nicht ein LAMP-Server, wenn dieser produktiv eingesetzt werden soll? In meinem Fall muss ich mit exe-Dateien arbeiten. Ich hab's unter Linux versucht und mithilfe von WINE ließen sie sich nicht starten.

Warum diese Dokumentation? Weil ich ein Glückspilz bin, der ein Anziehungspunkt für Fehlermeldungen ist. Es gibt keine Dokumentation, die so detailliert auf evtl. auftretende Fehlermeldungen eingeht, wie diese hier. Hinzu kommt, dass diese Doku alle empfohlenen Features/Bedingungen enthält, die in der INSTALL.txt des TYPO3-Paketes enthalten sind.

Ich habe die Doku so aufgebaut, dass der Server nach jedem Kapitel immer noch lauffähig sein sollte. Wenn ihr also alle Schritte durchgeht, kann es durchaus sein, dass ihr ein und dieselbe Datei bis zu 4mal editieren müsst.

## Apache

Für den [Apache](https://httpd.apache.org/download.cgi) gibt es drei Downloadmöglichkeiten:
- Win32 Source (Für alle, die den Apache unter Windows selbst kompilieren wollen.)
- Win32 Binary without crypto (Webserver OHNE https-Unterstützung.)
- Win32 Binary including OpenSSL (Meine Empfehlung: Für alle, die neben der einfachen Installation auch noch mit dem https-Protokoll arbeiten wollen. phpMyAdmin meckert zum Beispiel, wenn https nicht vorhanden ist.)

Während der Installation werdet ihr nach der Domäne, dem Server und der Admin-Mail gefragt. Für eine lokale Testumgebung tragt ihr bei "Domäne" und "Server": `localhost` ein. Bei der E-Mail-Adresse würde ich dann aber schon eine öffentliche E-Mail-Adresse angeben. Weiter geht's mit der typical-Installation.

Nach dem Start des Apache erhalte ich eine Fehlermeldung in der `logs/error.log`:

```shell
httpd.exe: Could not reliably determine the server's fully qualified domain name
```

Da wir `localhost` in die Setuproutine eingetragen haben, wird die Zeile

```ini
#ServerName localhost:80
```

in der `conf/httpd.conf` nicht automatisch auskommentiert. Das müsst ihr bitte händisch nachholen:

```ini
ServerName localhost:80
```

Laut `INSTALL.txt` von TYPO3 benötigen wir noch die beiden Apache-Module `mod_rewrite` und `mod_expires`. Diese beiden sind im Apache bereits enthalten und müssen nur noch aktiviert werden. Wir bleiben also in unserer `httpd.conf` und suchen nach diesen beiden Zeilen und entfernen die Raute `#` am Anfang jeder Zeile:

```ini
LoadModule expires_module modules/mod_expires.so
LoadModule rewrite_module modules/mod_rewrite.so
```

Damit `mod_rewrite` funktionieren kann, muss der Apache die Möglichkeit haben, auf die jeweiligen `.htaccess` Dateien eines jeden Verzeichnisses zugreifen zu können. Aus Performancegründen (Ja, das Auslesen und Suchen von `.htaccess` Dateien in jedem Verzeichnis dauert länger, als wenn ihr die Settings direkt in der `httpd.conf` hinterlegen würdet.) wurde per Default das Auslesen der `.htaccess` Dateien erstmal deaktiviert. Wir suchen nun in unserer `httpd.conf` nach dem Block, der mit dieser Zeile anfängt:

```ini
<Directory "C:/Programme/Apache Software Foundation/Apache2.2/htdocs">
```

Innerhalb diesen Blocks gibt es eine Zeile mit `AllowOverride None`. Diese Zeile bitte wie in der `INSTALL.txt` gewünscht abändern:

```ini
AllowOverride Indexes FileInfo
```

Ohne diese Zeile würden Extensions wie `EXT:realurl` nicht funktionieren.

Nach dieser Änderung muss der Apacheserver neu gestartet werden.

{{% notice style="info" title="Hinweis" icon="info" %}}
Wenn der Apache nicht starten will und euch nichtssagende Fehlermeldungen ausspuckt, dann startet den Apache mal über das Startmenü -> Apache -> HTTP-Server 2.2 -> Control Apache Server -> Start. Denn falls ein Fehler auftauchen sollte, dann erscheint diese nun für 30 Sekunden und verschwindet nicht wieder sofort.
{{% /notice %}}

## PHP

Auch wenn in der PHP Dokumentation steht:

> The installer will currently configure IIS, Apache, Xitami, and Sambar Server; if you are using a different web server you'll need to configure it manually.

Es hat bei mir einfach nicht geklappt. Der Installer hat PHP nicht in die bestehende Apache-Konfiguration eingebunden. Weiterhin hat mir die jeweils mitgelieferte `php5.dll` nach der manuellen Einbindung nur Fehler im Error-Log des Apache rein geschreiben. Der Apache ließ sich danach nicht mehr starten und damit habe ich für mich entschlossen, die Installation manuell mithilfe der ZIP-Datei zu realisieren.

Nur im Bereich der manuellen Installation (und im Downloadbereich selbst) erfahrt ihr welche PHP-Installationsdatei ihr für welchen Server herunterladen müsst:

- VC6 -> Apache
- VC9 -> IIS

- Jetzt bleibt aber immer noch die Frage offen, ob nun `Thread safe` oder `non thread safe`. Ich hab beide Versionen ausprobiert und erhalte bei der `Non thread safe` Version auch wieder nur Fehlermeldung im Error-Log des Apache. Außerdem beinhaltet diese Version nicht diese `php5apache2.dll`, wie sie in all den anderen Onlinedokumentationen erwähnt wird. Die gesuchte DLL-Datei findet ihr nur in diesem Paket:

> VC6 x86 Thread Safe (ZIP)

Diese Zip im Verzeichnis `C:\Programme\php` entpacken und in die `htdocs.conf` Setupdatei des Apache (`C:\Programme\Apache Software Foundation\Apache2.2\conf`) Folgendes eintragen:

```ini
LoadModule php5_module "c:/Programme/php/php5apache2_2.dll"
AddHandler application/x-httpd-php .php

# configure the path to php.ini
PHPIniDir "C:/Programme/php"
```

PHP ist nun zwar grundsätzlich aktiviert, aber wenn ihr ein Verzeichnis aufruft in der sich eine `index.php` befindet, wird diese nicht direkt ausgeführt wie vielleicht erwartet. Dazu müsst ihr in der `httpd.conf` folgende Zeilen ausfindig machen:

```ini
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
```

und entsprechend abändern:

```ini
<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule>
```

## php.ini

Standardmäßig gibt es im PHP-Verzeichnis keine `php.ini`. Dafür aber 2 `php.ini` Vorlagen:

- php.ini-development
- php.ini-production

Entscheidet also selbst, ob ihr ein Testsystem oder eine Produktivumgebung aufbauen wollt und benennt die entsprechende Datei in `php.ini` um. Bei der Produktivumgebung ist aber zu bedenken, dass die Option `display_errors` ausgeschaltet ist. Bei Fehlern erhaltet ihr so nur noch eine weiße Seite.

Auf meiner Testumgebung konnte das Introductionpackage von TYPO3 nicht installiert werden, da die `max_execution_time` von 30 nicht ausgereicht hat und das, obwohl ich hier mit einem 3GHz DualCore arbeite und 4 GB RAM. Ändert den Wert in der `php.ini` also auf 60:

```ini
max_execution_time = 60
```

Seit PHP 5.3.* gibt es nun eine Fehlermeldung, wenn die Zeitzone in der `php.ini` nicht oder falsch gesetzt wurde:

> Warning: phpinfo(): It is not safe to rely on the system's timezone settings. You are *required* to use the date.timezone setting or the date_default_timezone_set() function. In case you used any of those methods and you are still getting this warning, you most likely misspelled the timezone identifier.

In der Datei `php.ini` muss folgende Zeile

```ini
;date.timezone =
```

folgendermaßen geändert werden

```ini
date.timezone = "Europe/Berlin"
```

## OpenSSL

OpenSSL eine einfache Möglichkeit, um selbst zertifizierte Zertifikate auszustellen. Normalerweise werden Zertifikate von vertrauenswürdigen Zertifizierungsstellen ausgestellt, die der Browser kennt und abfragen kann. Dieser Weg ist bestimmt der Bessere, aber er kostet Geld und für einen kleinen Webserver sollte der Weg über OpenSSL völlig ausreichen. Das hat aber auch einen Nachteil: Die Browser vertrauen den eigenen Zertifikaten nicht und fragen zig mal nach, ob wir uns sicher sind, die gewünschte Seite aufzurufen. Im Firefox muss dieses erst heruntergeladen und akzeptiert werden und im Internet Explorer muss man auf den Link "Laden dieser Webseite fortsetzen (nicht empfohlen)" klicken. Der IE akzeptiert Euer Zertifikat, aber er quittiert die "Ungültigkeit" mit einer fetten roten Adressleiste.
Öffnet nun über Start -> Ausführen -> cmd die Kommandozeile und navigiert zu dem bin-Verzeichnis des Apacheservers:

```shell
cd "Programme\Apache Software Foundation\Apache2.2\bin"
```

Die Beispielbefehle laut Apache-SSL lassen zu wünschen übrig und schmeißen mit Fehlermeldungen um sich:

> Unable to load config info from /usr/local/ssl/openssl.cnf

Mit folgendem Befehl geht's dann aber doch endlich los:

```shell
openssl req -config ../conf/openssl.cnf -new -out wampserver.csr
```

Euch werden nun ein paar Fragen gestellt:

- **PEM pass phrase**: Ein beliebiges Passwort mit min. 4 Zeichen
- **Country Name**: DE (für Deutschland)
- **State**: Euer Bundesland
- **Locality Name**: Tragt hier den Namen Eurer Stadt ein
- **Organisation Name**: Tragt hier Euren Firmennamen ein oder lasst es leer
- **Organizational Unit Name**: In welchem Bereich ist die Firma tätig. Ansonsten leer lassen
- **Common Name**: Es wird hier vorgeschlagen den eigenen Namen anzugeben. ABER: Ihr werden dann einen Hinweis in der error.log erhalten, dass der common name nicht mit dem Namen des Servers übereinstimmt. Tragt hier bitte den Servernamen ein, wie ihr ihn in der httpd.conf angegeben habt OHNE den Doppelpunkt und dem Port.
- **E-Mail address**: Sollte klar sein
- **A challenge password**: Würde ich leer lassen. Haben oben doch schon eins eingetragen
- **An optional company name**: Würde ich auch leer lassen.

Jipie, wir sind die ewige Fragerei durch und haben nun eine `wampserver.csr` und eine `privkey.pem` im bin-Verzeichnis.

Im nächsten Schritt entfernen wir die Pass-Phrase:

```shell
openssl rsa -in privkey.pem -out wampserver.key
```

Gebt hier das Passwort, dass ihr oben unter "PEM pass phrase" angegeben habt ein. Nun ist eine `wampserver.key` im bin-Verzeichnis hinzugekommen.

Der letzte Befehl lautet:

```shell
openssl x509 -in wampserver.csr -out wampserver.cert -req -signkey wampserver.key -days 365
```

Legt nun im Apacheverzeichnis das Verzeichnis `cert` an und verschiebt die drei `wampserver.*` Dateien in dieses neue Verzeichnis. In der `httpd.conf` muss noch die SSL-Unterstützung aktiviert werden. Sucht nach folgender Zeile und entfernt die Raute `#` ganz am Anfang

```ini
LoadModule ssl_module modules/mod_ssl.so
```

Weiter unten findet Ihr die Einstellung mit der die erweiterte Konfiguration für SSL geladen werden kann. Dieser Abschnitt muss folgendermaßen aussehen:

```ini
# Secure (SSL/TLS) connections
Include conf/extra/httpd-ssl.conf
```

In `conf/extras` findet Ihr die gerade freigeschaltete `httpd-ssl.conf`. Sucht nach folgender Zeile:

```ini
SSLCertificateFile "C:/Programme/Apache Software Foundation/Apache2.2/conf/server.crt"
```

und ändert den Pfad entsprechend der Zertifikate ab:

```ini
SSLCertificateFile "C:/Programme/Apache Software Foundation/Apache2.2/cert/wampserver.cert"
```

Dasselbe auch hier:

```ini
SSLCertificateKeyFile "C:/Programme/Apache Software Foundation/Apache2.2/conf/server.key"
```

und entsprechend abändern:

```ini
SSLCertificateKeyFile "C:/Programme/Apache Software Foundation/Apache2.2/cert/wampserver.key"
```

Nach einem Neustart des Apache, könnt Ihr nun Eure Webseite auch über das https-Protokoll erreichen. Wichtig ist nur, dass die beiden Dateien `libeay32.dll` und `ssleay32.dll` global erreichbar sind. Dies haben wir im Bereich PHP bereits erledigt. Ohne PHP-Unterstützung müsstet Ihr den PATH um das Verzeichnis `C:\Programme\Apache Software Foundation\Apache2.2\bin` Verzeichnis erweitern.

## PHP-Erweiterungen für TYPO3

Damit TYPO3 läuft, müssen wir ein paar zusätzliche PHP-Extensions aktivieren. Dazu müssen wir PHP erstmal sagen, wo sich die Extensions befinden. Dazu muss folgende Zeile:

```ini
; extension_dir = "ext"
```

entsprechend geändert werden in

```ini
extension_dir = "ext"
```

auch folgende Möglichkeit funktioniert:

```ini
extension_dir = "C:/Programme/php/ext"
```

oder auch

```ini
extension_dir = ".\ext"
```

Laut TYPO3-Doku sollten folgende Extensions in PHP verfügbar sein:

- cURL
- filter (bereits enthalten)
- GD2
- JSON (bereits enthalten)
- mbstring
- mysql
- pcre (bereits enthalten)
- session (bereits enthalten)
- SPL (bereits enthalten)
- standard (bereits enthalten)
- xml (bereits enthalten)

## Ext: curl

In vielen Dokumentationen zur cURL-Integration heißt es, dass zwei Dateien in das Windows-System32-Verzeichnis kopiert werden müssen. Dem will ich hier nicht Folge leisten, da in der PHP-Doku folgender Satz zu finden war:

> The PHP manual used to promote the copying of files into the Windows system directory, this is because this directory (C:\Windows, C:\WINNT, etc.) is by default in the systems PATH. Copying files into the Windows system directory has long since been deprecated and may cause problems.

cURL bringt außer der `php_curl.dll` im ext-Verzeichnis auch noch die beiden Dateien `libeay32.dll` und `ssleay32.dll` mit. Allerdings liegen diese beiden Dateien im Root-Verzeichnis von PHP und somit nicht auffindbar von cURL. Um diese DLL-Dateien im PHP-Verzeichnis global auffindbar zu machen, müssen wir die Systemvariable `PATH` von Windows um das Verzeichnis von PHP erweitern (siehe Link oben).

Start -> Systemsteuerung -> System -> Erweitert -> Umgebungsvariablen -> Abschnitt "Systemvariablen" -> Macht hier einen Doppelklick auf die Variable PATH -> und fügt Folgendes noch hinten dran:

```ini
;C:\Programme\PHP
```

Bitte vergesst dabei nicht das führende Semikolon und bestätigt sämtliche Dialoge nun mit "OK". Danach müsste Ihr Euren Rechner einmal neu starten! Ich hab's ohne probiert und es ging nicht.

Nach dem Neustart müsst in der `php.ini` die Zeile

```ini
;extension=php_curl.dll
```

entsprechend abändern:

```ini
extension=php_curl.dll
```

und den Apache Server neu starten.

# GD2

Da wir für die PHP-Installation nicht den Installer nehmen konnten, müssen wir die GD-Lib nun selbst einbinden.

In unserer ZIP-Version ist die GD-Lib bereits enthalten und muss über die `php.ini` nur noch freigeschaltet werden. Editiert also die `php.ini` und sucht nach folgendem Eintrag:

```ini
;extension=php_gd2.dll
```

und ändert diesen in

```ini
extension=php_gd2.dll
```

Nach einem Neustart des Apache ist GD installiert und wird auch bei `PHP:phpinfo()` aufgeführt.

## Ext: mbstring

In der `php.ini` muss folgende Zeile

```ini
;extension=php_mbstring.dll
```

durch diese ersetzt werden

```ini
extension=php_mbstring.dll
```

## Ext: mysql

In der `php.ini` muss folgende Zeile:

```ini
;extension=php_mysql.dll
```

durch diese ersetzt werden

```ini
extension=php_mysql.dll
```

## Ext: bz2

Diese Extension wird von TYPO3 zwar nicht benötigt, aber unsere spätere phpMyAdmin-Installation braucht sie. In der `php.ini` muss folgende Zeile:

```ini
;extension=php_bz2.dll
```

durch diese ersetzt werden

```ini
extension=php_bz2.dll
```

## MySQL

{{% notice style="info" title="Wichtig" icon="info" %}}
Nach wirklich sehr langem Suchen habe ich herausgefunden, dass der Virenscanner McAfee das Konfigurationsscript daran hindert bestimmt Einstellungen am Betriebssystem durchzuführen. Ich empfehle also den Virenscanner temporär zu deaktivieren. Solltet Ihr ihn trotzdem anlassen, dann bitte nach der Installation/Konfiguration prüfen ob der MySQL-Server läuft und das macht Ihr bitte nicht einfach mit `mysql --user root -p` in der Kommandozeile, sondern bitte mit folgendem Befehl:

```shell
telnet localhost 3306
```

Sollte daraufhin die Versionsnummer von MySQL mit so ner kryptischen Zeile angezeigt werden, dann habt Ihr alles richtig gemacht. Konnte die Verbindung allerdings nicht aufgebaut werden, dann doch mal den Virenscanner deaktivieren und das Konfigurationsscript nochmal durchgehen.
{{% /notice %}}

## Installation

Auf der MySQL-Download-Seite werden 2 Downloads als "Recommended" vorgeschlagen. Wählt hier die 32 oder 64bit Variante.

Während der Installation wählt bitte die `Detailed Configuration`. Ihr erhaltet folgende Auswahl:

- **Developer Machine** (Entwickler-Server auf dem ein paar Applikationen drauf laufen. Erfordert ein Minimum an Speicher)
- **Server Machine** (Einige Serverprogramme werden auf dieser Maschine laufen. Wähle diese Option, wenn Du Deinen Rechner als Webserver laufen lassen willst. Benötigt schon was mehr Speicher.)
- **Dedicated MySQL Server Machine** (Dieser Server ist ein reiner MySQL-Server. Keine andere Software/anderer Dienst läuft auf diesem Server. MySQL wird alles an verfügbarem Arbeitsspeicher verwenden.)

- Ich habe mich für Punkt 2 entschieden, aber bitte, wählt selbst. Auf der nächsten Seite erhaltet Ihr wieder eine Auswahl:

- **Multifunctional Database** (Der Server wird optimiert für die Verwendung von InnoDB und MyISAM)
- **Transactional Database only** (Der Server wird nur auf die Verwendung von InnoDB optimiert. MyISAM ist aber natürlich weiterhin verwendbar)
- **Non-Transactional Database only** (Hier ist nur noch MyISAM aktiviert)

Alle aktuellen TYPO3-Versionen unterstützen in vielen Tabellen wie den Cache- und indexed_search-Tabellen InnoDB. Die restlichen Tabellen sollten bei guter Extensionprogrammierung und TYPO3-Konfiguration nur noch selten verwendet werden, da alle Daten dann im Cache vorliegen. In diesem Falle bitte Punkt 2 wählen. Für ein bisschen testen hier und entwickeln dort, könnte Punkt 1 interessanter sein.

Im nächsten Schritt könnt Ihr einfach auf "Next" klicken, es sei denn ihr wollt einen anderen Pfad für den InnoDB-Tablespace setzen.

Im nächsten Schritt habt Ihr folgende Auswahl:

- **Decision Support (DDS)*: Kaum gleichzeitige Verbindungen zum Server. Erwartet werden hier so um die 20 gleichzeitige Verbindungen
- **Online Transaction Processing**: Hier wird der Server auf bis zu 500 gleichzeitige Verbindungen optimiert
- **Manual Setting**: Hier könnt Ihr die gleichzeitigen Verbindungen selbst auswählen. Von 15 bis 1400 ist alles dabei.

Als Entwickler oder für wirklich sehr kleine Webseiten mit kaum Besuchern kann man Punkt 1 wählen. Der Standard sollte wohl aber zu Punkt 2 greifen. Die, die es genau wissen und Ihre Webseite entsprechend analysiert haben sollten mit Punkt 3 vorliebnehmen.

Im nächsten Schritt müsst Ihr entscheiden, ob der Server auch über TCP/IP erreichbar sein soll. Ich würde diese Einstellung empfehlen, den Port auf 3306 beibehalten aber die Checkbox für die Firewallregel deaktiviert lassen, damit der Server nur lokal erreichbar bleibt. Die andere Checkbox bei `Enable Strict Mode` bitte deaktivieren, auch wenn dort steht, dass sie besser aktiviert bleiben soll. Wenn Ihr diese Checkbox nicht deaktiviert, kann es passieren, dass Ihr folgende Fehlermeldung beim Anlegen neuer Seiten in TYPO3 erhaltet:

```shell
2: SQL error: 'Incorrect integer value: '' for column 'storage_pid' at row 1' (pages:NEW4c874265bfe6b)
```

Im Characterset-Dialog sollten wir Punkt 2 wählen (UTF-8). Auch wenn Eure bisherige Webseite nur Deutsch - Englisch ist, so weiß man nie, ob Russisch und Co. irgendwann mal hinzukommen. In der Doku zu TYPO3 wird auch empfohlen UTF-8 zu verwenden.

Im nächsten Schritt kann der MySQL-Service als Dienst registriert werden und braucht so nicht jedes Mal manuell gestartet zu werden. Die 2te Checkbox würde ich aktivieren, damit Ihr die mysql-Befehle in der Kommandozeile von überall aus aufrufen könnt ohne Euch explizit im BIN-Verzeichnis des MySQL-Servers befinden zu müssen.

Im nächsten Schritt solltet Ihr ein sicheres Passwort für den root (Admin) User vergeben. Wenn's nach Möglichkeit geht, solltet Ihr versuchen die beiden Checkboxen `Enable root access...` und `Create an Anonymous Account` deaktiviert zu lassen.

Endlich im letzten Schritt angekommen, könnt Ihr nun den Execute-Button betätigen und die Installation mit `Finish` abschließen.

## PhpMyAdmin

Da unser Artikel zumindest etwa mehr Sicherheit erreichen soll, solltet Ihr für jede Datenbank einen eigenen User anlegen. Nehmt dazu bitte nicht die Extension phpmyadmin aus dem Repository von TYPO3, da diese immer wieder sehr buggy ist. So wird es auch in einem Newsletter seitens www.jweiland.net geschildert. Deshalb richten wir uns hier nun eine unabhängige phpMyAdmin-Installation ein.

Entpackt die Dateien nach `C:\Programme\phpMyAdmin`

Editiert die Apachekonfigurationsdatei in conf/https.conf und fügt ganz am Ende Folgendes hinzu:

```ini
Alias /phpMyAdmin "C:/Programme/phpMyAdmin/"

<Directory "C:/Programme/phpMyAdmin">
Options none
AllowOverride Limit
Order deny,allow
Deny from all
Allow from 127.0.0.1
</Directory>
```

Damit erstellt Ihr einen Alias, der über diese URL erreichbar ist.

```ini
localhost/phpMyAdmin
```

Der eigentliche Inhalt muss aber nicht zwingend im htdocs-Verzeichnis liegen, wie Ihr in dem Beispiel sehen könnt. Bitte beachtet, dass die Groß- und Kleinschreibung auch in der URL zu berücksichtigen sind. Viele Beispiele im Internet definieren die Aliase auch in dieser Form:

```ini
Alias /phpMyAdmin/ "C:/Programme/phpMyAdmin/"
```
Man beachte den abschließenden Slash am Ende des Aliasnamen. Dieser sorgt dafür dass Ihr diesen Slash auch in der URL hinten dran hängen müsst

```ini
localhost/phpMyAdmin/
```

Folgende Beispiele führen zu Fehlern:

> localhost/phpmyadmin: NOT FOUND -> The requested URL /phpmyadmin was not found on this server.

>192.168.0.87/phpMyAdmin: FORBIDDEN -> You don't have permission to access /phpMyAdmin on this server.

Da ich in der Konfiguration die 127.0.0.1 angegeben habe, führt letztes Beispiel zu dem FORBIDDEN-Fehler. Ein externer Zugriff ist somit nicht mehr möglich.

Erstellt nun das Verzeichnis `config` im phpMyAdmin-Verzeichnis. Das ist ein Sicherheitsfeature, damit ihr in den Wizard von phpMyAdmin gelangen könnt. Ruft dazu folgende Seite auf:

```shell
localhost/phpMyAdmin/setup/
```

Dank des Links in der ersten gelben Warnbox "Unsichere Verbindung", können wir das Konfigtool mit einer sicheren Verbindung aufbauen. Passwörter werden nun verschlüsselt über die Leitung geschickt. Wenn Ihr das mit OpenSSL nicht hinbekommen habt oder die falsche Version des Apache installiert habt, erscheint hier auch noch eine weitere gelbe Box mit dem Hinweis, dass der Server keine Unterstützung für sichere Verbindungen unterstützt. Auch der zweiten bzw. dritten Warnung solltet Ihr Folge leisten und die Option zur "SSL-Verbindung erzwingen" aktivieren.
Nachdem nun alle Warnungen beseitigt sind, könnt Ihr nun einen neuen Server für phpMyAdmin anlegen. Ich empfehle folgende Settings:

- **Serverbezeichnung**: WAMP-Server
- **Hostname**: localhost, wenn Ihr bei "Art der Verbindung" socket auswählt. 127.0.0.1 solltet Ihr die Art der Verbindung auf tcp beibehalten. Unter Windows XP spielt das keine Rolle, aber alle danach folgenden Betriebssysteme unterstützen IPv6 und haben den DNS-Eintrag für localhost in der hosts-Datei deaktiviert.
- **Benutze SSL**: Ja
- **Art der Verbindung**: Siehe Hostname
- **Authentifikationstyp**: Ich empfehle http
- **Benutzername für config Authentifikation**: root entfernen bzw. Feld einfach leer lassen

Nach dem Speichern ladet Ihr wieder auf der Startseite und könnt nun die Sprache auf Deutsch stellen. Lasst die anderen Einstellung so bestehen wie sie sind und klickt erneut auf Speichern. Im Hintergrund wurde nun eine config.inc.php im phpMyAdmin-Verzeichnis config erstellt. Diese bitte direkt in das phpMyAdmin-Verzeichnis verschieben und den config-Ordner aus Sicherheitsgründen wieder löschen.

Nach einem Aufruf von

```shell
localhost/phpMyAdmin
```

im Browser erscheint nun die http-Abfrage in der Ihr Euer per MySQL-Konfigurationswizard erstellten Usernamen und Passwort eintragen müsst. Ihr landet dann in der Euch bekannten phpMyAdmin-Oberfläche.

## GraphicsMagick

GraphicsMagick ist die von TYPO3 empfohlene Grafikbibliothek und kann als EXE-Installer heruntergeladen werden. Es gibt eine Q8 und eine Q16 Version. Ich hab mich hier für die Q16 entschieden, aber auch nur weil die am meisten heruntergeladen wurde.

GraphicsMagick schlägt einen Installationspfad inkl. Versionsnummer vor. Da ich diesen Pfad später im Installtool wieder angeben muss, habe ich mich für die etwas einfachere Weise entschieden:

```shell
C:\Programme\GraphicsMagick
```

Diesen Pfad im Installtool bitte, wie dort als Beispiel aufgeführt, mit einem abschließenden Backslash (\) eintragen.

## Der neue Server und TYPO3

Der neue Server sollte nun sauber laufen. Alle empfohlenen Bedingungen laut der `TYPO3-INSTALL.txt` sind erfüllt und Dank, der mitgelieferten `php.ini` für den Produktiveinsatz haben wir ein recht sicheres System aufbauen können. Selbst der MySQL-Server ist auf unsere Bedürfnisse angepasst.

Ihr könnt nun TYPO3 im htdocs Verzeichnis installieren und erhaltet evtl. folgende Fehlermeldung:

> Fatal error: mcrypt_create_iv(): Could not gather sufficient random datain C:\Programme\Apache Software Foundation\Apache2.2\htdocs\introductionpackage-4.4.2\t3lib\class.t3lib_div.php on line 1597

Ich kann nicht genau sagen, ob diese Fehlermeldung an PHP oder TYPO3 liegt. Auf der PHP-Seite heißt es, dass der Code von TYPO3 nicht ganz sauber sein soll und auf der anderen Seite heißt es, PHP hat den Fehler doch schon bemerkt und in seinem SVN-Verzeichnis bereits behoben. Hier ein paar Links zum Thema

[PHP Bug 52523](http://bugs.php.net/bug.php?id=52523)

[TYPO3 Bug 15629](http://bugs.typo3.org/view.php?id=15629)
