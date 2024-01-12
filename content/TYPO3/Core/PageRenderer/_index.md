+++
title = "Der PageRenderer"
linkTitle = "PageRenderer"
date = 2024-01-06T23:56:07+01:00
slug = "PageRenderer"
+++

Den PageRenderer findet Ihr in der Datei `sysext/core/Classes/Page/PageRenderer.php`. Mithilfe diesen Objektes könnt Ihr RequireJS-Module registrieren und laden oder auch Eure ganzen JavaScript und CSS-Dateien zusammenführen und/oder auch komprimieren. Auch die Verwaltung der Headerdaten wie den title-Tag, den body-Tag und die XML-Deklarationen könnt
Ihr hiermit bestimmen.

Sehr interessant finde ich die Funktionen für die Integration von JavaScript und CSS, die ich hier näher beschreiben möchte. Ich habe mir dazu eine Extension erstellt und im Rootverzeichnis der Extension die Datei `ext_localconf.php` angelegt mit folgendem Inhalt:

```php
<?php
if (!defined ('TYPO3_MODE')) {
    die('Access denied.');
}

if (TYPO3_MODE === 'FE') {
    $renderer = \TYPO3\CMS\Core\Utility\GeneralUtility::makeInstance(\TYPO3\CMS\Core\Page\PageRenderer::class);
    $renderer->addJsInlineCode('effects', 'function hallo(text){alert(text);}hallo("Tag auch");');
    $renderer->addJsInlineCode('effects', 'function hallo(text){alert(text);}hallo("Du mich auch");');
    $renderer->addJsInlineCode('effects', 'function hallo(text){alert(text);}hallo("Nix da");');
}
```

Wenn Ihr nun in Euer Frontend geht, werdet Ihr feststellen, dass statt drei `alert()` Nachrichten nur eine aufpoppt. Die Lösung liegt im Quellcode vom PageRenderer, der nur eine Skriptzuweisung pro Skriptnamen erlaubt. Dazu schauen wir uns die vier Parameter mal genauer an:

**Name**: 
> Ein eindeutiger Name für das angegebene Script

**Block**:
> Hier kommt das JavaScript rein. Kein Dateipfad oder sonst was, nur reines JavaScript
> OHNE die <script>-Tags

**Compress**:
> Soll das Script komprimiert werden oder nicht? Default: TRUE

**ForceOnTop**:
> Soll das Script im Kopf oder im Fußbereich der Webseite eingefügt werden? 
> Default: FALSE

Mit jedem Aufruf dieser Funktion wird überprüft, ob es ein Script mit dem eindeutigen Namen schon gibt. Wenn "ja", dann wird das Script nicht weiter ausgeführt. Hier liegt also der Grund, warum die beiden letzten Scripte meiner `ext_localconf.php` nicht mit eingebunden werden. Ändert in meinem Beispiel mal die Namen und Ihr werdet sehen, das Euch nun drei `alert()` Boxen nerven. Die Lösung mit den eindeutigen Namen ist für Extensionentwickler sehr sinnvoll, da Ihre Extension auch mehrfach auf ein und derselben Seite eingefügt werden könnten. Sehr schnell ist ein und dasselbe Script zwei mal im Header verfügbar und es kommt evtl. zu unvorhergesehenen Fehlermeldungen.

## Was macht Compress?

In meinem Beispiel von oben: Gar nichts! Denn die Komprimierungsfunktion vom `PageRenderer` löscht nur überflüssige Leer- und Absatzzeichen und hat nicht mit diesen Komprimierungsverfahren zu tun, wie Ihr sie in den minimized Versionen von JavaScript-Frameworks kennt. Die Variablennamen bleiben beim TYPO3-eigenen Komprimierer also vollständig erhalten. Versucht es mal:

**ext_localconf.php**

```php
<?php
if (!defined ('TYPO3_MODE')) {
    die('Access denied.');
}

if (TYPO3_MODE === 'FE') {
    $renderer = \TYPO3\CMS\Core\Utility\GeneralUtility::makeInstance(\TYPO3\CMS\Core\Page\PageRenderer::class);;
    $renderer->addJsInlineCode('effects', '
        function hallo(text) {
            alert(text);
        }
        hallo("Tag auch");
    ');
    $renderer->addJsInlineCode('effects1', '
        function hallo(text) {
            alert(text);
        }
        hallo("Du mich auch");
    ');
    $renderer->addJsInlineCode('effects2', '
        function hallo(text) {
            alert(text);
        }
        hallo("Nix da");
    ');
}
```

Mit Komprimierung schaut das Script im Browser so aus:

```html
<script type="text/javascript">
/**/
<!-- 
/*effects*/
function hallo(text){alert(text);}
hallo("Tag auch");
/*effects1*/
function hallo(text){alert(text);}
hallo("Du mich auch");
/*effects2*/
function hallo(text){alert(text);}
hallo("Nix da");
// -->
/**/
</script>
Setzen wir FALSE als dritten Parameter erhalten wir diese Ausgabe:

<script type="text/javascript">
/**/
<!-- 
/*effects*/

    function hallo(text) {
      alert(text);
    }
    hallo("Tag auch");
  

/*effects1*/

    function hallo(text) {
      alert(text);
    }
    hallo("Du mich auch");
  

/*effects2*/

    function hallo(text) {
      alert(text);
    }
    hallo("Nix da");
  

// -->
/**/
</script>
```
