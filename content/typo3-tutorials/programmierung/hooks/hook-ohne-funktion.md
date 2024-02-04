+++
title = "Hook ohne Funktion"
date = 2024-01-09T20:49:38+01:00
aliases = ["hook-ohne-funktion.html"]
+++

Hier stelle ich euch den Hook `render_preProcess` vor, der sich in der Datei `class.t3lib_pagerenderer.php` befindet. Der Kommentar beinhaltet zwar das Wort `hook`, damit er sich auffinden lässt, aber wirklich aussagefähig ist er nicht. Ich habe den Hook einfach ausgewählt und während ich das hier schreibe, weiß ich selbst noch nicht was er macht.

Wenn ich mir die Parameter anschaue, lese ich Worte wie `header`, `js` und `css`. Meine erste Vermutung lässt darauf schließen, das dieser Hook etwas mit den Kopfzeilen unseres HTML-Templates zu tun hat. Evtl. kann man mit diesem Hook eigene `css` bzw. `js` Dateien und/oder JS-Code im Header einfügen.

```php
// preRenderHook for possible manuipulation
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
        'cssInline' => &$this->cssInline
    );
    
    foreach ($GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_pagerenderer.php']['render-preProcess'] as $hook) {
        t3lib_div::callUserFunction($hook, $params, $this);
    }
}
```

Der Aufruf über `callUserFunction` weist uns darauf hin, dass wir unseren Eintrag in der `ext_localconf.php` mit einem eigenen Funktionnamen versehen müssen:

```php
$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_pagerenderer.php']['render-preProcess'][] 
    = 'EXT:sftesthooks/hooks/class.tx_sftesthooks.php:tx_sftesthooks->includeJS';
```

Der Aufruf muss in folgender Struktur erfolgen:

```php
Hook = '[Pfad]:[Objekt]->[Funktionsname]'
```

Ich habe mich hier für den Funktionsnamen `includeJS` entschieden, weil ich vorhabe eine nervige Alert-Box auf meine Webseite zu setzen. Ist ja Gott-Sei-Dank nur ein Beispiel. Mal schauen, ob's klappt:

Lasst uns in unsere PHP-Datei erstmal Folgendes eintragen:

```php
<?php
class tx_sftesthooks {
    public function includeJS($params, $pObj) {
        print_r($params);
    }
}
```

Wir sehen nach einen ClearCache, dass sich diese Ausgabe nicht nur im Frontend, sondern auch im Backend auswirkt. Weiterhin erkennen wir, dass die Angaben als Arrays fungieren. Mit diesen Informationen bauen wir unsere Datei nun folgendermaßen um:

```php
<?php
class tx_sftesthooks {
    public function includeJS($params, $pObj) {
        if (TYPO3_MODE === 'FE') {
            $params['jsInline']['nervigeMeldung'] = 'alert("Ich nerve ab jetzt jedes mal, wenn die Webseite aufgerufen wird");';
        }
    }
}
```

So, ab nun wird der Code nur noch im Frontend ausgeführt und wir fügen unser Script als Array ein. Aber es klappt immer noch nicht. Also müssen wir nochmal einen Blick in den Hook machen und ein wenig herunterscrollen bis wir folgenden Eintrag finden:

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

Jetzt sehen wir `$properties['code']`. Na super, noch ein Array! Also ab in unsere PHP-Datei und noch mal ändern:

```php
<?php
class tx_sftesthooks {
    function includeJS($params, $pObj) {
        if (TYPO3_MODE === 'FE') {
            $params['jsInline']['nervigeMeldung']['code'] = 'alert("Ich nerve ab jetzt jedes mal, wenn die Webseite aufgerufen wird");';
        }
    }
}
```

und endlich erscheint die nervige Meldung auf unserer Webseite.
