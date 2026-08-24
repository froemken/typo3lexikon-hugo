+++
title = "Hook ohne Funktion"
date = 2024-01-09T20:49:38+01:00
aliases = ["hook-ohne-funktion.html"]
+++

Hier schauen wir uns den Hook `render-preProcess` aus der Core-Klasse `class.t3lib_pagerenderer.php` an. Der Kommentar im Code verrät zwar das Wort `hook`, liefert aber zunächst wenig Details zur konkreten Wirkungsweise.

Ein Blick auf die übergebenen Parameter mit Bezeichnungen wie `header`, `js` und `css` lässt bereits vermuten: Dieser Hook greift kurz vor der Aufbereitung der HTML-Headerdaten. Damit lassen sich eigene CSS- und JavaScript-Dateien sowie Inline-Code in den Seiten-Header einschleusen.

```php
// preRenderHook for possible manipulation
if (is_array($GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_pagerenderer.php']['render-preProcess'])) {
    $params = [
        'jsLibsCore' => &$jsLibs,
        'jsLibs' => &$this->jsLibs,
        'jsFiles' => &$this->jsFiles,
        'jsFooterFiles' => &$this->jsFiles,
        'cssFiles' => &$this->cssFiles,
        'headerData' => &$this->headerData,
        'footerData' => &$this->footerData,
        'jsInline' => &$this->jsInline,
        'cssInline' => &$this->cssInline,
    ];

    foreach ($GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_pagerenderer.php']['render-preProcess'] as $hook) {
        t3lib_div::callUserFunction($hook, $params, $this);
    }
}
```

Der Aufruf über `t3lib_div::callUserFunction()` ist hier das entscheidende Signal: Wir sind nicht an einen fest vom Core vorgegebenen Methodennamen gebunden, sondern MÜSSEN in der `ext_localconf.php` unseren eigenen Methodennamen mit angeben.

Die Registrierung in der `ext_localconf.php` erfolgt nach folgendem Schema:

```php
$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_pagerenderer.php']['render-preProcess'][]
    = 'EXT:sftesthooks/hooks/class.tx_sftesthooks.php:tx_sftesthooks->includeJS';
```

Das Schema lautet hier:

```php
HOOK = '[PFAD]:[Objekt]->[Methodenname]'
```

In unserem Beispiel wählen wir den Methodennamen `includeJS`. Das Ziel: Eine JavaScript-Alert-Box auf der Webseite ausgeben.

Erstellen wir im ersten Schritt unsere Hook-Klasse mit einer Test-Ausgabe:

```php
<?php

class tx_sftesthooks
{
    public function includeJS($params, $pObj)
    {
        print_r($params);
    }
}
```

Nach dem Leeren des Caches fällt sofort auf: Diese Testausgabe erscheint sowohl im Frontend als auch im Backend, weil der `PageRenderer` in beiden Kontexten zum Einsatz kommt. Außerdem sehen wir im `print_r()`, dass die Parameter als verschachtelte Arrays übergeben werden.

Passen wir den Code an, um die Ausgabe gezielt auf das Frontend zu beschränken:

```php
<?php

class tx_sftesthooks
{
    public function includeJS($params, $pObj)
    {
        if (TYPO3_MODE === 'FE') {
            $params['jsInline']['nervigeMeldung'] = 'alert("Ich nerve ab jetzt jedes Mal beim Seitenaufruf");';
        }
    }
}
```

Beim Test zeigt sich jedoch: Die Alert-Box wird noch nicht ausgegeben. Ein tieferer Blick in die nachfolgenden Zeilen des `PageRenderer` offenbart die Ursache:

```php
if (count($this->jsInline)) {
    foreach ($this->jsInline as $name => $properties) {
        if ($properties['forceOnTop']) {
            if ($properties['section'] === self::PART_HEADER) {
                $jsInline = '/*' . htmlspecialchars($name) . '*/' . chr(10) . $properties['code'] . chr(10) . $jsInline;
            } else {
                $jsFooterInline = '/*' . htmlspecialchars($name) . '*/' . chr(10) . $properties['code'] . chr(10) . $jsFooterInline;
            }
        } else {
            if ($properties['section'] === self::PART_HEADER) {
                $jsInline .= '/*' . htmlspecialchars($name) . '*/' . chr(10) . $properties['code'] . chr(10);
            } else {
                $jsFooterInline .= '/*' . htmlspecialchars($name) . '*/' . chr(10) . $properties['code'] . chr(10);
            }
        }
    }
}
```

Der Core erwartet unter `$properties` ein weiteres Array mit dem Key `code`.

Passen wir unsere Hook-Methode entsprechend an:

```php
<?php

class tx_sftesthooks
{
    public function includeJS($params, $pObj)
    {
        if (TYPO3_MODE === 'FE') {
            $params['jsInline']['nervigeMeldung']['code'] = 'alert("Ich nerve ab jetzt jedes Mal beim Seitenaufruf");';
        }
    }
}
```

Mit dieser Struktur wird der JavaScript-Code korrekt in den PageRenderer eingespeist und die Popup-Meldung erscheint wie gewünscht im Frontend.
