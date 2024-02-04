+++
title = "Hook mit Funktion"
date = 2024-01-09T20:49:38+01:00
aliases = ["hook-mit-funktion.html"]
+++

Hier stelle ich euch den Hook getSingleField_beforeRender aus der Datei class.t3lib_tceforms.php vor. Es gibt in dieser Datei noch ein paar weitere Hooks, aber wenn der PHP-Parser hier angekommen ist, sind alle Daten bereits verfügbar und fertig für den letzten Schritt: Die Verarbeitung der Informationen, um ein Feld im Backend zu erzeugen. Hier wollen wir ansetzen und dem Feld "header" grundsätzlich das Wort "Header: " voransetzen. Mach zwar keinen Sinn, aber es soll nur ein Beispiel sein und euch zeigen, wie ihr diesen Hook anwendet. Hier der Hook:

```php
// Hook:
function getSingleField_beforeRenderforeach ($this->hookObjectsSingleField as $hookObject) {
    if (method_exists($hookObject, 'getSingleField_beforeRender')) {        
        $hookObject->getSingleField_beforeRender($table, $field, $row, $PA);
    }
}
```

Wie in meiner Einleitung schon geschildert, fehlt uns in diesem Hook das Array, mit dem wir den Hook aufrufen können. Einmal nach `hookObjectsSingleField` gesucht und schon haben wir unser Array gefunden:

```php
$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass']
```

Weiterhin sehen wir `method_exists`. Unser Objekt muss also exakt diesen Funktionsnamen beinhalten. Da wir also keinen eigenen Funktionsnamen angeben dürfen, schaut unsere `ext_localconf.php` ungefähr so aus:

```php
<?php
if (!defined('TYPO3_MODE')) {
    die ('Access denied.');
}

$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass'][] 
    = 'EXT:sftesthooks/hooks/class.tx_sftesthooks.php:tx_sftesthooks';
```

Da `$this` nicht in den Parametern der Funktion enthalten ist, können wir den Funktionsaufruf 1 zu 1 in unsere PHP-Datei einfügen. Ansonsten hätten wir `$this` umbenennen müssen in z.B. `$pObj`. Schauen wir uns zuerst mal an was in `$PA` drin steht. Dazu editieren wir unsere PHP-Datei:

```php
<?php
class tx_sftesthooks {
    function getSingleField_beforeRender($table, $field, $row, $PA) {
        echo $field.'<br />';
    }
}
```

Wenn wir jetzt irgendein Content-Element im Backend bearbeiten, dann erhaltet ihr "im Hintergrund" eine kleine Liste mit allen Feldern, die für dieses Inhaltselement verfügbar sind. Es sollte schnell klar werden, dass wir das Feld `header` benötigen, um die Überschrift zu verändern und wir wissen nun, dass dieser Hook für jedes einzelne Feld durchgeführt wird. Das sollten wir in unserem PHP-Code berücksichtigen.

Gehen wir mal einen Step weiter und schauen uns die Daten der Variable `$PA` an, die nur für unseren `header` vorgesehen sind:

```php
<?php
class tx_sftesthooks {
    function getSingleField_beforeRender($table, $field, $row, $PA) {
        if ($field === 'header') {
            print_r($PA);
        }
    }
}
```

Wenn wir nun wieder irgendein Inhaltselement im Backend bearbeiten erscheint ein Array, dass alle Eigenschaften für unser Feld `header` bereithält. Unter anderem findet ihr dort auch einen Eintrag `itemFormElValue`, der unsere derzeit eingetragene Überschrift beinhaltet. Natürlich ist dieser Eintrag leer, wenn ihr keine Überschrift eingetragen habt :-)

Also wollen wir nun diesen Eintrag bearbeiten. Wir schauen uns den Hook nochmal an und stellen fest, dass dieser keine Daten zurückgibt. Heißt: Wir müssen den Parameter als Referenz übertragen, den wir verändern wollen. Das ist in unserem Fall halt `$PA`:

```php
<?php
class tx_sftesthooks {
    function getSingleField_beforeRender($table, $field, $row, &$PA) {
        if ($field === 'header') {
            $PA['itemFormElValue'] = 'Header: ' . $PA['itemFormElValue'];
        }
    }
}
```

Wir nehmen uns die Überschrift, fügen unser Wort voran ein und übergeben es wieder zurück an die Überschrift Dank Referenz.

Wenn ihr nun wieder ein Inhaltselement öffnet, sollten alle Überschriften dieses `Header: ` vorne stehen haben.
