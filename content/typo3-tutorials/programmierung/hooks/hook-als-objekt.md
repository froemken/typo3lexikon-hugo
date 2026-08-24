+++
title = "Hook als Objekt"
date = 2024-01-09T20:49:38+01:00
aliases = ["hook-als-objekt.html"]
+++

Ich habe gezielt nach einem Hook gesucht, der nicht an jeder Ecke in Foren zitiert wird. Fündig geworden bin ich in der Datei `class.t3lib_userauthgroup.php` im Verzeichnis `t3lib`:

```php
// Hook for manipulation of the WHERE sql sentence which controls which BE-groups are included
if (is_array($GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_userauthgroup.php']['fetchGroupQuery'])) {
    foreach ($GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_userauthgroup.php']['fetchGroupQuery'] as $classRef) {
        $hookObj = t3lib_div::getUserObj($classRef);
        if (method_exists($hookObj, 'fetchGroupQuery_processQuery')) {
            $whereSQL = $hookObj->fetchGroupQuery_processQuery($this, $grList, $idList, $whereSQL);
        }
    }
}
```

Das Erste, was hier auffällt, ist der Aufruf von `t3lib_div::getUserObj()`. Damit ist klar: Wir müssen unseren eigenen PHP-Code als Objekt übergeben.

Hier die Registrierung in der `ext_localconf.php`:

```php
$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_userauthgroup.php']['fetchGroupQuery'][]
    = 'EXT:sftesthooks/hooks/class.tx_sftesthooks.php:tx_sftesthooks';
```

Im Code des Hooks sehen wir außerdem `method_exists()`. Damit schreibt TYPO3 uns exakt vor, welche Methode unsere Hook-Klasse bereitstellen muss. Das ist auch der Grund, warum wir in der `ext_localconf.php` keinen Methodennamen anhängen dürfen.

Verboten ist:

```php
HOOK = [PFAD]:[Objekt]->[Methode]
```

Pflicht ist in diesem Fall:

```php
HOOK = [PFAD]:[Objekt]
```

Ein wichtiger Punkt: Die vorgegebene Methode erwartet einen Rückgabewert, in diesem Fall `$whereSQL`. Heißt: Wir müssen das Ergebnis unserer Anpassung zwingend per `return` an den Aufrufer zurückgeben.

## Was macht dieser Hook genau?

Dieser Hook erweitert den SQL-String, der die Backend-Gruppen eines Benutzers ermittelt. Ihr könnt hier also die Gruppen-Zuordnung des aktuell angemeldeten Backend-Users abgreifen und festlegen, dass dieser zusätzlich zu Gruppe 1 auch die Berechtigungen von Gruppe 2 erbt.

## Wie nutze ich diesen Hook?

Zuerst registrieren wir den Hook in der `ext_localconf.php`:

```php
<?php
defined('TYPO3') || die('Access denied.');

$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_userauthgroup.php']['fetchGroupQuery'][]
    = 'EXT:sftesthooks/hooks/class.tx_sftesthooks.php:tx_sftesthooks';
```

Passt den Pfad und den Klassennamen an eure Extension-Struktur an. Nun erstellen wir die entsprechende Hook-Klasse:

```php
<?php

class tx_sftesthooks
{
    public function fetchGroupQuery_processQuery($pObj, &$grList, $idList, $whereSQL)
    {
        $grList = '1,2';
        return preg_replace('/(\w) uid IN \(([0-9,])\) (\w)/i', '\\1 uid IN (' . $grList . ') \\3', $whereSQL);
    }
}
```

Wie ihr seht, enthält die Klasse genau die vom Hook vorgegebene Methode `fetchGroupQuery_processQuery`. Die Methoden-Signatur könnt ihr im Grunde direkt übernehmen. Eine Kleinigkeit müsst ihr jedoch beachten: Der Parameter `$this` aus dem Core-Aufruf muss in eurer Methode umbenannt werden, etwa in `$pObj` (Parent Object). Belasst ihr `$this` im Methodenkopf, quittiert PHP das mit einem Fatal Error:

```
Fatal error: Cannot re-assign $this in .../class.tx_sftesthooks.php on line 3
```

Ich habe dieses Beispiel auch deshalb gewählt, weil hier eine Stolperfalle lauert: Der Parameter `$grList` wird als Referenz übergeben (`&$grList`). Das ist entscheidend: Obwohl der manipulierte SQL-String bereits passte, besaß der Test-User im Backend zunächst immer noch nicht die Rechte der Gruppe 2.

Der Grund zeigt sich ein paar Zeilen weiter unten im Core-Code:

```php
$include_staticArr = t3lib_div::intExplode(',', $grList);
```

TYPO3 verwendet `$grList` an späterer Stelle erneut. Wenn wir `$grList` in unserem Hook nicht per Referenz anpassen, greift die nachfolgende Logik weiterhin auf die alten Gruppen-IDs zurück.

Mit `preg_replace()` ersetzen wir `uid IN (1)` durch unsere erweiterte Liste `uid IN (1,2)`. 

Im Test-Setup sah das Ergebnis folgendermaßen aus: Gruppe 1 besaß keinerlei Rechte, Gruppe 2 hatte Zugriff auf den Root-Knoten im Seitenbaum. Der Test-User war in der Datenbank ausschließlich Gruppe 1 zugewiesen. Dank des Hooks konnte er im Backend dennoch auf den Seitenbaum zugreifen.
