+++
title = "Logging mit TYPO3"
linkTitle = "Log"
date = 2024-01-06T23:26:31+01:00
slug = "log"
alwaysopen = false
+++

Mit der Logging API gibt es seit TYPO3 6.2 einen zentralen Punkt, um Fehlermeldungen, Warnungen und andere Informationen zu protokollieren. Bisher gab es viele unterschiedliche Anlaufstellen, um bestimmte Zustände zu loggen wie z.B. über `GeneralUtility::sysLog()` oder `BackendUserAuthentication::writelog()`. Außerdem könnt ihr mithilfe von diversen Writern entscheiden wohin geloggt werden soll. Z.B. in die Datenbank, auf die Festplatte oder in das syslog des Betriebsystems.

## Verwenden der Logging API

Einen Logger erstellt Ihr üblicherweise mit diesem Einzeiler.

Versucht nach Möglichkeit bitte immer das `__CLASS__` mit anzugeben. Damit erleichtert Ihr es den Webseiteninhabern die Konfiguration Eures Loggers individuell anzupassen.

```php
/** @var $logger \TYPO3\CMS\Core\Log\Logger */
$logger = \TYPO3\CMS\Core\Utility\GeneralUtility::makeInstance('TYPO3\\CMS\\Core\\Log\\LogManager')->getLogger(__CLASS__);
```

## Die Konfiguration

Die Konfiguration wird über die globale Variable `$GLOBALS['TYPO3_CONF_VARS']['LOG']` realisiert. Die Standardkonfiguration findet Ihr in der DefaultConfiguration.php des Cores und könnt diese z.B. über das Backend Modul "Konfiguration" einsehen bzw. überprüfen. Anpassen könnt Ihr die Konfiguration über eine Extension `ext_localconf.php` oder mithilfe der AdditionalConfiguration.php im Verzeichnis `typo3conf`.

Die Logging API kann sehr dynamisch angepasst werden. So habt Ihr zum Beispiel die Möglichkeit pro Klasse einen individuellen Logger zu erstellen. Ihr könnt sagen, dass der Logger in Eurem ActionController der Extension 1 in die Datenbank loggt und der MailService aus Extension 2 in eine Datei loggt. Außerdem könnt Ihr definieren, dass der ActionController erst ab LogLevel WARNING und aufwärts loggt, während der MailServer z.B. ab LogLevel ERROR und aufwärts loggt.

Wenn keine Konfiguration für Eure Klasse angelegt wurde, dann gilt der TYPO3 Standard aus `$GLOBALS['TYPO3_CONF_VARS']['LOG']['writerConfiguration']` der alles ab LogLevel WARNING aufwärts in eine Datei in `typo3temp/logs/` protokolliert.

