+++
title = "Hook mit Funktion"
date = 2024-01-09T20:49:38+01:00
aliases = ["hook-mit-funktion.html"]
+++

Hier schauen wir uns den Hook `getSingleField_beforeRender` aus der ehemaligen Core-Datei `class.t3lib_tceforms.php` an.

Wenn die Abarbeitung im Code an dieser Stelle angekommen ist, stehen alle Formulardaten bereit für den letzten Schritt: die eigentliche HTML-Generierung der Backend-Felder. Genau hier wollen wir ansetzen. Als anschauliches Beispiel setzen wir dem Formularfeld `header` pauschal den Text `"Header: "` voraus. Das macht in der Praxis zwar wenig Sinn, zeigt aber perfekt, wie dieser Hook-Typ funktioniert.

So sieht der Hook im TYPO3-Core aus:

```php
// Hook: getSingleField_beforeRender
foreach ($this->hookObjectsSingleField as $hookObject) {
    if (method_exists($hookObject, 'getSingleField_beforeRender')) {
        $hookObject->getSingleField_beforeRender($table, $field, $row, $PA);
    }
}
```

Wenn wir im Core-Code nach `$this->hookObjectsSingleField` suchen, stoßen wir auf das zugehörige Konfigurations-Array:

```php
$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass']
```

Im Hook wird per `method_exists()` geprüft, ob unsere Hook-Klasse eine Methode namens `getSingleField_beforeRender` besitzt. Da die Methode somit fest vorgegeben ist, dürfen wir in der `ext_localconf.php` keinen eigenen Methodennamen anfügen.

Unsere `ext_localconf.php` sieht daher wie folgt aus:

```php
<?php
defined('TYPO3') || die('Access denied.');

$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass'][]
    = 'EXT:sftesthooks/hooks/class.tx_sftesthooks.php:tx_sftesthooks';
```

Da `$this` im Core-Aufruf nicht als Parameter an die Hook-Methode übergeben wird, müssen wir auch keine Variable umbenennen. Wir können die Methoden-Signatur eins zu eins übernehmen.

Schauen wir uns zunächst an, was in der Variable `$PA` steckt:

```php
<?php

class tx_sftesthooks
{
    public function getSingleField_beforeRender($table, $field, $row, $PA)
    {
        echo $field . '<br />';
    }
}
```

Wenn ihr nun ein beliebiges Inhaltselement im Backend bearbeitet, erhaltet ihr eine Auflistung aller verarbeiteten Felder. Der Hook wird nacheinander für jedes einzelne Feld aufgerufen. Heißt: Wir müssen in unserem PHP-Code gezielt auf das Feld `$field === 'header'` filtern.

Gehen wir einen Schritt weiter und lassen uns die Struktur von `$PA` speziell für das Feld `header` ausgeben:

```php
<?php

class tx_sftesthooks
{
    public function getSingleField_beforeRender($table, $field, $row, $PA)
    {
        if ($field === 'header') {
            print_r($PA);
        }
    }
}
```

In dem ausgegebenen Array finden wir unter anderem den Schlüssel `itemFormElValue`. Dieser enthält den aktuell eingetragenen Wert der Überschrift.

Nun wollen wir diesen Wert verändern. Da die Core-Methode keinen Rückgabewert per `return` verarbeitet, müssen wir den zu verändernden Parameter per Referenz annehmen. In unserem Fall ist das `$PA`:

```php
<?php

class tx_sftesthooks
{
    public function getSingleField_beforeRender($table, $field, $row, &$PA)
    {
        if ($field === 'header') {
            $PA['itemFormElValue'] = 'Header: ' . $PA['itemFormElValue'];
        }
    }
}
```

Wir greifen auf den Wert der Überschrift zu, stellen unser Präfix voran und schreiben das Ergebnis zurück in das Referenz-Array `$PA`.

Wenn ihr danach ein Inhaltselement im Backend zum Bearbeiten öffnet, ist allen Überschriften der Text `"Header: "` vorangestellt.
