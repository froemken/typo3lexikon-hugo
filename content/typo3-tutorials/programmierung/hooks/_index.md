+++
title = "Was macht ein Hook?"
linkTitle = "Hooks"
date = 2024-01-09T20:49:38+01:00
alwaysopen = false
aliases = ["hooks.html"]
+++

Es gibt zwar eine allgemeine Erklärung auf Wikipedia, aber ich möchte das Thema Hooks in TYPO3 mal mit eigenen Worten aus der Praxis erklären.

Ein Hook ist im Grunde eine geplante Unterbrechung an einer ganz bestimmten Stelle im Programmablauf. TYPO3 hält an dieser Stelle kurz inne und gibt euch die Möglichkeit, eigenen PHP-Code einzuklinken und auszuführen, bevor der normale Ablauf fortgesetzt wird.

Im TYPO3-Core und in vielen Extensions sind an unzähligen Stellen solche Hooks eingebaut. Sie helfen euch dabei, TYPO3 nach euren Wünschen zu erweitern, ohne direkt am Core schrauben zu müssen.

Ich unterscheide Hooks gerne ganz pragmatisch in zwei Kategorien:

1. **Hooks zur Funktionserweiterung:** Damit erweitert ihr bestehende Abläufe um zusätzliche Logik. Das könnte zum Beispiel ein neues Inhaltselement sein, das ihr zusätzlich zu den Standard-Elementen wie Text oder Bild bereitstellen wollt.
2. **Hooks zur Inhaltsveränderung:** Hier bekommt euer Hook ein Datenobjekt oder ein Inhaltselement mit seinen Eigenschaften übergeben. Ihr könnt die Daten prüfen, manipulieren oder die Ausgabe des Elements gezielt anpassen.

## Wann macht ein Hook Sinn?

Ein Hook ist immer dann die richtige Wahl, wenn ihr ein bestehendes Verhalten anpassen oder erweitern wollt, ohne den TYPO3-Core oder fremde Extensions direkt anzufassen.

Ein paar praktische Beispiele:

In der Extension powermail gab es beispielsweise Hooks, die den Prozess genau in dem Moment unterbrechen, in dem ein Formularfeld als HTML generiert wird. Über den Hook lässt sich dann das Feld manipulieren, etwa um einen roten Rahmen, zusätzliche Attribute oder Hinweistexte einzufügen.

In tt_news existierten Hooks, mit denen sich zusätzliche Marker für das Template-Rendering bereitstellen ließen.

Neben Extensions bringt auch der TYPO3-Core selbst unzählige Hooks mit. Damit könnt ihr etwa Formulardaten vor dem Speichern in der Datenbank nochmals validieren, Werte umrechnen oder das Erscheinungsbild einzelner Felder im Backend verändern.

## Wie findet man Hooks?

Wenn ihr Glück habt, ist ein Hook in der Entwickler-Dokumentation sauber beschrieben. Oft müsst ihr euch aber selbst im Quellcode auf die Suche machen.

Der Quellcode von TYPO3 ist glücklicherweise an vielen Stellen gut kommentiert. Entwickler haben Hooks meist direkt im Code dokumentiert. Ihr könnt euer Projekt mit einer IDE oder einem Such-Tool gezielt nach dem Begriff `hook` durchsuchen.

Schauen wir uns dazu mal ein typisches Beispiel aus der damaligen TCEforms-Verarbeitung an:

```php
// Hook: getSingleField_preProcess
foreach ($this->hookObjectsSingleField as $hookObj) {
    if (method_exists($hookObj, 'getSingleField_preProcess')) {
        $hookObj->getSingleField_preProcess($table, $field, $row, $altName, $palette, $extra, $pal, $this);
    }
}
```

Hooks sind im Code fast immer über solche `foreach`-Schleifen aufgebaut. Ein Array (hier `$this->hookObjectsSingleField`) wird durchlaufen. Für jeden Eintrag prüft TYPO3 mit `method_exists()`, ob das Objekt `$hookObj` die Methode `getSingleField_preProcess` besitzt. Ist das der Fall, wird die Methode mit allen Parametern aufgerufen.

Wie ihr seht, gibt diese spezifische Funktion keinen Rückgabewert zurück. Heißt: Wenn ihr Daten verändern wollt, müsst ihr die entsprechenden Parameter in eurer eigenen Methode per Referenz annehmen:

```php
public function getSingleField_preProcess($table, $field, &$row, $altName, $palette, $extra, $pal, &$pObj)
{
    // ...
}
```

Es gibt auch Hooks, bei denen kein fester Methodenname vorgegeben ist, sondern ein komplettes Benutzerobjekt instanziiert wird. Hier ein Beispiel aus TemplaVoila:

```php
// First prepare user defined objects (if any) for hooks which extend this function:
$hookObjectsArr = [];
if (is_array($GLOBALS['TYPO3_CONF_VARS']['EXTCONF']['templavoila']['pi1']['renderElementClass'])) {
    foreach ($GLOBALS['TYPO3_CONF_VARS']['EXTCONF']['templavoila']['pi1']['renderElementClass'] as $classRef) {
        $hookObjectsArr[] = &t3lib_div::getUserObj($classRef);
    }
}
```

Eine weitere sehr verbreitete Variante arbeitet mit `callUserFunction`. Diese Form begegnet euch im TYPO3-Umfeld extrem häufig:

```php
// Call hooks
if (is_array($GLOBALS['TYPO3_CONF_VARS']['EXTCONF']['templavoila']['newcewizard']['forms'])) {
    foreach ($GLOBALS['TYPO3_CONF_VARS']['EXTCONF']['templavoila']['newcewizard']['forms'] as $userFunc) {
        $params = [
            'pObj' => &$this,
            'elements' => &$elements,
        ];
        t3lib_div::callUserFunction($userFunc, $params, $this);
    }
}
```

Bei dieser Variante schreibt euch der Hook keinen Methodennamen vor. Ihr legt euren eigenen Methodennamen fest und erhaltet alle relevanten Variablen gebündelt in einem `$params`-Array.

## Wie registriert man einen Hook?

Unabhängig von der genauen Variante wird ein Hook immer über ein globales Konfigurations-Array registriert. Für Core-Hooks wird üblicherweise `$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']` verwendet, für Extensions meist `$GLOBALS['TYPO3_CONF_VARS']['EXTCONF']`.

Falls ihr euch fragt, wo die Verknüpfung im ersten Beispiel lag: Wenn ihr im Quellcode nach `$this->hookObjectsSingleField` sucht, stoßt ihr auf folgende Initialisierung:

```php
$this->hookObjectsSingleField = [];
if (is_array($GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass'])) {
    foreach ($GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass'] as $classRef) {
        $this->hookObjectsSingleField[] = t3lib_div::getUserObj($classRef);
    }
}
```

Die Registrierung erfolgt typischerweise in der `ext_localconf.php` eurer eigenen Extension:

```php
<?php
defined('TYPO3') || die('Access denied.');

$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass'][]
    = 'EXT:my_extension/Classes/Hooks/TceformsHook.php:Vendor\\MyExtension\\Hooks\\TceformsHook';
```

Achtet unbedingt auf die leere eckige Klammer `[]` am Ende. Damit wird eure Klasse an das bestehende Array angehängt. Vergesst ihr die Klammern, überschreibt ihr alle zuvor registrierten Hooks anderer Extensions an dieser Stelle.

Wie eure Hook-Klasse im Detail aufgebaut sein muss, hängt von der jeweiligen Hook-Art ab. Schaut euch dazu die nachfolgenden Kapitel im Inhaltsverzeichnis an.
