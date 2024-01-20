+++
title = "Icons registrieren"
linkTitle = "IconRegistry"
date = 2024-01-09T20:17:26+01:00
slug = "iconregistry"
aliases = ["iconregistry.html", "imaging.html"]
+++

Jeder Extensionentwickler hat die Möglichkeit mit seiner Extension Datenbanktabellen anzulegen und jeder einzelnen Tabelle individuelle Icons zu vergeben. Geht in das Listen-Modul und wählt im Seitenbaum die Seite mit dem TYPO3-Logo aus (PID=0). Im rechten Frame erscheinen einige Tabellen und jede Tabelle hat Ihr eigenes Icon. Auf Seiten mit einer PID > 0 seht Ihr gerade bei der Inhalts-Tabelle (tt_content), dass Ihr sogar je nach verwendetem CType (Text, Bild, Plugin, Formular, ...) unterschiedliche Datensatz-Icons verwenden könnt. Auch bei den Buttons wie "Speichern", "Speichern und schließen" und "Speichern und Anzeigen" werden Icons verwendet. Selbst die TYPO3-Module in der linken Menüleiste haben alle Ihre eigenen individuellen Icons.

{{% notice style="note" title="Wichtig" icon="exclamation" %}}
Es handelt sich in diesem Tutorial nicht um das Einbinden von Bildern. Verwendet dafür bitte den ImageViewHelper von Fluid. Dieser bindet dann Eure Bilder sowohl in Euren Plugins als auch im Inhaltsbereich Eurer Backend-Module ein unter Verwendung der FAL API.
{{% /notice %}}

Bis TYPO3 6 wurde noch mit dem IconUtility gearbeitet, ab TYPO3 7 wurde das IconUtility als deprecated erklärt und die IconRegistry eingeführt. Ab TYPO3 8 gibt es nur noch die IconRegistry.

Warum das geändert wurde? Dafür gibt es mehrere Gründe:

- 2015 wurde auf den ACME in Nürberg entschieden, dass Utility-Klassen nur statische Methoden enthalten dürfen. Da dies jedoch etwas frickelig ist, mit UnitTests will man vermehrt auf Service- und Factory-Klassen umsteigen.
- Mit der Einführung von SVG-Icons musste man vorsichtig sein, da diese Art von "Bildern" völlig anders zu handhaben sind. Sie dürfen nicht einfach an den GIFBUILDER gesendet oder in Graustufen reduziert werden. Man merkte, dass man für jeden Icontyp ein eigenes Iconrendering benötigte.
- Viele Icons werden an mehreren Stellen verwendet. Wenn sich jetzt der Iconpfad ändert, musste man diesen auch an allen anderen verwendeten Positionen ändern. Mit Einführung der IconRegistry erhält jedes Icon einen individuellen Identifier, mit dem die IconFactory später die Ausgabe rendern kann.

Riskiert mal einen Blick in die Datei IconRegistry.php. Ihr werden über 2000 Zeilen Quellcode finden, die nichts anderes tun, als diverse TYPO3-Icons zu registrieren. Eine gute Anlaufstelle, falls Ihr für Eure Extension die TYPO3-Icons verwenden wollt.

Die wichtigste Methode in dieser Klasse ist die registerIcon() und wird aus Eurer ext_localconf.php heraus aufgerufen:

```php
if (TYPO3_MODE === 'BE') {
    /** @var \TYPO3\CMS\Core\Imaging\IconRegistry $iconRegistry */
    $iconRegistry = \TYPO3\CMS\Core\Utility\GeneralUtility::makeInstance(\TYPO3\CMS\Core\Imaging\IconRegistry::class);
    $iconRegistry->registerIcon(
        'content-elements-prismjs',
        \TYPO3\CMS\Core\Imaging\IconProvider\SvgIconProvider::class,
        ['source' => 'EXT:typo3lexikon/Resources/Public/Icons/prism-js.svg']
    );
}
```

Erklärung der Parameter
: **Erster Parameter**: Identifier: Vergebt hier einen beliebigen eindeutigen Namen für Euer Icon. Ich empfehle den Identifier immer klein zu schreiben und Wörter mittels Bindestrich zu trennen, wie es auch der TYPO3-Core macht.
: **Zweiter Parameter**: IconProvider: PHP-Klassen, die sich um das Renderingverhalten des Icons kümmert. Weitere Erklärung siehe unten.
: **Dritter Parameter**: Optionen: Je nach IconProvider. Üblicherweise wird mittels der source-Eigenschaft der Iconpfad angegeben. Im Falle des FontawesomeIconProviders muss eine Eigenschaft name existieren

