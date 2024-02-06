+++
title = "RenderType"
date = 2024-01-09T20:16:40+01:00
aliases = ["render-type.html"]
+++

## RenderType eines Eingabefeldes verändern

Seit TYPO3 7.5 könnt Ihr das Rendern von Eingabefeldern selbst in die Hand nehmen. Im Folgenden wollen wir ein Inputfeld erstellen, dass um 90 Grad gedreht angezeigt wird. Klar, macht nun überhaupt gar keinen Sinn, aber wenns scheiße aussieht, dann wisst Ihr wenigstens, dass es funktioniert hat :-)

Erstellt in Eurer Projekt bezogenen Extension eine Datei Configuration/TCA/Overrides/tt_content.php mit folgendem Inhalt:

```php
<?php
$GLOBALS['TCA']['tt_content']['columns']['header']['config']['renderType'] = 'rotateInput90';
```

Jetzt müsst Ihr den Wert rotateInput90 nur noch in TYPO3 registrieren. Fügt dazu folgende Zeilen in die ext_localconf.php ein:

```php
$GLOBALS['TYPO3_CONF_VARS']['SYS']['formEngine']['nodeRegistry'][1461871364] = array(
    'nodeName' => 'rotateInput90',
    'priority' => '70',
    'class' => \StefanFroemken\Sfcategory\Form\Element\InputTextElement::class,
);
```

Bei der Zahlenkombination handelt es sich nur um einen aktuellen TimeStamp. Mit nodeName registrieren wir den renderType, den wir oben bereits verwendet haben. Mit class gebt Ihr an, welche PHP-Klasse sich um das Rendering kümmern soll.

Da es innerhalb der FormularElemente nun keine Hooks oder SignalSlots mehr gibt, müsst Ihr nun die backend/Classes/Form/Element/InputTextElement.php in Eure Extension kopieren typo3conf/ext/[EureExt]/Classes/Form/Element/InputTextElement.php. Ändert in der Kopie den Namespace und fügt noch das use-Statement hinzu:

```php
use TYPO3\CMS\Backend\Form\Element\AbstractFormElement;
```

Damit habt Ihr nun eine exakte lauffähige Kopie. Ziemlich weit unten werden CSS-Klassen registriert. Fügt hier noch die fa-rotate-90 Klasse hinzu:

```php
// set classes
$classes[] = 'form-control';
$classes[] = 't3js-clearable';
$classes[] = 'hasDefaultValue';
$classes[] = 'fa-rotate-90';
```

Nachdem Ihr den Systemcache geleert und das Inhaltselement neu geladen habt, sollte die Titelzeile nun ja, um 90 Grad gedreht sein. Viel Spaß, beim Entdecken Eurer eigenen Möglichkeiten.
