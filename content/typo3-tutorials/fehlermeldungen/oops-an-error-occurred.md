+++
title = "Oops, an error occurred"
date = 2024-03-06T19:54:18+01:00
aliases = ["oops-an-error-occurred.html"]
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 6.2{{% /badge %}}

Mit Einführung von [ApplicationContext](/typo3-tutorials/core/systemextensions/core/application-context) wird TYPO3 von Haus aus in dem sogenannten `Production`-ApplicationContext ausgeführt. Eben dieser verhindert die Ausgabe von Fehlermeldungen und verhindert somit auch die Veröffentlichung von möglicherweise sicherheitsrelevanten Daten.

Die Meldung `Oops, an error occurred! Code: {code}` bezieht sich nur auf Fehler, die bei der Verarbeitung von Inhaltselementen entstehen. Das kann beispielsweise "Text", "Text mit Medien", "Tabellen", "Formulare", oder aber auch "Plugin" sein. Ich lehne mich mal weit aus dem Fenster und behaupte, dass dieser Fehler zu etwa 90 % bei der Verarbeitung von Plugins auftritt.

{{% notice style="info" title="Für Entwickler" icon="exclamation" %}}
Das Rendering der Inhaltselemente findet in der `render` Methode der TYPO3 Klasse `ContentObjectRenderer` statt. Um die `render` Methode eines jeden einzelnen Inhaltselementes herum ist ein `try-catch` Block gewickelt, der auftretende Fehler abfängt und je nach Konfiguration an den jeweiligen ExceptionHandler weiterreicht.
{{% /notice %}}

## Analyse des Codes

Fehlermeldung: `Oops, an error occurred! Code: 202304241947330576a7e3`

Der Code kann in Datum (Jahr, Monat, Tag), Uhrzeit (Stunden, Minuten, Sekunden) und einem eindeutigen 8-stelligen Request-Hash aufgeteilt werden:

Datum: 20230424: 24.04.2023
Uhrzeit: 194733: 19:47:33
Request-Hash: 0576a7e3

### Fehler finden

TYPO3 protokolliert diese Art Fehlermeldungen in seinem Log-Verzeichnis:

- neuer oder entspricht TYPO3 9 (Standalone): `typo3temp/var/log/typo3_[10-stelliger Hash-Wert].log`
- neuer oder entspricht TYPO3 9 (Composer): `var/log/typo3_[10-stelliger Hash-Wert].log`
- TYPO3 8 (Standalone): `typo3temp/var/logs/typo3_[10-stelliger Hash-Wert].log`
- In früheren TYPO3 Versionen gibt es zwar schon die Logging-API, aber TYPO3 selbst nutzt diese erst mit Version 8.

Diese Log-Dateien können über die Zeit sehr groß werden. Das Öffnen dieser Dateien mit einem Editor kann sehr lange dauern, aber auch den Editor zum Absturz bringen. Ich empfehle solch große Dateien mittels `less` zu öffnen:

```shell
less typo3_bd3c26ba0b.log
```

Drückt nun die Slash-Taste `/`. Damit versetzt ihr `less` in den Such-Modus. Kopiert und fügt den 8-stelligen Request-Hash aus dem Code von oben rein und drückt Enter. Es kann eine Weile dauern bis `less` euch zur gewünschten Stelle bringt. Hier ein Beispiel-Suchergebnis:

```shell
Sat, 17 Feb 2024 16:48:18 +0000 [ALERT] request="19e3c9e03b5cd" component="TYPO3.CMS.Frontend.ContentObject.Exception.ProductionExceptionHandler": Oops, an error occurred! Code: 2024021716481867fcf23b- Exception: Undeclared arguments passed to ViewHelper TYPO3\CMS\Fluid\ViewHelpers\Format\HtmlentitiesViewHelper: doubleQuote. Valid arguments are: value, keepQuotes, encoding, doubleEncode, in file /var/www/html/vendor/typo3fluid/fluid/src/Core/ViewHelper/AbstractViewHelper.php:461
```

## Fehler ausgeben lassen

Um den tatsächlichen Fehler im Frontend ausgeben zu lassen, müsst ihr in den Debug-Modus wechseln. Dazu habt ihr folgende Möglichkeiten. Bevorzugt von oben nach unten.

### ApplicationContext

[Setz den ApplicationContext](/typo3-tutorials/core/systemextensions/core/application-context#applicationcontext-setzen) auf `Development`.

### additional.php

```php
<?php
$GLOBALS['TYPO3_CONF_VARS']['FE']['debug'] = true;
$GLOBALS['TYPO3_CONF_VARS']['SYS']['devIPmask'] = '[DEINE IP-ADRESSE]';
$GLOBALS['TYPO3_CONF_VARS']['SYS']['displayErrors'] = 1;
```

### Preset

Im Installtool oder auch als System-Maintainer im TYPO3 Backend könnt ihr im Menü "Settings" unter "Presets" auf das "Debug"-Preset umstellen. Beachtet bitte, dass in diesem Fall jeder, der auf die fehlerhafte Seite kommt, den Fehler sieht.

### Backtrace

Zusätzlich zu dem Fehler im Frontend lässt sich dort auch ein vollständiger Backtrace, also ein Hergangs-Protokoll über alle aufgerufenen PHP Klassen und Methoden darstellen, die schlussendlich zu diesem Fehler geführt haben. Wir Entwickler lieben solche Ausgaben. Bitte macht von sowas einen Screenshot und schickt es eurer umsetzenden Agentur. Danach seid ihr deren Liebling. Wirklich!

Wenn ihr auf den `Development`-ApplicationContext gewechselt habt, dann seht ihr den Backtrace schon. Alle anderen unter euch müssen im TypoScript noch eine Einstellung hinzufügen:

```typo3_typoscript
config.contentObjectExceptionHandler = 0
```
