+++
title = "Hook als Objekt"
date = 2024-01-09T20:49:38+01:00
aliases = ["hook-als-objekt.html"]
+++

Ich hab mal ein bisschen gesucht, um einen Hook zu finden, der nicht überall dokumentiert ist und überall in Foren angefragt wird. Gefunden habe ich den Hook in der Datei `class.t3lib_userauthgroup.php` im Verzeichnis `t3lib`:

```php
// Hook for manipulation of the WHERE sql sentence which controls which BE-groups are included
if (is_array ($GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_userauthgroup.php']['fetchGroupQuery'])) {
    foreach ($GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_userauthgroup.php']['fetchGroupQuery'] as $classRef) {
        $hookObj = t3lib_div::getUserObj($classRef);
        if (method_exists($hookObj, 'fetchGroupQuery_processQuery')) {
            $whereSQL = $hookObj->fetchGroupQuery_processQuery($this, $grList, $idList, $whereSQL);
        }
    }
}
```

Das Erste, was wir sehen ist, dieses `getUserObj`. Damit ist klar, dass wir unseren eigenen PHP-Code als Objekt übergeben müssen. Hier ein Auszug aus meiner `ext_localconf.php`, wie ich auf diesen Hook zugreife:

```php
$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_userauthgroup.php']['fetchGroupQuery'][] 
    = 'EXT:sftesthooks/hooks/class.tx_sftesthooks.php:tx_sftesthooks';
```

Weiterhin sehen wir die Funktion `method_exists`. Damit wird uns eine Funktion vorgegeben, die wir in unserem Objekt haben müssen, um diesen Hook benutzen zu können. Das ist auch der Grund warum wir in der `ext_localconf.php` die Hookverwendung nicht in dieser Form

```php
HOOK = [PATH]:[Objekt]->[Funktion]
```

sondern nur diese Form

```php
HOOK = [PATH]:[Objekt]
```

verwenden dürfen.

Was ihr auch beachten müsst, ist, dass die vorgegebene Funktion etwas zurückgibt: nämlich `$whereSQL`. Heißt: Wir müssen das Ergebnis unserer Funktion per return zurückgeben.

## Was macht dieser Hook hier?

Dieser Hook erweitert den SQL-String, der die richtige Gruppe für einen User herausfinden soll. Ihr könnt mit diesem Hook also auf die Gruppen-ID des aktuell angemeldeten BE-Users zugreifen und sagen, dass er jetzt nicht nur in der Gruppe mit der ID 1 ist, sondern zusätzlich auch die Berechtigungen der Gruppe 2 erbt. Aber das ist nur ein Beispiel, das wir hier realisieren wollen.

## Wie nutze ich diesen Hook?

Zuerst müssen wir in der `ext_localconf.php` unserem TYPO3-System sagen, dass wir diesen Hook verwenden wollen:

```php
<?php
if (!defined('TYPO3_MODE')) {
    die ('Access denied.');
}

$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_userauthgroup.php']['fetchGroupQuery'][] 
    = 'EXT:sftesthooks/hooks/class.tx_sftesthooks.php:tx_sftesthooks';
```

Bitte passt den Pfad und den Dateinamen an eure Extensionstruktur an. Wir editieren nun unsere PHP-Datei mit der Klasse, wie ich sie in der `ext_localconf.php` angegeben habe:

```php
<?php
class tx_sftesthooks {
    public function fetchGroupQuery_processQuery($pObj, &$grList, $idList, $whereSQL) {
        $grList = '1,2';
        return preg_replace('/(\w) uid IN \(([0-9,])\) (\w)/i', '\\1 uid IN ('.$grList.') \\3',$whereSQL);
    }
}
```

Ihr seht hier, dass meine Klasse eine Methode beinhaltet, wie sie oben im Hook vorgeschrieben ist: `fetchGroupQuery_processQuery`. Den Funktionsaufruf könnt ihr nahezu komplett per copy&paste in eure Klasse einfügen. Ihr müsst nur dem Parameter `$this` einen anderen Namen geben wie z.B. `$pObj` was so viel bedeutet wie `parent object`. Warum das? Wenn ihr `$this` nicht umbenennt, dann erhaltet ihr die folgende Fehlermeldung im Frontend:

```
Fatal error: Cannot re-assign $this in D:\xampp\htdocs\typo3\typo3conf\ext\sftesthooks\hooks\class.tx_sftesthooks.php on line 3
```

Ich habe mir hier extra dieses Beispiel mit diesem Hook ausgesucht, weil er eine kleine Tücke beinhaltet: Vielleicht habt ihr schon entdeckt, dass der Parameter `$grList` als Referenz angesprochen wird `&$grList`. Das war echt gemein, denn obwohl mein neuer SQL-String korrekt war, hatte mein Test-BE-User immer noch nicht die Berechtigungen der Gruppe mit der UID 2. Wenn ihr bei dem Hook noch ein bisschen nach unten scrollt, werdet ihr feststellen, dass die Gruppen-IDs aus `$grList` nochmal verwendet werden:

```php
$include_staticArr = t3lib_div::intExplode(',', $grList);
```

Deshalb der Parameteraufruf als Referenz. Mit dem `PHP:preg_replace()` ersetze ist den Bereich `uid IN (1)` durch meine neue Gruppenauflistung in `uid IN (1,2)`.

Jetzt endlich haben wir den Hook sauber implementiert und er funktioniert. Ich habe eine BE-Gruppe angelegt, die nichts darf mit der UID 1 und eine Gruppe, die auf die Rootseite zugreifen darf mit der UID 2. Mein Test-User ist aber im BE nur der Gruppe mit der UID 1 zugewiesen. Also dürfte er den Seitenbaum eigentlich gar nicht sehen. Aber Dank diesem Hook kann er trotzdem darauf zugreifen.
