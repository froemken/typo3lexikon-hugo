+++
title = "cc_awstats"
date = 2024-01-09T20:50:48+01:00
aliases = ["cc-awstats.html"]
+++

Mit AwStats habt ihr ein Tool, das jeden Webseitenzugriff in einer Datei abspeichert und euch ermöglicht alle gemachten Zugriffe sauber zu protokollieren und darzustellen.

Wenn ihr nach der Installation direkt auf den neu erschienen Button in der linken Navigationsleiste klickt, hagelt es erst mal wieder Fehlermeldungen:

```
FEHLER:
No logfiles found in directory ''
The logfiles have to be named *.log or *log*.txt
examples: my-domain.log logfile.txt website-log.txt
```

Directory ''? Ganz genau. Während der Defaultinstallation von TYPO3 wird im Installtool kein Wert für das Log-Verzeichnis gesetzt. Im Installtool findet Ihr die Eigenschaft: `logfile_dir`. Ich würde empfehlen hier den Wert: `fileadmin/` zu setzen, damit wir auch mit der "Dateiliste" darauf zugreifen können:

```php
['FE']['logfile_dir'] = 'fileadmin/';
```

In diesem angegebenen Verzeichnis sucht AwStats nun nach Dateien, die entweder die Endung `*.log` haben oder das Wort `log` im Namen enthalten aber dann die Endung `*.txt` benötigen. Wenn ihr nur mit der Dateiliste arbeitet, dann habt ihr 2 Möglichkeiten eine entsprechende Logdatei anzulegen:

1. Die Dateiliste kann nur folgende Dateien anlegen:

   `*.txt, *.html, *.htm, *.css, *.tmpl, *.js, *.sql, *.xml, *.csv, *.phpsh, *.inc`

   `*.log` ist nicht enthalten, also müssen wir eine Datei anlegen, die ein `log` im Dateinamen enthält und die Endung `*.txt` besitzt. Wie so eine Datei heißen könnte, seht ihr ja in der Fehlermeldung. Ich nutze z.B. `sfroemken-log.txt`

2. Im Installtool gibt es noch die Eigenschaft `txtfile_ext` im Bereich `SYS`. Fügt dieser Liste am Ende noch die Endung `log` hinzu:

   ```php
   ['SYS']['txtfile_ext'] = 'txt,html,htm,css,tmpl,js,sql,xml,csv,phpsh,inc,log'
   ```

Nun könnt ihr auch mit der Dateiliste Namen wie `sfroemken.log` im Fileadmin-Verzeichnis anlegen.

### AWStats konfigurieren

AWStats muss im Setupbereich des Haupttemplates konfiguriert werden. Gebt hier den Dateinamen an, wie ihr ihn im `fileadmin` Verzeichnis angelegt habt.

```typo3_typoscript
config.stat = 1
config.stat_apache = 1
config.stat_apache_logfile = sfroemken-log.txt
```

Nach einem Klick auf AWStats werden euch alle Dateien aufgelistet, die dem Muster aus der Fehlermeldung entsprechen. Wir befinden uns direkt in der Konfiguration unserer Datei und AWStats erwartet nun, für welche Domain die Datei zuständig sein soll. Bei einem Domainnamen wie `www.sfroemken.de` würde ich hier folgenden Eintrag machen:

```
sfroemken.de,www.sfroemken.de
```

Danach auf `Konfiguration sichern` und im nächsten Fenster müsst ihr noch auf die Datei selbst geklicken und landet nun auf der Übersicht. Nach einem Klick auf `Jetzt aktualisieren` erhaltet ihr evtl. die Fehlermeldung:

```
No qualified records found in log (0 corrupted, 0 dropped)
```

Das liegt daran, dass noch niemand eure Webseite nach der AWStats-Installation besucht hat. Geht mal in das Frontend und klickt auf eurer Seite ein bisschen rum. Zurück im Backend klickt ihr nun wieder auf den Button `Jetzt Aktualisieren` und schon seht ihr wer mit welcher IP-Adresse von welcher Seite auf eurer Seite war.
