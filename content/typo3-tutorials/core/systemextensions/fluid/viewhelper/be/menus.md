+++
title = "Menus"
date = 2024-01-09T20:28:20+01:00
aliases = ["menus.html"]
+++

## f:be.menus.actionMenuItem

Mit diesem ViewHelper könnt ihr einen Menüeintrag für Euer BE-Modul erstellen. Damit das funktioniert müssen sich diese ViewHelper innerhalb des `f:be.menus.actionMenu` ViewHelper befinden.

### Parameter

| Parameter  | Erklärung                                                                           | Pflichtfeld |
|------------|-------------------------------------------------------------------------------------|-------------|
| label      | Der anzuzeigende Name des Menüeintrages                                             | ja          |
| controller | In welchem Controller liegt die aufzurufende Action                                 | ja          |
| action     | Welche Action soll nach Auswahl aufgerufen werden                                   | ja          |
| arguments  | Welche Parameter sollen an die aufzurufende Action übergeben werden (Arraynotation) | nein        |

### Beispiel

Dieses Menü beinhaltet eine Selectbox mit 2 Menüeinträgen, die nach Auswahl die hinterlegte Action des hinterlegten Controllers direkt aufrufen. Wie ihr seht, könnt ihr die Labels entweder direkt oder noch besser als Sprach-ViewHelper realisieren.

```html
<f:be.menus.actionMenu>
    <f:be.menus.actionMenuItem label="Neu" controller="Fluid" action="new" />
    <f:be.menus.actionMenuItem label="{f:translate(key='list')}" controller="Fluid" action="list" />
</f:be.menus.actionMenu>
```

## f:be.menus.actionMenu

Dieser ViewHelper erstellt eine Selectbox. Mithilfe der actionMenuItem-ViewHelper könnt ihr diese Selectbox mit Optionen auffüllen, die dann nach Auswahl auf die gewünschte Action zugreifen.

### Parameter

| Parameter         | Erklärung                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| defaultController | Gebt hier den Controllernamen ein auf den zurückgegriffen werden soll, falls im ActionMenuItem-ViewHelper kein Controller angegeben worden ist. Wenn ich mir den ViewHelper im Quellcode anschaue, würde ich diesen Parameter besser nicht verwenden. Erstens: Weil er im Quelltext überhaupt nicht weiter verwendet wird und Zweitens: Weil der Controller in den ActionMenuItems eh eine Pflichtangabe ist. |

Beispiele findet ihr bei den `f:be.menues.actionMenuItem` ViewHelpern.
