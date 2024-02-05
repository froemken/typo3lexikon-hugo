+++
title = "Tutorial Extbase Extension"
date = 2024-01-09T20:24:02+01:00
ordersectionsby = "linktitle"
alwaysopen = false
slug = "tutorial-extbase-extension"
aliases = ["tutorial-extbase-extension.html", "meine-erste-extbase-extension.html"]
+++

Fluid wird die neue Standard Templatemaschine für TYPO3-Version 5 und steht euch Dank einer aufwändigen Rückportierung auch in TYPO3-Versionen ab 4.3 zur Verfügung. Ich habe mir sämtliche Podcasts und Tutorials angeschaut und festgestellt, dass Fluid sehr häufig anhand einer fertigen Extension erklärt wird. Für jemanden wie mich, der solche Extensions aber von Grund auf selbst erstellen will, sind diese Tutorials nicht sonderlich hilfreich.

Wer keine Erklärungen haben will, kann sich auch meinen Crashkurs zum Thema Fluid anschauen.

Um mich in die neue Materie einzuarbeiten habe ich mir die Extension `blog_example` installiert und bin wirklich jeden Schritt, wie der PHP-Parser auch, jede Zeile durchgegangen, um herauszufinden, wie wo und warum was aufgerufen wird. Nach diesem Durchwühlen kann ich sagen, dass das Prinzip hinter Extbase/Fluid einfach nur genial ist. Ich kann aber auch sagen, dass diese beiden Extensions nicht grade einfach zu erlernen ist. Ohne diese ganze Automatismen zu verstehen, die im Hintergrund ablaufen, werden viele Programmierer Probleme haben zu verstehen, warum die eine oder andere Datei unbedingt diesen Namen haben muss oder warum grade diese Verzeichnisse angelegt werden müssen.

Hier ein kleines Miniglossar:

`Fluid` ist die neue Templateengine

`Extbase` ist ein MVC Framework, dass man als Verbindung zwischen TYPO3 und Extensions bezeichnen kann. So stellt extbase zum Beispiel Funktionen bereit, um eine Extension in TYPO3 zu registrieren, damit ihr sie später im Pluginbereich der Inhaltselemente auswählen könnt. Wenn ich extbase vergleichen müsste, dann würde ich sie am ehesten mit der Datei: `class.t3lib_extmgm.php` aus dem t3lib-Verzeichnis vergleichen.

Mit Extbase greift ihr nicht mehr direkt auf die Datenbank zu. Extbase liest praktisch eure Klassen- und Variablennamen aus und sucht anhand dieser Namen Tabellen mit den gleichen Namen in der Datenbank. Aber dazu später mehr.

In dem folgenden Tutorial werde ich euch nur zeigen wie Extbase/fluid funktioniert und als Ergebnis werden wir nur eine Variable `{name}` in einem Template per Fluid ersetzten. Dieses Tutorial behandelt keine Datenbankintegration!

## Wie erstelle ich so eine Fluid-Extension?

Bevor wir überhaupt mit dem Erstellen einer eigenen Extbase/Fluid-basierenden Extension anfangen können, müssen diese zwei Extensions aktiviert werden. Diese Beiden sind in der 4.3er Version von TYPO3 schon als System-Extensions vorhanden und müssen nur noch im Extensionmanager aktiviert werden:

- extbase
- fluid
- Kickstarter

Im Kickstarter legen wir die "General info", evtl. die "Setup languages" und auf jeden Fall mal eine neue Tabelle an. Wir werden zwar innerhalb dieses Tutorials noch nicht darauf zugreifen, aber in meinem zweiten Tutorial werden wir diese Tabelle benötigen.

Ich nenne meine Tabelle `customer` und füge ihr zwei Felder `vorname` und `nachname` hinzu. Mehr will ich noch nicht machen. ihr soll erstmal Extbase/Fluid kennenlernen, bevor wir uns mit der Datenbankintegration beschäftigen.

Diese Extension speichern wir nun über "View result" -> Write und installieren sie auch gleich. Ein Frontendplugin erstellen wir an dieser Stelle noch nicht.

## Einbindung von Plugins

Wir wollen in diesem Tutorial das neue Extension-Framework Extbase verwenden. Dem zugrunde haben wir im `kickstarter` kein Frontendplugin erstellt, sondern wollen dieses mithilfe von Extbase einbinden. Öffnet dazu bitte die `ext_tables.php` und ihr findet nur die beiden Bereiche mit "Access denied" und der Tabellendefinition für unsere Tabelle.

Fügt nun noch folgenden Code ein:

```php
Tx_Extbase_Utility_Extension::registerPlugin(
    $_EXTKEY,
    'Pi1',
    'Mein erstes Fluid-Template'
);
```