## Das Renderingverhalten beeinflussen

Je nach Icontyp wird ein anderes Renderingverhalten benötigt. So braucht der FontawsomeIconProvider z.B. einen SPAN-Tag, während die beiden anderen Provider den IMG-Tag benötigen.

### BitmapIconProvider

Das Wort Bitmap ist hier etwas irritierend, denn mit diesem IconProvider können nicht nur bmp-Icons, sondern alle möglichen Arten von Icons wie jpg, jpeg, bmp und gif verwendet werden.

### FontawesomeIconProvider

Icons können auch mittels einer Schriftart eingebunden werden. TYPO3 läd dazu im Backend automatisch die Font Awesome Bibliothek. Alle Icons können mittels [Identifier](http://fontawesome.io/icons/) direkt angesprochen werden.

### SvgIconProvider

Spezieller IconProvider für SVG-Icons

### Eigene IconProvider

Als zweiten Parameter könnt Ihr natürlich auch die Klasse Eures eigenen IconProviders verwenden. Wichtig ist nur, dass Eure Klasse vom IconProviderInterface erbt.

## Bilder in TYPO3 Backend Modulen anzeigen lassen

Normalerweise kommt Ihr mit der IconFactory nur sehr selten in Kontakt. Denn bei viele Konfigurationen wie beim newContentElementWizard, den TCA-Icons oder den Modul-Icons könnt Ihr direkt mit den Icon-Identifiern arbeiten. Einige Konfigurationen, wie beim newContentElementWizard, lassen Icon-Pfade nicht mals mehr zu.

{{% notice style="info" title="Info" icon="exclamation" %}}
Der TYPO3-Core will nicht mehr, dass bestimmte Einstellungen über mehrere Wege hinweg konfiguriert werden können. Deshalb werden derzeit viele Möglichkeiten als deprecated markiert und mit den nächsten Versionen entfernt. Auch hier im Bereich der Icons merke ich, dass die Angabe von Icon-Pfaden nicht immer funktioniert. Von daher empfehle ich Euch, sofern möglich, alle Eure Bilder mit der IconRegistry zu registrieren, damit Eure Icons auch beim nächsten Upgrade weiterhin erscheinen.{{% /notice %}}

Ladet die IconFactory wie folgt und ruft dann die render() Methode des Icons auf:

```php
$iconFactory = GeneralUtility::makeInstance(IconFactory::class);
$renderedIcon = $iconFactory->getIcon('actions-system-cache-clear-impact-low', Icon::SIZE_SMALL)->render());
```

## Die Icon-Klasse

Mit der getIcon() Methode aus der IconFactory erhaltet Ihr zunächst ein Icon-Objekt. Damit daraus ein gerendeter HTML-Quelltext wird, muss noch die render() Methode aufgerufen werden.

getIcon() hat 4 Parameter
: **$identifier**: Der eindeutige Name des Icons
: **$size**: Icons sind üblicherweise quadratisch. Somit bezieht sich diese Angabe hier sowohl auf die Breite als auch auf die Höhe. Standard sind 32 Pixel. Ihr könnt entweder die 3 Vorgaben Icon::SIZE_SMALL (16 Pixel), Icon::SIZE_DEFAULT (32 Pixel) oder Icon::SIZE_LARGE (64 Pixel) verwenden. Natürlich könnt Ihr hier auch individuelle Werte verwenden.
: **$overlayIdentifier**: Wenn Ihr eine Seite versteckt, dann erscheint auf dem Seiten-Icon im Seitenbaum unten rechts ein kleines Durchfahrtsverboten-Schild. Hierbei handelt es sich um einen Overlay: Ein Icon, dass ein anderes Icon überlagert. Solche Icons sollten Transparenzen aufweisen, damit das darunter liegende Icon weiterhin zu bestimmten Teilen sichtbar ist. Gebt hier den Identifier eines solchen Icons an.
: **$state**: Wenn Icons auf Buttons angezeigt werden und dieser Button als deaktiviert dargestellt wird, sollte auch das Icon etwas halb-transparent dargestellt werden. Dafür gibt es die IconState, die Icons als disabled darstellen kann.
