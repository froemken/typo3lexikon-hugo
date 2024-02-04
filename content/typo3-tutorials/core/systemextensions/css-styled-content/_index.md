+++
title = 'css_styled_content'
date = 2024-01-09T20:19:39+01:00
alwaysopen = false
aliases = ["css-styled-content.html"]
+++

Die System-Extension `css_styled_content` ist bei jeder TYPO3-Installation bereits integriert und standardmäßig aktiviert. Das Einzige, was ihr noch machen müsst: Ihr müsst in eurem Template im Tab "Enthält" die Zeile "Statische Templates einfügen" das statische Template `css_styled_content` einbinden.

## Was ist css_styled_content?

`css_styled_content` ist eine Systemextension und somit bereits in TYPO3 installiert und aktiv. Diese Extension erleichtert euch das Leben enorm, denn ohne sie müsstet ihr alle Daten aus der Datenbank selber abfragen und an das Design, das ein Redakteur für einen Datensatz eingestellt hat, anpassen.

## Jetzt nochmal in langsam

Schaut euch bitte das Bild hier oben rechts an und werft einen Blick auf die Elemente innerhalb des roten Rahmens. Evtl. habt ihr in eurer TYPO3-Installation eine andere Ansicht und müsst ganz unten noch die Checkbox "2te Optionspalette aktivieren" anhaken.

Ihr seht nun, dass ein Redakteur allein für die Überschrift 5 Werte setzen kann:

- Die Überschrift an sich
- Ausrichtung
- Typ
- Link / Verweis
- Datum

Es reicht also nicht einfach die Überschrift aus der Datenbank zu holen und in einen Wrap zu packen.

`css_styled_content` stellt euch verschiedene Funktionen zur Verfügung, die euch genau solche Aufgaben bereits abnehmen. So kümmert sich die Funktion `lib.stdheader` allein um den Aufbau der Überschrift unter Berücksichtigung aller Optionen, die der Redakteur für eine Überschrift hinterlegen kann. Auch Funktionen zum Auslesen der 4 Inhaltsspalten sind in `css_styled_content` bereits enthalten:

- styles.content.get
- styles.content.right
- styles.content.left
- styles.content.border
