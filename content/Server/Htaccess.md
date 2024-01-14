+++
title = ".htaccess und TYPO3"
linkTitle = ".htaccess"
date = 2024-01-09T20:59:25+01:00
slug = "htaccess"
+++

Ich möchte auf dieser Seite nicht erklären wie `.htaccess` Dateien erstellt werden. Nein. Davon gibt es schon genug Dokumentationen im Web:

- [Allgemeines zu htaccess](http://de.selfhtml.org/servercgi/server/htaccess.htm)
- [URLs manipulieren](http://de.selfhtml.org/servercgi/server/rewrite.htm)
- [Reguläre Ausdrücke](http://de.selfhtml.org/perl/sprache/regexpr.htm)

Auf dieser Seite geht es mehr um das Zusammenspiel von TYPO3 und `.htaccess` Dateien gerade in Bezug unter der Verwendung von der Extension `EXT:readurl`. In den nächsten Zeilen werde ich Euch meine `.htaccess` Datei in Detail näher erklären:

## RewriteEngine

Mit RewriteEngine kannst Du das Modifizieren von URLs de- bzw. aktivieren. Erst wenn diese Option aktiviert ist, können z.B. alle Anfragen, die an den Server gesendet werden auf eine völlig andere Webseite umleiten.

```ini
RewriteEngine On
```

## Bestimmte Verzeichnisse nicht umschreiben

Wenn Ihr RealUrl installiert habt, dann erhaltet ihr je nach Konfiguration URLs wie

```shell
/meine-seite/kontakt/impressum.html
```

Dabei sind die vermeintlichen Ordner gar keine echten Ordner auf dem Server, sondern Informationen, die an die `index.php` weitergeleitet werden, um dann die gewünschte Seite aufrufen zu können. Was aber, wenn wir wirklich mal einen Ordner auf dem Server öffnen wollen? Dann darf dieser Name des Ordners nicht an die `index.php` weitergeleitet werden. Um das zu erreichen, verwende ich das Skript aus der RealUrl-Dokumentation:

```ini
RewriteRule ^typo3$ - [L]
RewriteRule ^typo3/.*$ - [L]
RewriteRule ^uploads/.*$ - [L]
RewriteRule ^fileadmin/.*$ - [L]
RewriteRule ^typo3conf/.*$ - [L]
```

{{% notice style="info" title="Hinweis" icon="info" %}}
[L] bedeutet, dass nach dieser Umschreibung der URL keine weiteren Umschreibungen mehr ausgeführt werden.
{{% /notice %}}

Der Bindestrich `-` bedeutet, dass keine Umschreibung der URL stattfinden soll.

## Doppelter Content

Wer von Euch seine Seite schon mal mit [SeitWert](https://www.seitwert.de) bewertet hat, wird bestimmt schon mal über den Eintrag bzgl. doppelten Content/Inhalt gestoßen sein. So kommt es je nach Konfiguration vor, dass eine Seite wie `sfroemken.de` das Gleiche anzeigt wie `www.sfroemken.de`. Dieses Problem kann sich auch negativ auf die Position in den Suchergebnissen auswirken. Aber auch das lässt sich mithilfe einer `.htaccess` bereinigen:

```ini
RewriteCond  %{HTTP_HOST} ^sfroemken\.de$ [NC]
RewriteRule ^(.*)$ www.sfroemken.de/$1 [R=301,L]
```

### Erklärung

`%{HTTP_HOST}` kann in diesem Fall `sfroemken.de` oder aber auch `www.sfroemken.de` sein. Mit `RewriteCond` könnt Ihr nun überprüfen, ob der `HTTP_HOST` mit dem Wert `sfroemken.de` übereinstimmt. Dank `[NC]` soll die Groß- und Kleinschreibung bei diesem Vergleich nicht berücksichtigt werden.

Wenn `RewriteCond` nun ein `true` zurück gibt, dann wird die Modifikation in `RewriteRule` ausgeführt. Alles, was durch die Klammern markiert wurde, kann im zweiten Parameter mit `$1` wiederverwendet werden. `[R=301]`: Das `R` steht für `redirect` und die `301` ist ein bestimmter Servercode, mit dem bestimmt wird, dass es sich bei dieser Weiterleitung, um eine `Parmanente Weiterleitung` handelt.

So wird aus

```shell
sfroemken.de/kontakt/index.php
```

ein

```shell
www.sfroemken.de/kontakt/index.php
```

## Subdomains

Bei Subdomains müssen wir das genau andersrum machen. Dort gibt es kein `www`. Da www.seitwert.de aber trotzdem überprüft, ob die Domain auch mit einem `www` davor erreichbar ist, müssen wir das folgendermaßen abfangen:

```ini
RewriteCond  %{HTTP_HOST} ^www\.typo3\.sfroemken\.de$ [NC]
RewriteRule ^(.*)$ typo3.sfroemken.de/$1 [R=301,L]
```

## Ordner

Leider haben sich viele Surfer daran gewöhnt Ordner auf einem Server ohne abschließendem Slash (/) anzugeben. Damit dieser Slash automatisch hinten dran gemacht wird, können wir folgendes Script verwenden:

```ini
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-l
RewriteRule (.*[^/])$ %{HTTP_HOST}/$1/ [L,R]
```

### Erklärung

Die ersten beiden RewriteCond fragen ab, ob es sich bei der aufzurufenden URL nicht um eine Datei handelt und nicht um einen symbolischen Link. Wenn beides zutrifft, kann getrost ein zusätzlicher Slash hinten angefügt werden.

### Achtung
Wenn ihr RealUrl so konfiguriert habt, dass ihr URLs wie

```shell
/kontakt/impressum.html
```

erhaltet, dann muss das Skript noch etwas angepasst werden, dann ansonsten erhaltet ihr eine URL wie diese hier:

```shell
/kontakt/impressum.html/
```

Hier das angepasste Skript:

```ini
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-l
RewriteCond %{REQUEST_FILENAME} (^.htm$|^.html$)
RewriteRule (.*[^/])$ %{HTTP_HOST}/$1/ [L,R]
```

Heißt: Wenn die URL mit `.htm` oder `.html` endet, dann soll der zusätzliche Slash nicht hinten dran gemacht werden.

## Index-Dateien in Ordnern

Je nach Projekt und Ordner kann es vorkommen, dass Du trotz RealUrl die Index-Dateien in bestimmten Ordnern ausführen möchtest:

```ini
/fileadmin/projekt/download/index.php
```

Allerdings wird derzeit noch diese URL an die TYPO3-eigene index.php im Rootverzeichnis weitergeleitet. Um auch das zu unterbinden sind folgende 6 Zeilen vorgesehen:

```ini
RewriteCond %{REQUEST_FILENAME}/index.html -f
RewriteRule / %{REQUEST_URI}/index.html [L]
RewriteCond %{REQUEST_FILENAME}/index.htm -f
RewriteRule / %{REQUEST_URI}/index.htm [L]
RewriteCond %{REQUEST_FILENAME}/index.php -f
RewriteRule / %{REQUEST_URI}/index.php [L]
```

## URL-Modifikation für RealUrl

Dieses Skript ist das wichtigste Skript überhaupt für RealUrl. Hier wird nun endlich gesagt, dass alle Teile aus der URL an die TYPO3-eigene index.php weitergeleitet werden sollen zur Weiterverarbeitung durch RealUrl.

```ini
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-l
RewriteRule .* index.php
```

### Hinweis

Je nach Art Eurer TYPO3-Installation könnte es sein, dass Eure index.php ein symbolischer Link ist. In diesem Fall müsst ihr die letzte Zeile folgendermaßen ändern:

```ini
RewriteRule .* /index.php
```

## Scripmerger

Dieses Script benötigt Ihr nur, wenn ihr die Extension `EXT:scriptmerger` verwenden wollt:

```ini
# Expires Header + Removal of ETag
<FilesMatch "\.(ico|png|gif|js|css|jpg|jpeg|swf)">
<IfModule mod_expires.c>
ExpiresActive on
ExpiresDefault "access plus 7 days"
ExpiresDefault "access plus 2 months"
</IfModule>

# ETag
FileETag MTime Size
<IfModule mod_headers.c>
FileETag none
Header unset Last-Modified
</IfModule>
</FilesMatch>

# Compressed Content
<FilesMatch "\.gz\.(js|css)">
<IfModule mod_headers.c>
Header set Content-Encoding gzip
</IfModule>
</FilesMatch>
```
