+++
title = "Das TYPO3 Caching Framework"
linkTitle = "Cache"
date = 2024-01-06T19:10:57+01:00
slug = "cache"
weight = 2
alwaysopen = false
+++

Bei dem Caching Framework handelt es sich um eine einheitliche API, um Daten 
zwischenzuspeichern. Dabei ist es völlig egal, wie (Frontend) und wo (Backend) die Daten
abgespeichert werden. Jeder Extensionentwickler hat die Möglichkeit einen Cache zu
erstellen und eine Standardkonfiguration dafür mitzuliefern. Schlussendlich obliegt es
aber dem TYPO3-Administrator, ob er Eure Cache-Konfiguration übernimmt, oder das Backend
auf ein anderes Speichersystem wie APCu umstellt. Die Programmierung der Extension muss 
aufgrund der einheitlichen Methodennamen der API nicht angepasst werden.

Das TYPO3 Caching Framework wurde im Rahmen von Flow programmiert und steht Euch seit 
TYPO3 4.3 zur Verfügung. Damals musste das Caching Framework noch über das Installtool 
explizit aktiviert werden. Mit TYPO3 4.6 wurde das Caching Framework zum Standard und die
Option im Installtool entfernt. Seitdem wurde das Caching Framework immer weiter entwickelt
und neue Backends hinzugefügt.

## Wie richte ich einen eigenen Cache ein?

Folgende 3 Zeilen in der `ext_localconf.php` erzeugen einen Cache mit dem Namen 
cache_what_ever. Als Standard wird das VariableFrontend und das Typo3DatabaseBackend 
verwendet:

```php
<?php
if (empty($GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_what_ever'])) {
    $GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_what_ever'] = [];
}
```

## Arbeiten mit dem Cache Frontend

Mit dem Cache Frontend wird entschieden, welche Daten zu speichern sind. Am Häufigsten 
wird das VariableFrontend verwendet, weil damit sowohl Texte, Zahlen, Objekte und Arrays
abgespeichert werden können. Jedoch erfordert dies den zusätzlichen Aufruf von
`serialize()`/`unserialize()`, was das Lesen und Schreiben vom Cache minimal verlangsamt.
Etwas schneller ist das StringFrontend. Dies beinhaltet zwar kein `serialize()` mehr, kann
deshalb jedoch nur noch Texte speichern. Array und Objekte sind hier nicht mehr möglich.

Beachtet bitte, dass eine Umstellung vom VariableFrontend auf das StringFrontend zu
erheblichen Problemen führen kann. Ihr müsst zunächst sicherstellen, dass über den zu
ändernden Cache wirklich nur Texte gespeichert werden. Eine Umstellung von StringFrontend
zu VariableFrontend würde hingegen problemlos funktionieren.

```php
// Cache erstellen. VariableFrontend wird als Standard verwendet.
if (!is_array($GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_what_ever'])) {
    $GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_what_ever'] = [];
}

// Cache Frontend überschreiben. Klappt auch für sämtliche anderen Caches.
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_what_ever']['frontend'] = \TYPO3\CMS\Core\Cache\Frontend\StringFrontend::class;
```

## Das Backend eines Caches verändern

Im Gegensatz zum Frontend ist das Backend etwas unempfindlicher, was Änderungen 
anbelangt. Klar, ein Backend, das zum Laden von PHP Dateien verwendet wird (require_once)
kann nicht auf das ApcuBackend oder das TransientMemoryBackend umgebogen werden.

Im Folgenden setzen wir das Backend des Page-Caches auf das SimpleFileBackend.

Die Optionen eines Backends können je nach Backend unterschiedlich sein. Da wir die
Konfiguration des Typo3DatabaseBackends für das SimpleFileBackend hier nicht anwenden 
können, müssen die Optionen für das Backend (siehe 2te Zeile) zurückgesetzt oder je 
nach Backend sogar völlig neu gesetzt werden.

```php
# Bis TYPO3 9:
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_pages']['backend'] = \TYPO3\CMS\Core\Cache\Backend\SimpleFileBackend::class;
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_pages']['options'] = [];

# Ab TYPO3 10 wurde der Prefix "cache_" von den Cachenamen entfernt:
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['pages']['backend'] = \TYPO3\CMS\Core\Cache\Backend\SimpleFileBackend::class;
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['pages']['options'] = [];
```

## Die Cache-Konfiguration von typo3lexikon.de

Für meine Webseite habe ich alle TYPO3 Core Caches auf andere Backends umgestellt.
Die entsprechende Konfiguration habe ich in der AdditionalConfiguration.php vorgenommen:

```php
<?php
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['extbase_reflection']['backend'] = \TYPO3\CMS\Core\Cache\Backend\ApcuBackend::class;
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['extbase_datamapfactory_datamap']['backend'] = \TYPO3\CMS\Core\Cache\Backend\ApcuBackend::class;
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_rootline']['backend'] = \TYPO3\CMS\Core\Cache\Backend\ApcuBackend::class;

$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_hash']['backend'] = \TYPO3\CMS\Core\Cache\Backend\SimpleFileBackend::class;
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_pages']['backend'] = \TYPO3\CMS\Core\Cache\Backend\SimpleFileBackend::class;
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_pages']['options'] = [];
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_pagesection']['backend'] = \TYPO3\CMS\Core\Cache\Backend\SimpleFileBackend::class;
$GLOBALS['TYPO3_CONF_VARS']['SYS']['caching']['cacheConfigurations']['cache_pagesection']['options'] = [];
```
