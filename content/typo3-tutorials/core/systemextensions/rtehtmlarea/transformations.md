+++
title = "Transformations"
date = 2024-01-09T20:32:27+01:00
aliases = ["transformations.html"]
+++

Dieses Thema müsst Ihr Euch immer im Hinterkopf behalten, denn hier verstecken sich Funktionen, die man nicht auf anhieb sieht und schnell für eine große Fehlersuche sorgen. Denn Transformations verändern den übergebenen Inhalt in "human-readable" Code.

## Was machen Transformations?

Sagen wir so: Transformations kümmern sich darum die Inhalte aus dem RTE für die Datenbank zu konvertieren. Dasselbe auch von der Datenbank zurück zum RTE. So wird als bekanntestes Beispiel der <a>-Tag als <link>-Tag in der Datenbank gespeichert. Dies ist auch die häuftigste Fehlerquelle für unsere allowTags-Eigenschaften. Denn hier muss bei der Verwendung von Transformations nicht der a-Tag, sondern der link-Tag in die Liste der allowTags-Eigenschaft eingetragen werden. Vergesst Ihr das, wird Euch gar kein Link angezeigt.

Auch gibt es Transformations, die die <p>- und <div>-Tags entfernen, um einen "für Menschen leserlichen Code" zu erzeugen (so die englische Doku). Nur wenn diese <p>- und <div>-Tags Attribute beinhalten, dann werden auch diese inkl. <p>- und <div>-Tag in der Datenbank abgespeichert.

Kurz: Transformations sind dafür da, um den Quellcode leserlicher zu machen.

## Welche Transformations gibt es?

Ich liste hier zwar alle Transformations auf, die es gibt und was sie grundsätzlich machen, aber wenn es jemanden interessiert, was welche Transformation im einzelnen genau macht, den kann ich nur auf die englische Dokumentation für "Transformation Details" verweisen

### ts_transform

Diese Transformation kümmert sich um die Content Elemente "Text" und "Text mit Bild". Angaben bezüglich Text, Schrift und Farbe werden mit dem font-Tag realisiert. Jeder, der schon mal was von css gehört hat wird wissen, dass diese Option nicht in Frage kommen sollte.

### css_transform

Genau wie ts_transform. Allerdings werden hier die h1,h2, ...Tags unverändert an die Datenbank weitergegeben. Dasselbe auch mit Listen wie <OL> und <UL>. Tags wie TYPOLIST und TYPOHEAD werden in Hx und OL/UL umgewandelt, aber nicht andersherum. Der table-Tag wird durch die Angabe von css_transform komplett deaktiviert.

### ts_preserve

Diese Transformation gilt als "deprecated" und wird hier nicht weiter erläutert.

### ts_images

Diese Transformation prüft, ob ein Bild von einer externen Quelle eingebunden wurde. Wenn dem so ist, dann wird das Bild von dieser Quelle heruntergeladen und in dem localen Ordner uploads/ abgelegt. Weiterhin wird überprüft, ob sich die Dateigröße von Bilder verändert hat. Wenn ja, dann werden diese neu berechnet und im uploads-Ordner aktualisiert. Für alle lokalen Bild gilt: Absolute Pfade (http://www.webseite.de/blabla/typo3...) werden in relative Pfade (fileadmin/images/...) umgewandelt.

### ts_links
Diese Transformation konvertiert alle absoluten Pfade eines Links in den TYPO3-konformen link-Tag. So wird z.B. "http://www.webseite.de/index.php?id=2" zu "<link 2>Klasse Seite</link>".

### ts_reglinks

Sollte diese Transformation verwendet werden, passiert das gleiche wie bei ts_links. Nur werden die Links als a-Tag übernommen.

### ts

Dies ist eine Gruppierung mehrerer Transformations und beinhaltet:

- ts_transform
- ts_preserve
- ts_images
- ts_links 

### ts_css

Dies ist eine Gruppierung mehrerer Transformations und beinhaltet:

- css_transform
- ts_images

### ts_links

Es heißt, dass diese Transformationgruppe der neue Standard für Transformations im Bereich des RTE werden soll. Für mich bzw. uns heißt das: Wir sollen diese Gruppierung verwenden und nur diese Gruppe und nix anderes.

Standardmäßig sollte jeder RTE bereits die Transformation "ts_css" verwenden. Im Beispiel von Inhaltselementen wird das folgendermaßen in der TCA gesetzt:

```php
$GLOBALS['TCA']['tt_content']['types']['text']['showitem'] = ...bodytext;Inhalt;;richtext:rte_transform[flag=rte_enabled|mode=ts_css]...
```

Falls dort ein anderer Wert stehen sollte, könnt Ihr die Transformation mit Hilfe der pageTSconfig überschreiben:

```typo3_typoscript
RTE.default.proc.overrideMode = ts_css
```
