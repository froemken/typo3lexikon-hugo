+++
title = "Tipps und Tricks"
date = 2024-01-09T20:25:52+01:00
aliases = ["tipps-und-tricks.html", "fluid-tipps-und-tricks.html"]
+++

## If condition mit strings

Mitte Januar hatten wir eine Extbase/Fluid-Schulung mit Patrick Lobacher. Klar, auch hier kam die Frage auf, wie man eine Variable mit einem String vergleicht. Leider funktioniert diese naheliegende Lösung noch nicht:

```html
<f:if condition="{data.ctype} == 'table'">
{data.bodytext}
</f:if>
```

### Die Alias-Lösung

Das liegt daran, dass Fluid nur Arrays und Objekte miteinander vergleichen kann. Nach ein bissl tüfteln bin ich auf die mittlerweile im Internet mehrfach veröffentliche Lösung gestoßen:

```html
<f:alias map="{myText: 'table'}">
<f:if condition="{data.ctype} == {myText}">
{data.bodytext}
</f:if>
</f:alias>
```

### Die Array-Lösung

```html
<f:if condition="{0:data.ctype} == {0:'table'}">
{data.bodytext}
</f:if>
```

### Mehrere Werte als Array überprüfen

```html
<f:if condition="{0:data.ctype, 1:data.hidden, 2:data.deleted} == {0: 'table', 1: '0', 2: '0'}">
{data.bodytext}
</f:if>
```

## Rendern von Contentelementen

Es gibt ja diese Contentelemente Text, Text mit Bild, Tabelle, Liste und so weiter. Wenn ihr mithilfe von Extbase mal auf die Inhaltselemente zugreifen müsst, dann holt ihr euch den Inhalt üblicherweise über das Feld bodytext.

Wenn ihr wie ich auch den RTE verwendet, könnt ihr nun mithilfe von Fluid, den Text aus der Datenbank wieder in HTML-Quelltext konvertieren:

```html
<f:format.html>{content.bodytext}</f:format.html>
```

Bis hierhin ist die Welt noch in Ordnung, aber spätestens, wenn ihr den bodytext einer Tabelle ausgebt, steht ihr vor einem Problem und `f:format.html` wandelt euch das bestimmt nicht in eine Tabelle um. Folgende Lösungen stehen euch zur Verfügung, wobei die letzte Lösung die Beste ist:

1. Schon bei der Datenbankabfrage im Repository könnt ihr mithilfe einer Switch-Case-Anweisung herausfinden, welchen CType ihr aktuell habt und die dafür entsprechenden css_styled_content-Funktionen ausführen. Ich gehe da jetzt nicht weiter drauf ein, weil das bestimmt nicht der way-of-fluid ist.
2. Innerhalb des Controllers holt ihr Euch ein Objekt von tslib_cObj und ladet Euch die Contentinhalte mit Hilfe des Contentobjects CONTENT.
3. Die geilste und vor allem die kürzeste Variante ist aber:

```html
<f:if condition="{content.ctype}">
<f:cObject typoscriptObjectPath="tt_content.{content.ctype}.20" data="{content}" />
</f:if>
```

Beachtet aber, dass ihr für diese Lösung auch alle benötigten tt_content-Felder in Eurem Model definiert habt.
