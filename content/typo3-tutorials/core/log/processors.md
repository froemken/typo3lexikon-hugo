+++
title = "Logging API: Processors"
linkTitle = "Processors"
date = 2024-01-06T23:50:44+01:00
slug = "processors"
aliases = ["processors.html"]
+++

Die von TYPO3 mitgelieferten Writer arbeiten mit der LogRecord Klasse zusammen, die die wichtigsten Zusatzinformationen zu Eurem Logeintrag bereithält:

**requestId**
: Eine eindeutige ID, die den aktuellen Request identifiziert

**created**:
: Aktueller Zeitstempel. Realisiert mit `PHP:microtime`

**component**:
: Der Name des Loggers. Meist ein PHP Klassenname inkl. Namespace

**level**:
: Level, Wichtigkeit des Protokol Eintrages. Es wird der Integer des Levels geloggt. Also 4 für WARNING

**message**:
: Eure Nachricht, die protokolliert werden soll

**data**:
: Ein paar zusätzliche Daten, die parallel zu Eurer Nachricht als Arary mit abgespeichert werden sollen

Mithilfe der Methode `__toString()` werden die Daten für den FileWriter und mit der Methode `toArray()` automatisch für den DatabaseWriter entsprechend aufbereitet.

Processoren werden immer nur dann eingesetzt, wenn dieser Standard aus dem LogRecord mal nicht mehr ausreicht, ihr z.B. zusätzlich noch ein paar Angaben zum verbrauchten Arbeitsspeicher benötigt oder den Backtrace gleich mit protokollieren wollt. Im Folgenden die Processoren, die von Haus aus in TYPO3 dabei sind.

## IntrospectionProcessor

Hierbei handelt es sich um einen sehr gewaltigen Processor. An der Position, wo die `writeLog()` Methode aufgerufen wird, geht dieser Processor den ganzen Quellcode zurück, sammelt alle aufgerufenen Klassen und Methodennamen, sowie die Zeilennummern und fügt diese Information den zusätzlichen Informationen (data) der Klasse LogRecord hinzu.

## MemoryPeakUsageProcessor

Dieser Processor holt sich mit `memory_get_peak_usage()` den zur Laufzeit maximalen Speicherverbrauch und fügt diesen dem LogRecord hinzu.

## MemoryUsageProcessor

Dieser Processor ist ähnlich dem `MemoryPeakUsageProcessor`. Jedoch mit dem Unterschied, dass er nur den Speicherverbrauch misst, wie er aktuell beim Funktionsaufruf ist.

## NullProcessor

Nimmt das LogRecord Objekt so wie es kommt und liefert es unverändert zurück. Dieser Processor ist eigentlich Quatsch, aber er wird intern für die UnitTests benötigt.

## WebProcessor

Erst dachte ich, dieser Processor passt den LogRecord so an, dass die Logs aussehen wie die Apache Logs. Doch da lag ich falsch. Dieser Processor ruft `GeneralUtility::getIndpEnv('_ARRAY')` auf und das ist echt mal krass. Denn mit dem Schlüsselwort `_ARRAY` werden sämtliche aufbereiteten $_SERVER Einträge protokolliert und diese beinhalten URIs zum DocumentRoot, zum aktuellen Verzeichnis, außerdem die URL Query, absolute Pfade, den Host, das aktuelle Script und vieles mehr.
