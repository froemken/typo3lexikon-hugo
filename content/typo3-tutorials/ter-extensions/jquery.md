+++
title = "jquery"
date = 2024-01-09T20:51:35+01:00
aliases = ["jquery.html"]
+++

`jquery` ist die am meisten heruntergeladene TYPO3-Extension, wenn nach dem Suchbegriff `jquery` gesucht wird. Derzeit führt sie noch mit über 400 mehr Downloads als "jQuery Thickbox". Allerdings wurde `jquery` seit dem 04.02.2008 nicht mehr aktualisiert und arbeitet somit immer noch mit der jquery-Version 1.2.2, obwohl 1.4 grade frisch herausgekommen ist. Zusätzlich zur 1.2.2 werden noch die Versionen 1.1.4 und 1.2.1 mitgeliefert.

Was ist sehr gut finde ist, dass `jquery` sämtliche Plugins wie Tabs, Drag, Sortables und viele mehr direkt mitliefert. Außerdem könnt ihr selbst entscheiden, ob diese Dateien komprimiert oder unkomprimiert auf Eurer Seite eingebunden werden sollen.

Noch während der Installation muss die Extension `jsmanager` mitinstalliert werden. Auch diese Extension ist von Jörg Schoppet und bringt weder eine Dokumentation mit noch ist diese Extension in irgendeiner Weise im Repository beschrieben. Ein kurzer Blick in den Quellcode könnte darauf hindeuten, dass hiermit JavaScript-Dateien in die Webseite integriert werden. Falls einige JS-Dateien doppelt sind (bei z.B. mehreren Plugins auf einer Seite), dann kümmert sich diese Extension darum, die doppelt geladenen JS-Dateien wieder zu entfernen. Ich persönlich finde das etwas übertrieben und würde hier eher mit der Funktion `uniqueInList` aus der `t3lib_div` Klasse arbeiten.

Der erste Versuch das Beispiel von der jquery-Dokumentation einzubinden schlug fehl. Sämtliche Anführungszeichen wurden von Word oder Openoffice in die Dokumentation kopiert und werden nicht als "echte" Anführungszeichen erkannt. Statt

```html
<ul id="firstlist">
```

steht

```html
<ul id=”firstlist”>
```

in der Dokumentation. Nach dieser Problembehebung erschien nun diese Fehlermeldung im Frontend:

Fatal error: Call to undefined function rount() in /var/www/test/typo3conf/ext/sfjquerytest/pi1/class.tx_sfjquerytest_pi1.php on line 70

Hier hat der Programmierer wohl die PHP-Funktion "round" gemeint. Danach funktioniert das Beispiel zu 90%. Immerhin wird es angezeigt, aber im Frontend erscheinen noch ein paar HTML-Fehler. Dies liegt an der fehlerhaften Zeile im PHP-Code:

```php
$secondList = '<ul id="secondlist"> . "\n"';
```

Hier befindet sich das \n innerhalb von einfachen Anführungszeichen und ich denke die Zeile sollte wohl so lauten:

```php
$secondList = '<ul id="secondlist">' . "\n";
```

Weiter unten habe ich noch diese Zeile gefunden:

```php
$secondList .= '/ul>' . "\n";
```

Da fehlt noch eine Klammer und muss so geändert werden:

```php
$secondList .= '</ul>' . "\n";
```

Ab jetzt funktioniert das Beispiel zu 95%. Die restlichen 5% sind für mich Fehler in der Programmierung an sich. Denn laut Quellcode schaut es so aus als wolle der Programmierer zwei Listen erstellen. Da ich aber nur eine Liste im Frontend sehe...ist da wohl irgendwas schief gelaufen. Dann habe ich noch einen Fehler entdeckt, der die aktuelle Zeile während des Sortierens in die Mitte der Webseite katapultiert (siehe Bild).
Konfiguration

jquery ist keine eigenständige Extension und arbeitet eher wie eine API für eigene Extensions. Deshalb muss in jeder Extension, die jquery verwenden will, zuerst eine jquery-PHP-Datei eingebunden werden:

```php
require_once(t3lib_extMgm::extPath("jquery")."class.tx_jquery.php");
```

Im nächsten Schritt (function main()) müsst ihr dann angeben welche Plugins ihr für Eure Extension zur Verfügung stehen haben wollt:

```php
tx_jquery::setPlugins(array('iutil','idrag','idrop','isortables'));
```

Die Dokumentation listet im Abschnitt Plugins nur drei Plugins auf und das, obwohl sich im Plugins-Ordner sich ca. 35 Plugins tummeln.

Jetzt wo die Plugins ausgewählt wurden, müsst ihr nun mit folgender Funktion die Plugins für Eure Webseite verfügbar machen:

```php
tx_jquery::includeLib();
```

Ab jetzt kommt nur noch Chaos. JavaScript, PHP, HTML und jQuery werden nun in einer Datei "gemischt". Keine Trennung zwischen domready und Funktionen außerhalb von domready sind vorhanden und müssen selbst programmiert werden. Meine Testdatei schaut nach den Änderungen mitlerweile so aus:

```php
<?php
require_once(PATH_tslib.'class.tslib_pibase.php');
require_once(t3lib_extMgm::extPath("jquery")."class.tx_jquery.php");

class tx_sfjquerytest_pi1 extends tslib_pibase {
    var $prefixId      = 'tx_sfjquerytest_pi1';        // Same as class name
    var $scriptRelPath = 'pi1/class.tx_sfjquerytest_pi1.php';    // Path to this script relative to the extension dir.
    var $extKey        = 'sfjquerytest';    // The extension key.
    var $pi_checkCHash = true;
    
    /**
     * returns a html-code with two sortable lists
     */
    function main($content,$conf){
        // only include the scripts, that are necessary
        tx_jquery::setPlugins(array('iutil','idrag','idrop','isortables'));
        tx_jquery::includeLib();
        $content = '';
        $firstList = '<ul id="firstlist">' . "\n";
        $secondList = '<ul id="secondlist">' . "\n";
        
        // define the list-content
        $listArray = array(
            'Item 1',
            'Item 2',
            'Item 3',
            'Item 4',
            'Item 5',
            'Item 6',
            'Item 7',
        );
        
        // the list items should be split on two lists
        $fieldCount = round(count($listArray)/2);
        $counter = 0;
        foreach ($listArray as $listItem){
            if ($counter < $fieldCount){
                $listName = 'firstList';
            } else {
                $listName = 'secondList';
            }
            $$listName .= '<li class="sortable">' . $listItem . '</li>' . "\n";
        }
        $firstList .= '</ul>' . "\n";
        $secondList .= '</ul>' . "\n";
        $content = '<div id="sortables">' . "\n"
        . $firstList
        . $secondList
        . '</div>' . "\n";
        $content .= '<script type="text/javascript">' . "\n"
        . '// <![CDATA[' . "\n"
        . "$('#firstlist').Sortable({accept:'sortable', fit:true, tolerance:'intersect', floats:false});" . "\n"
        . "$('#secondlist').Sortable({accept:'sortable', fit:true, tolerance:'intersect', floats:false});" . "\n"
        . '// ]]>' . "\n"
        . '</script>' . "\n";
        
        return $this->pi_wrapInBaseClass($content);
    }
}

if (defined('TYPO3_MODE') && $TYPO3_CONF_VARS[TYPO3_MODE]['XCLASS']['ext/sfjquerytest/pi1/class.tx_sfjquerytest_pi1.php'])    {
    include_once($TYPO3_CONF_VARS[TYPO3_MODE]['XCLASS']['ext/sfjquerytest/pi1/class.tx_sfjquerytest_pi1.php']);
}
```
