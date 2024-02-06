+++
title = "TypoScript"
date = 2024-01-06T19:11:53+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["typoscript.html"]
+++

## Das Verzeichnis: tslib

Dieser Bereich wird wohl zur Lebensaufgabe :-) Mein Wunsch für diesen Bereich ist es alle Funktionen aus dem Verzeichnis tslib zu analysieren und mit vielen Beispielen zu dokumentieren.

Warum eigentlich den ganzen Kram aus der englischen Original Dokumentation hier nochmal?

Ganz einfach. Schauen wir uns doch mal die Erklärung von current=1 mal an:

```
Sets the content to the "current"-value (see ->split)
```

OK, dann mal auf nach split:

```
The "current"-value is set to the value of current item, when the objects are called. See "stdWrap" / current.
```

Geil oder? Die eine Doku verweist auf `split` und `split` verweist auf `stdWrap`.

Wir können auch die Funktion `data` anschauen. Die Beispiele in der Doku sind viel kürzer als die Beschreibungen auf der rechten Seite. Für einen Anfänger echt schwierig da überhaupt sauber durchzublicken.

Ich hoffe mit meiner Dokumentation und den Beispielen zu jeder Funktion kommt ihr besser klar.

Euer Stefan
