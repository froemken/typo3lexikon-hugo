+++
title = "Bildausschnitt wählen"
date = 2024-01-09T20:16:32+01:00
aliases = ["image-manipulation.html"]
+++

## Bildausschnitt wählen - ImageManipulation

Seit TYPO3 7.2 könnt Ihr Bilder in Inhaltselementen individuell zuschneiden, bevor diese auf Eurer Webseite gerendert werden. Dazu wurde in die Tabelle sys_file_reference eine neue Spalte crop eingebunden. Der Inhalt könnte z.B. so aussehen:

```json
{
    "x": 15.444649825426083,
    "y": 223.5653781242466,
    "width": 560.555350174574,
    "height": 438.69549144097084,
    "rotate": 0,
    "scaleX": 1,
    "scaleY": 1
}
```

x und y zeigt auf die obere linke Ecke des von Euch gewählten Ausschnitts und mit width/height wird Breite/Höhe abgespeichert.

Die vorgegebenen Seitenverhältnisse seitens TYPO3 sind 16:9, 4:3, 1:1 und freie Auswahl. Leider lassen sich diese Werte nicht einfach mit Hilfe von pageTSconfig oder TypoScript konfigurieren. Hier hilft nur eine Änderung im TCA der Tabelle sys_file_reference. Diese Änderung solltet Ihr nicht mittels der typo3conf/extTables.php realisieren, da die Einbindung dieser Datei bereits als deprecated markiert ist und bald entfernt wird.

Die Standardvorgaben findet Ihr in der Datei ImageManipulationElement.php:

```php
/**
 * Default element configuration
 *
 * @var array
 */
protected $defaultConfig = array(
    'file_field' => 'uid_local',
    'enableZoom' => false,
    'allowedExtensions' => null, // default: $GLOBALS['TYPO3_CONF_VARS']['GFX']['imagefile_ext']
    'ratios' => array(
        '1.7777777777777777' => 'LLL:EXT:lang/locallang_wizards.xlf:imwizard.ratio.16_9',
        '1.3333333333333333' => 'LLL:EXT:lang/locallang_wizards.xlf:imwizard.ratio.4_3',
        '1' => 'LLL:EXT:lang/locallang_wizards.xlf:imwizard.ratio.1_1',
        'NaN' => 'LLL:EXT:lang/locallang_wizards.xlf:imwizard.ratio.free',
    )
);
```

Die ArrayKeys spiegeln die Seitenverhältnisse wieder. 16:9 also 16 geteilt durch 9 ergibt 1.7777777777777777. Alle Werte über 1 sind Querformat. Werte unter 1 sind Hochformat. Im Folgenden habe ich mich für ein Seitenverhältnis von 23:18 entschieden. 23 geteilt durch 18 ergibt 1.2777777777777778.

Erstellt nun in Eurer individuellen Projekt Extension die Datei Configuration/TCA/Overrides/sys_file_reference.php mit folgendem Inhalt:

```php
<?php
$GLOBALS['TCA']['sys_file_reference']['columns']['crop']['config']['ratios'] = array(
    '1.7777777777777777' => 'LLL:EXT:lang/locallang_wizards.xlf:imwizard.ratio.16_9',
    '1.3333333333333333' => 'LLL:EXT:lang/locallang_wizards.xlf:imwizard.ratio.4_3',
    '1.2777777777777778' => '230/180',
    '1' => 'LLL:EXT:lang/locallang_wizards.xlf:imwizard.ratio.1_1',
    'NaN' => 'LLL:EXT:lang/locallang_wizards.xlf:imwizard.ratio.free',
);
```

Vielleicht etwas unerwartet, aber sobald Ihr Eure eigenen Seitenverhältnisse erstellt, werden die TYPO3 seitigen Seitenverhältnisse komplett entfernt. Wenn Ihr die beibehalten wollt, dann müsst Ihr die wie im Beispiel oben entsprechend ergänzen.