1. Parameter erwartet den Namen unserer Extension. Ein $EXTKEY erspart uns etwas Arbeit

2. Parameter erwartet einen eindeutigen Namen unseres Plugins. Der erste Buchstabe hier muss großgeschrieben werden. Da die 
   meisten Plugins in TYPO3 als pi1, pi2, pi3, ... bezeichnet werden, werden auch wir uns daran halten.

3. Parameter benötigt den Text, der in der Selectbox erscheinen soll.

Das Plugin könnt ihr erst auswählen, wenn ihr auch den Quellcode für die `ext_localconf.php` eingetragen habt. Nachdem ihr das Kapitel `ext_localconf.php` abgearbeitet habt, könnt ihr hier in der `ext_tables.php` den Wert "Pi1" ja mal auf "Trallala" setzen. ihr werdet im Quellcode des TYPO3-Backends feststellen, dass sich der Value unserer Extensionoption von "sffluid_pi1" auf "sffluid_trallala" geändert hat. Aber das bitte gleich wieder zurück auf "Pi1" ändern.

## ext_localconf.php

Diese Datei ist für TYPO3 in Verbindung mit Extbase sehr wichtig, denn ohne sie passiert im Frontend mal gar nichts. Es wird einfach nur unser Template angezeigt, aber der Contentbereich bleibt leer. Wir müssen TYPO3 mit dieser Datei nun mitteilen, wo der Code ist, der auszuführen ist. Das funktioniert nun Dank Extbase nicht mehr per require oder include, sondern folgendermaßen (evtl. müsst ihr die Datei noch anlegen):

```php
<?php
if (!defined ('TYPO3_MODE')) die ('Access denied.');

Tx_Extbase_Utility_Extension::configurePlugin(
    $_EXTKEY,
    'Pi1',
    array(
        'Blabla' => 'testblabla',
    )
);
```

1. Parameter erwartet den Namen unserer Extension. Ein $EXTKEY erspart uns etwas Arbeit

2. Parameter erwartet einen eindeutigen Namen unseres Plugins. Der erste Buchstabe hier muss großgeschrieben werden. Da die meisten Plugins in TYPO3 als pi1, pi2, pi3, ... bezeichnet werden, werden auch wir uns daran halten.

3. Parameter ist ein Array in dem als Key (hier: Blabla) der Wert testblabla übergeben wird. Wie ich oben schon gesagt habe, bastelt Extbase sich die Methoden- und Objektnamen selbst zusammen.

Wir speichern die Datei `ext_localconf.php` mal ab und schauen, was uns das Frontend nach einem Clear Cache dazu sagt.

Wer im Frontend eine weiße leere Seite erhält, muss im Installtool folgende Einstellungen zum Debuggen setzen:

```
devIPmask = Deine öffentliche IP-Adresse (www.wieistmeineip.de)
sqlDebug = 1
displayErrors = 1
debug = 1
```

In meinem Fall erscheint nun diese Fehlermeldung:

```
Could not analyse class:StefanFroemken\Sffluid\Controller\BlablaController maybe not loaded or no autoloader?
```

Wir können hier sehr schön erkennen, wie Extbase sich den Objektnamen zusammengeschraubt hat. Aus dem Extensionnamen sffluid und dem key Blabla aus unserem Array in der `ext_localconf` wurde der Objektname `\StefanFroemken\Sffluid\Controller\BlablaController`. Dieser Name ist nicht nur ein Name, sondern er gibt anhand der Unterstriche eine Art Verzeichnisstruktur wieder:

StefanFroemken = Vendorname, um gleichnamige Extension differenzieren zu können. Theoretisch.

Sffluid = Verzeichnis innerhalb des Extensionrootverzeichnisses

Controller = Unterverzeichnis von Classes. Classes ist ein Ordner, der bei Extbase-Projekten zwingend vorhanden sein muss.

BlablaController = Dateiname (.php) im Ordner Controller

Kurz: Allein durch die Angabe des Extensionnamens und dem Array wird nun im Verzeichnis typo3conf/ext/sffluid/Classes/Controller.php eine Datei mit dem Namen BlablaController.php erwartet. Diese Datei muss zwingend ein Objekt mit dem Namen \StefanFroemken\Sffluid\Controller\BlablaController enthalten.

Also dann: legen wir diese Struktur mal an.

Hinweis:
In diesem Array (ext_localconf.php) können auch mehrere Actions per Komma getrennt aufgelistet werden:

```php
Tx_Extbase_Utility_Extension::configurePlugin(
    $_EXTKEY,
    'Pi1',
    [
        'Blabla' => 'testblabla, bearbeiten, delete, auflisten',
    ]
);
```

