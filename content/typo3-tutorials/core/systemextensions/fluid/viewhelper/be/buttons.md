+++
title = "Buttons"
date = 2024-05-07T20:34:20+01:00
aliases = ["buttons.html"]
+++

## f:be.buttons.csh

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 12.0{{% /badge %}}
{{% badge style="red" icon="skull-crossbones" %}}TYPO3 13.0{{% /badge %}}

Dieser ViewHelper ist veraltet. Bitte nicht mehr verwenden.

CSH steht für `Context Sensitive Help`. Damit ließ sich für jedes Feld in den Backend-Formularen (List-Modul) ein zusätzlicher Button mit Fragezeichen-Symbol einbauen, über den weitere Informationen zu einem Feld erhalten werden konnten.

{{% notice style="info" title="Hinweis" icon="info" %}}
Die CSH Implementation im TYPO3 Core klappte nur für direkte TCA Felder. Für FlexForm Felder war die Implementierung immer wieder kaputt. Auch für die Felder in einem Scheduler Task gab es immer wieder Probleme. Da traute man sich irgandwann auch gar nicht mehr dran. Ich denke, alle sind glücklich mit der neuen TCA `description` Eigenschaft, um erklärende Texte zu einem Feld direkt und ohne JavaScript Schnick-Schnack ausgeben zu können. Bye Bye CSH.
{{% /notice %}}

### Parameter

| Parameter       | Erklärung                                                                                                                                                                                                                            | Standardwert |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| table           | Der Tabellenname                                                                                                                                                                                                                     |              |
| field           | Der Key aus der Sprachdatei                                                                                                                                                                                                          |              |
| iconOnly        | {{% badge style="orange" icon="angle-double-up" %}}TYPO3 7.3{{% /badge %}} {{% badge style="red" icon="skull-crossbones" %}}TYPO3 8.0{{% /badge %}} Es wird nur das Bildchen dargestellt, nicht aber der Hilfetext                   | deaktiviert  |
| styleAttributes | {{% badge style="orange" icon="angle-double-up" %}}TYPO3 7.3{{% /badge %}} {{% badge style="red" icon="skull-crossbones" %}}TYPO3 8.0{{% /badge %}} Zusätzliches `style` Attribut, das in die umschließende Tabelle eingebunden wird |              |

## f:be.buttons.icon

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 7.6{{% /badge %}}
{{% badge style="red" icon="skull-crossbones" %}}TYPO3 8.0{{% /badge %}}

Dieser ViewHelper ist veraltet. Bitte verwendet von nun an den `core:icon` ViewHelper.

Mit diesem ViewHelper konnten Icons in selbst programmierte Backend Module eingebaut werden. Die `icon` Eigenschaft ließ jedoch nur eine geringe Anzahl erlaubter Icons zu. Bei Bedarf konnte das Icon auch verlinkt werden.

Liste erlaubter Icons:

`add`, `add_workspace`, `button_down`, `button_hide`, `button_left`, `button_unhide`, `button_right`, `button_up`, `clear_cache`, `clip_copy`, `clip_cut`, `clip_pasteafter`, `closedok`, `datepicker`, `deletedok`, `edit2`, `helpbubble`, `icon_fatalerror`, `icon_note`, `icon_ok`, `icon_warning`, `new_el`, `options`, `perm`, `refresh_n`, `saveandclosedok`, `savedok`, `savedoknew`, `savedokshow`, `viewdok`, `zoom`.

### Parameter

| Parameter | Erklärung                                                                                                   |
|-----------|-------------------------------------------------------------------------------------------------------------|
| uri       | Bei Angabe einer URI wird das Icon verlinkt. Kann auch mit einem der `f:uri.*` ViewHelper kombiniert werden |
| icon      | Der Name des Icons, dass verwendet werden soll                                                              |
| title     | Der hier eingegebene Wert wird als `title` Attribut für den Link verwendet                                  |

## f:be.buttons.shortcut

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 11.0{{% /badge %}}
{{% badge style="red" icon="skull-crossbones" %}}TYPO3 12.0{{% /badge %}}

Mit diesem ViewHelper konntet ihr in euren Backend-Modulen diesen Bookmark Button oben rechts einfügen, um die aktuelle Seite in den TYPO3 Favoriten abzuspeichern.

{{% notice style="info" title="Hinweis" icon="info" %}}
Ich vermute, durch die `ButtonBar` wurde dieser ViewHelper obsolet.
{{% /notice %}}


### Parameter

| Parameter | Erklärung                                                                                                              |
|-----------|------------------------------------------------------------------------------------------------------------------------|
| getVars   | Wenn nichts angegeben wird, dann werden Seiten-UID, Modul und Modulargumente mit dem Favoriten zusammen abgespeichert. |
| setVars   | Hier wird auf template::makeShortcutIcon() verwiesen. Wird aber für Extbase-Module normalerweise nicht benötigt.       |

### Beispiel

```html
<f:be.buttons.shortcut />
```
