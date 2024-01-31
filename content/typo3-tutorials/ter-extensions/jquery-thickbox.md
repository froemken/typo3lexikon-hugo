+++
title = "jquery_thickbox"
date = 2024-01-09T20:51:52+01:00
aliases = ["jquery-thickbox.html"]
+++

Die Dokumentation von `jquery_thickbox` ist sehr schlicht, eine Seite groß und beinhaltet keine Bilder. Aber immerhin steht dort, dass mit dieser Extension die `jquery` libraries und das Plugin Thickbox eingebunden wird. Ein Beispielbild ist nicht vorhanden und unter thickbox konnte ich mir auch nichts vorstellen.

Bei dieser Extension handelt es sich um eine Slimbox bzw. Lightbox, wie wir sie von vielen anderen Extensions her kennen. Ihr braucht diese Extension nur zu installieren und nachdem ihr das statische Template eingebunden habt, könnt ihr im Content Element `Bild` und `Text mit Bild` mit der Checkbox für Click-enlargement die Thickbox aktivieren.

Nachteil: Ich habe in dem Quellcode der `jquery_thickbox` nur diese Funktion gefunden:

```php
function main($content,$conf) {
    $GLOBALS['TSFE']->additionalHeaderData['tx_jquerythickbox_inc'] = '
    <link rel="stylesheet" href="'.t3lib_extMgm::siteRelPath($this->extKey).'thickbox/thickbox.css" type="text/css" />
    <script type="text/javascript" src="'.t3lib_extMgm::siteRelPath($this->extKey).'jquery/jquery.js"></script>
    <script type="text/javascript" src="'.t3lib_extMgm::siteRelPath($this->extKey).'thickbox/thickbox.js"></script>';
}
```

Damit sollte klar sein, dass sich die Schaltflächen für weiter, zurück und viele andere Optionen für Breite, Höhe sich mithilfe von Konstanten im statischen Template NICHT ändern lassen.
