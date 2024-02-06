+++
title = "RTE DB"
date = 2024-01-09T20:32:51+01:00
aliases = ["rte-db.html"]
+++

## RTE <--> DB

Ich schildere Euch hier den Weg, wie die Daten vom RTE in die DB kommen. Den genauen Ablauf findet ihr auf der TYPO3-Seite:

Der Inhalt des RTE wird nicht sofort in die Datenbank geschrieben, sondern durchläuft einen Prozess von mehreren Abschnitten

### RTE

Hier wird der Inhalt schon beim Editieren eines Textes zu einem HTML-Quellcode umgewandelt

### entryHTMLparser_db

In diesem Abschnitt kann der von dem RTE generierte HTML-Quellcode VOR der Transformation schon mal vorformatiert werden.

### Transformations

Hier wird die eingestellte Transformation durchgeführt.

### exitHTMLparser_db

Hier können jetzt noch abschließende Veränderungen NACH der Transformation definiert werden, bevor der Quelltext in der DB landet. Hier definieren viele Programmierer, dass aus b und i wieder strong und em wird.

### DB

Hier wird nix mehr konvertiert. Hier wird der zig mal verarbeitete Inhalt des RTE endlich in der Datenbank abgelegt.

### DB --> RTE

Ähnlich sieht es auf dem Weg von der DB zum RTE aus. Die Erklärungen eines jeden Schrittes könnt Ihr denen von oben entnehmen:

1. DB
2. entryHTMLparser_rte
3. Transformations
4. exitHTMLparser_rte
5. RTE
