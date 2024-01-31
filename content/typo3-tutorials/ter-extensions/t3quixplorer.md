+++
title = "t3quixplorer"
date = 2024-01-09T20:53:14+01:00
aliases = ["t3quixplorer.html"]
+++

Ich bin seit März 2010 Teammember von `t3quixplorer`. Warum also nicht auch diese Extension hier etwas genauer beschreiben?

Diese Doku wird noch etwas wachsen, aber bis dahin schon mal Folgendes:

Wenn ihr diese Fehlermeldung bekommt:

```
Scriptet indeholder syntax-fejl. Er du sikker på at du vil gemme?
```

Dann muss das nicht immer daran liegen, dass ihr auch tatsächlich einen Fehler in eurer Programmierung habt, sondern evtl. der Pfad zu der php-Executable nicht stimmt. Für die meisten Linux-Systeme wird der Pfad wohl in Ordnung sein, aber bei Windowssystemen, muss dieser in den Konstanten des Erweiterungsmanager genaustens gesetzt werden.

Ich hab bei mir folgenden Pfad eingetragen und nun ist die nervige Meldung weg:

`F:\xampp\php\php.exe`
