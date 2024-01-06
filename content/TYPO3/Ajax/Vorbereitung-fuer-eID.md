+++
title = "eID Key für AJAX-Requests registrieren"
linkTitle = "Vorbereitung für eID"
date = 2024-01-06T19:24:41+01:00
slug = "vorbereitung-fuer-eid"
weight = 2
+++

Damit Ihr Euer AJAX-Script mithilfe eines eID-Parameters in der URL aufrufen könnt, müsst
Ihr diesen Parameter zunächst in einer `ext_localconf.php` registrieren. Dazu benötigt Ihr
eine Extension. Ihr könnt Euch ein Grundgerüst mithilfe des ExtensionManagers zusammen 
stellen lassen oder Ihr verwendet eine bereits bestehende Extension von Euch.

Erstellt nun, falls noch nicht vorhanden, die Datei ext_localconf.php im Rootverzeichnis 
Eurer Extension mit einem Inhalt wie diesem hier:

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include']['beliebiger_key'] = 'EXT:extension_key/Classes/Ajax/EurePhpDatei.php';
```

Mit dieser Zeile registriert Ihr einen eID Key, den Ihr in Eurer URL verwenden könnt.
So z.B. könnte Eure URL dann aussehen.

`www.eure-webseite.de/index.php?eID=beliebiger_key`

Achtet bitte auf die richtige Schreibweise (Groß- und Kleinschreibung) von "eID" und
Eurem Key.

Der Wert von eID wird später von TYPO3 wieder ausgelesen. Wenn Ihr einen Dateipfad 
angebt (Beispiel oben), wird diese Datei mithilfe von include() geladen. Ihr seid selbst
für die Ausgabe verantwortlich.

Als Alternative könnt Ihr auch ein Callable angeben. Schaut Euch dazu bitte das
Datenbank-Beispiel an. Hier zeige ich Euch auch welche Callable-Typen erlaubt sind