Wie Ihr in der Standardkonfiguration unten seht, wird der Namespacename der Klasse anhand der Backslashes aufgeteilt. Ja, nahezu zerrupft. Aber das hat auch einen Vorteil: So könnt Ihr definieren, dass alle Klassen im Namespacepfad `\TYPO3\CMS\Core` eine bestimmte Konfiguration verwenden sollen und diese Konfiguration kann dann durch eine detailliertere Angabe wie `\TYPO3\CMS\Core\Cache\` erneut überschrieben werden.

Alternativ zu den Klassennamen könnt Ihr auch eigene Namen verwenden. Jedoch kann es dann passieren, dass Ihr Euch mit anderen Extensionentwicklern bei der Namensgebung in die Quere kommt. Also versucht in diesem Fall einen möglichst eindeutigen Namen zu definieren. Dabei hilft Euch evtl. der Unterstrich. Denn auch dieser hat die gleiche Funktion wie der Backslash und teilt den Namen in Einzelteile auf. Somit greift `\TYPO3\CMS\Core` und `TYPO3_CMS_Core` auf ein und dieselbe Konfiguration zu: `$GLOBALS['TYPO3_CONF_VARS']['LOG']['TYPO3']['CMS']['Core']['writerConfiguration']`

```php
$GLOBALS['TYPO3_CONF_VARS']['LOG'] = array(
    'writerConfiguration' => array(
        \TYPO3\CMS\Core\Log\LogLevel::WARNING => array(
            \TYPO3\CMS\Core\Log\Writer\FileWriter::class => array()
        )
    ),
    'TYPO3' => array(
        'CMS' => array(
            'Core' => array(
                'Resource' => array(
                    'ResourceStorage' => array(
                        'writerConfiguration' => array(
                            \TYPO3\CMS\Core\Log\LogLevel::ERROR => array(
                                \TYPO3\CMS\Core\Log\Writer\FileWriter::class => array(),
                                \TYPO3\CMS\Core\Log\Writer\DatabaseWriter::class => array()
                            )
                        )
                    )
                )
            )
        )
    )
);
```

## Arbeiten mit Processors

Mithilfe von Processors könnt Ihr zusätzliche Daten dem Array für zusätzliche Angaben hinzufügen oder auch bestehende Angaben verändern. Mit der folgenden Konfiguration, definiert über processorConfiguration, fügen Ihr zunächst die maximale Speicherauslastung und dann auch noch die aufbereiteten $_SERVER Daten hinzu. Für die processorConfiguration gilt das gleiche rekursive Verhalten wie bei der writerConfiguration. Das Beispiel bezieht sich nur auf PHP Klassen innerhalb der Extension `sfcategory` mit dem Vendorname `sfroemken`.

Mit der Option logFile gebt Ihr dem FileWriter einen Pfad mit, wohin das Protokoll geschrieben werden soll. Evtl. enthaltene Unterverzeichnisse werden automatisch angelegt. Von der Namensnennung her etwas blöd, aber meine log Datei heißt debug.log und protokolliert erst ab dem Status WARNING in diese Datei.

Auch den Processoren können Optionen mitgegeben werden. So könnt Ihr mit der Option realMemoryUsage dem MemoryPeakUsageProcessor mitteilen, dass nicht nur der verwendete maximale Speicherplatz, sondern der für den PHP Prozess tatsächlich zur Verfügung gestellte maximale Speicher protokolliert wird. Mit der Option formatSize habe ich die Formatierung der zurückgelieferten Bytes abgeschaltet. Aus dem einfachen Grund, weil TYPO3 in dieser Berechnung standardmäßig mit Mibibytes (geteilt durch 1000) und nicht Megabytes (geteilt durch 1024) arbeitet und somit alle Formatierungen folgendermaßen aussehen: 34 Mi oder 545Ki.

Der Webprocessor hat keine Optionen. Da er an 2ter Stelle steht, werden seine generierten Daten hinter die Memory Daten des vorhergehenden Processors angefügt.

```php
$GLOBALS['TYPO3_CONF_VARS']['LOG']['SF']['Sfcategory'] = array(
    'writerConfiguration' => array(
        \TYPO3\CMS\Core\Log\LogLevel::WARNING => array(
            \TYPO3\CMS\Core\Log\Writer\FileWriter::class => array(
                'logFile' => 'typo3temp/logs/sfcategory/debug.log'
            ),
        )
    ),
    'processorConfiguration' => array(
        \TYPO3\CMS\Core\Log\LogLevel::WARNING => array(
            \TYPO3\CMS\Core\Log\Processor\MemoryPeakUsageProcessor::class => array(
                'realMemoryUsage' => true,
                'formatSize' => false
            ),
            \TYPO3\CMS\Core\Log\Processor\WebProcessor::class => array()
        )
    )
);
```

Ausgabe in der debug.log

```shell
Sat, 25 Mar 2017 21:03:21 +0100 [ALERT] request="49abd94a2359f" component="SF.Sfcategory.Controller.CarController": blabla - {"0":"foo","1":"bar","memoryPeakUsage":39583744,"HTTP_HOST":"typo376:8080","TYPO3_HOST_ONLY":"typo376","TYPO3_PORT":"8080","PATH_INFO":"","QUERY_STRING":"id=5",...}
```