Die erste Action in diesem Array ist immer die defaultAction, die als erstes ausgeführt wird.

## Verzeichnisstruktur

Extbase lehnt sich an das DDD an. Dieses Design erfordert eine ganz klare Trennung des Programmcodes. Ist vielleicht etwas salopp ausgedrückt, aber wer mal nach Domain Driven Design im Internet sucht wird Massen an Informationen finden, die über mehrere Seiten ja sogar über ganze Bücher hinweg diskutiert werden.

Hier mal eine Struktur einer Extension wie sie aussehen sollte:

- Classes
  --- Controller
  --- Domain
  --- ViewHelpers
- Configuration
  --- FlexForms
  --- TCA
  --- TypoScript
- Resources
  --- Private
  ----- Templates
  --- Public

ihr könnt nun sehr deutlich sehen, dass jeder Themenbereich einen eigenen Ordner erhalten hat.

In unserem Beispiel ist bisher nur der Ordner "Controller" im Verzeichnis "Classes" interessant. Alle Dateien in diesem Ordner sind einem festen Objekt zugewiesen. Ich sage das deshalb, weil wir später auch noch das Thema Datenbank ansprechen wollen und sich dann nur EIN Objekt um unsere Tabelle kümmern wird. Es wird sowas nicht mehr geben, dass ich aus verschiedenen Objekten auf ein und dieselbe Tabelle zugreife. In diesen Dateien werden so Funktionen hinterlegt wie "ListeMirAlleDatenAusDerTabelleAuf" oder "IchBinDieFunktionDieAlsErstesGeladenWird" oder "IchKannDatenEditieren".

## Das Controllerobjekt

Bis hier hin werdet ihr bestimmt schon den Ordner "Classes" mit seinem Unterordner "Controller" erstellt haben. Evtl. habt ihr dort auch schon eine Datei mit dem Namen "BlablaController.php" erstellt.

Ich hab meine BlablaController.php einfach mit dem gewünschten Objekt gefüllt. Mehr nicht:

```php
<?php
class Tx_Sffluid_Controller_BlablaController extends Tx_Extbase_MVC_Controller_ActionController 
{
}
```

Nach einem Speichern und Clear Cache erhalte ich nun die Fehlermeldung, dass eine testblablaAction-Funktion fehlt:

An action "testblablaAction" does not exist in controller "Tx_Sffluid_Controller_BlablaController".
Vielleicht erinnert ihr euch daran, das der Value unseres Arrays, das wir in der ext_localconf.php angegeben haben, nun wieder zutage gekommen ist.

Also legen wir diese Function in unserer BlablaController.php an:

```php
<?php
class Tx_Sffluid_Controller_BlablaController extends Tx_Extbase_MVC_Controller_ActionController 
{
    public function testblablaAction() {
    }
}
```

und nach einem Speichern und Clear Cache erhalten wir nur noch einen netten Hinweis darauf, dass wir noch kein Template angelegt haben:

```
Sorry, the requested view was not found. The technical reason is: No template was found. View could not be resolved for action "testblabla".
```

Dazu im letzten Kapitel mehr.

## Ich will endlich was sehen

Ich hab mir dazu die halbe Funktionsreferenz angeschaut und festgestellt, dass Extbase die zum Controller gehörende Templatedatei automatisch hier sucht:

`Resources/Private/Templates/[Controller]/[Action].html`

Heißt in unserem Beispiel: Wir haben in der `ext_localconf.php` das Array mit dem key "Blabla" angelegt. Das ist unser Controller. Weiterhin haben wir mit dem Array-Value "testblabla" gesagt, dass es die Funktion testblablaAction gibt.

Unsere Datei wird also hier erwartet:

```php
Resources/Private/Templates/Blabla/Testblabla.html
```

Nachdem ihr diese Verzeichnisstruktur angelegt habt, füllt diese Datei mit folgendem Inhalt:

```html
<p>Hallo {name}, wie geht es Dir?</p>
```

Variablen werden in Fluid mit geschweiften Klammern umschlossen. Die Variable `{name}` werden wir nun durch unseren Namen ersetzen. Wie hieß unsere aufzurufende Action doch gleich? Ach ja richtig: Die testblablaAction. Also auf in die BlablaController.php:

```php
class Tx_Sffluid_Controller_BlablaController extends Tx_Extbase_MVC_Controller_ActionController 
{
    public function testblablaAction() {
        $this->view->assign('name', 'Stefan Froemken');
    }
}
```

Damit habt ihr den ersten Schritt zu Fluid geschafft und habt vielleicht nun eine Ahnung wie geil Extbase/Fluid sein kann und wie mächtig und gewaltig das Ganze drum rum ist.
