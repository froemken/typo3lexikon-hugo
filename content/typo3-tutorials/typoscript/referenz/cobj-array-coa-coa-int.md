+++
title = "COBJ_ARRAY (COA, COA_INT)"
date = 2024-01-09T20:58:10+01:00
aliases = ["cobj-array-coa-coa-int.html"]
+++

Ein Contentobject besteht aus Inhalt wie Text (TEXT) oder auch Bildern (IMAGE). Wenn aber mal mehrere solcher Contentobject benötigt werden, kann man hier auf das COBJ_ARRAY zurückgreifen. Da dieser Name doch was lang ist und in TypoScript oft verwendet wird, könnt ihr dieses Objekt mit COA abkürzen. Falls das COA-Objekt nicht gecached werden soll, weil z.B. die enthaltenen Daten mit jedem Seitenaufruf neu generiert werden sollen, dann könnt ihr auf das COA_INT-Objekt umsteigen.

## Eigenschaften

### 1,2,3,4...

Typ: cObject

Ein Contentobject wie TEXT, IMAGE, ...

### if

Typ: if

Sollte dieses if "false" zurückliefern, dann wird das komplette COA-Objekt nicht gerendert und somit nicht auf der Webseite angezeigt.

### wrap

Typ: wrap

Das Ergebnis aller enthaltenen Inhaltsobjekte wird mit diesem wrap umschlossen

### stdWrap

Typ: stdWrap

Das Ergebnis aller enthaltenen Inhaltsobjekte kann mit den stdWrap-Eigenschaften nachbearbeitet werden.

### includeLibs

Typ: Kommaseparierte Liste von Dateipfaden

Hier könnt ihr PHP-Dateien einbinden, die Funktionen und Methoden für alle Contentobjekte bereitstellen. Mit dem Contentobjekt USER und der Eigenschaft userFunc solltet ihr auf diese zugreifen können.
