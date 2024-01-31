+++
title = "scriptmerger"
date = 2024-01-09T20:52:38+01:00
aliases = ["scriptmerger.html"]
+++

Google bietet unter der Kategorie "Webtools" ein Firefox Add-On mit dem Namen `Page speed` an. Um dieses Add-On zu installieren, muss das Add-On Firebug bereits installiert sein.

Mit diesen beiden Tools bewaffnet könnt ihr nun auf die Suche gehen und überprüfen, ob eine/Eure Webseite optimiert ist oder nicht. Ich kann euch jetzt schon sagen: Die meisten Webseiten sind nicht optimiert!

### Woran liegt's?

Die Webserver bringen meist schon eine Umgebung mit, die die komprimierte Datenübertragung unterstützen, aber meist wurde dieses Feature nicht vom Webseiteninhaber aktiviert.

Jede Verlinkung zu einer Datei auf dem Server bedarf einem erneuten Verbindungsaufruf zum Server. Viele eingebundene CSS- und JS-Dateien verlangsamen also den Aufbau Eurer Webseite.

### Scriptmerger hilft!

Mit der TYPO3-Extension scriptmerger wird euch ein Tool an die Hand gelegt, dass sämtliche JS- und CSS-Dateien im Header Eurer Webseite ausließt, verkleinert (Entfernung überflüssiger Leerzeichen und ähnlichem), zusammen führt (zu einer großen Datei) und anschließend komprimiert. Im Endeffekt habt ihr also nur noch eine CSS-Datei pro "media"-Attribut (Attribut im link-Tag: screen, print und all) und eine JS-Datei.

### Scriptmerger installieren

Nach der Installation muss von seiten TYPO3 nur das statische Template eingebunden werden für die Seiten, für die Scriptmerger aktiv sein soll. Der Rest geschieht erstmal serverseitig:

Ihr müsst eine .htaccess-Datei im Rootbereich Eures Webspeicherplatzes erstellen und z.B. mit dem Inhalt aus der mitgelieferten example.htaccess füllen:

```shell
# Expires Header + Removal of ETag
<FilesMatch "\.(ico|png|gif|js|css|jpg|jpeg|swf)">
<IfModule mod_expires.c>
ExpiresActive on
ExpiresDefault "access plus 7 days"
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

Mit dieser Konfiguration hat sich meine Webseite von 62/100 Punkte im page speed-Add-On auf 87/100 raufgearbeitet. Im Add-On habe ich dann noch einen Mängel gefunden, dass der expiration wert zu niedrig ist. Nachdem ich diesen auf 2 Monate hoch gesetzt habe, hatte ich schon 89/100 Punkte:

```shell
# Expires Header + Removal of ETag
<FilesMatch "\.(ico|png|gif|js|css|jpg|jpeg|swf)">
<IfModule mod_expires.c>
ExpiresActive on
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

Die Ablaufzeit ist erforderlich, damit der Cache bzw. die zwischengelagerten Proxies diese Dateien (ico, png, jpg, ...) temporär abspeichern können. Ohne diese Proxies würde das Surfen im Internet richtig langsam werden.

Damit unser Browser weiß, dass er `gz.js` und `gz.css` Dateien entpacken muss, müssen wir den "Compressed Content"-Bereich in die .htaccess-Datei einbinden.

### Hinweis

In der Dokumentation und auf anderen Webseiten wird darauf hingewiesen, dass es bei der Optimierung und Komprimierung zu Problemen kommen kann, wenn z.B. irgendwo ein abschließendes Semikolon fehlt. Auch mit verschiedenen JS/CSS-Frameworks kann es zu Problemen kommen. In diesem Fall studiert bitte die Dokumentation, denn dort wird unteranderem beschrieben, wie man ganz bestimmte Dateien von der Optimierung/Komprimierung ausschließen kann. Auch ein entsprechendes Beispiel ist in der Doku enthalten.
