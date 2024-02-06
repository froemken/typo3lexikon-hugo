+++
title = "Allowed Classes"
date = 2024-01-09T20:32:43+01:00
aliases = ["allowed-classes.html"]
+++

In meiner TYPO3-Installation wunderte mich, dass der RTE beim Abspeichern unerlaubte Classes trotzdem zugelassen hat. Meine Klassen habe is so definiert:

```typo3_typoscript
RTE.default.classesParagraph = test1,test2,...
RTE.default.proc.allowedClasses < RTE.default.classesParagraph
```

Nach langem Probieren habe ich festgestellt, dass diese Funktionen nur auf <p>, <div> und <span>-Tags Wirkung zeigt. Alle anderen Tags wie H1, H2 und Co. sind davon ausgeschlossen.

Damit auch bei anderen Tags die Classes entfernt werden, müsst ihr folgende Eigenschaft setzen:

```typo3_typoscript
RTE.default {
  proc {
    entryHTMLparser_db {
      tags {
        h2.fixAttrib.class.list = subheader
        h2.fixAttrib.class.default = subheader
        h3.fixAttrib.class.list = subheader2
        h3.fixAttrib.class.default = subheader2
      }
    }
  }
}
```

Damit werden automatisch ALLE Classes entfernt, die nicht in der kommegetrennten Liste vorhanden sind. Weiterhin erhalten definierte Tags, ohne Classes, automatisch den ersten Wert dieser Liste als Defaultwert.
