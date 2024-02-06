+++
title = "listNum"
date = 2024-01-09T20:58:10+01:00
aliases = ["listnum.html"]
+++

TYPO3 verwendet sehr oft kommaseparierte Listen. Ihr hinterlegt im Feld media der Seiteneigenschaften einer jeden Seite mehrere Dateien/Bilder, dann werden die Dateinamen alle hintereinander durch ein Komma getrennt in der Datenbank gespeichert. So auch mit Tabellen, wenn mehrere Datensätze einem Datensatz aus einer anderen Tabelle zugeordnet werden soll (Ohne MM-Tabelle).

Spalte "media" der Tabelle "pages":

```
cols-bottom.gif,dot.gif,footer-topblue.gif,headerblue.jpg
```

Auf dieses Feld kann ich mit der data-Funktion sehr einfach zugreifen:

```typo3_typoscript
10 = TEXT
10.data = DB:pages:34:media
```

Damit wir nun immer auf den ersten Eintrag aus dieser Liste zugreifen können gibt es die Eigenschaft listNum. listNum splittet die Einträge an Hand der Kommas auf und nummeriert die Einträge beginnend mit 0 durch.

```typo3_typoscript
10 = TEXT
10.data = DB:pages:34:media
10.listNum = 0
```

Ergebnis: `cols-bottom.gif`

## splitChar

```typo3_typoscript
10 = TEXT
10.value= 12,34,132,07,32,54,23,99
10.listNum = 2
```

Warum auch immer, aber es könnte ja mal passieren, dass Ihr Beträge/Preise in solch einer Liste haben wollt. Na? Könnt Ihr noch sauber harusfinden welche Zahl zu welchem Betrag gehört?! listNum wird hier sturr die 132 zurückgeben.

```typo3_typoscript
10 = TEXT
10.value= 12,34#132,07#32,54#23,99
10.listNum = 2
10.listNum.splitChar = #
```

Hier schaut es schon anders aus. Ich habe die Beträge nicht mit einem Komma, sondern mit der Raute getrennt. Nun kann listNum auch endlich den richtigen Betrag 32,54 zurückgeben.

## Hier noch ein etwas extremeres Beispiel

```typo3_typoscript
10 = TEXT
10.value= 12,34###TRENNER###Was ein Text###TRENNER###Verwenden Sie den Marker ###IMG###, um Bilder einzufügen###TRENNER###Hallo zusammen
10.listNum = 2
10.listNum.splitChar = ###TRENNER###
```

Hier sinkt die Wahrscheinlichkeit, dass listNum einen Text unerwünscht trennt drastisch. Selbst der Marker ###IMG### bringt listNum nicht durcheinander.

## last

```typo3_typoscript
10 = TEXT
10.data = DB:pages:34:media
10.listNum = last
```

Während wir mit listNum = 0 immer auf den ersten Eintrag zugreifen können, können wir mit dem String "last" immer auf den letzten Eintrag einer kommaseparierten Liste zugreifen. last wird nicht in Anführungszeichen gesetzt wie es auf der englischen Doku zu sehen ist. Das bedeutet nur, dass dieser Wert als String zu interpretieren ist und nicht als Funktion wie bei calc.

## calc

```typo3_typoscript
10 = TEXT
10.data = DB:pages:34:media
10.listNum = last - 1

```

calc wird ohne Anführungszeichen auf der englischen Doku angegeben. Das bedeutet, dass uns ein paar standard Rechenzeichen wie + - * / zur Verfügung stehen. In dem Beispiel oben können wir also sehr einfach immer auf das vorletzte Element der kommaseparierten Liste zugreifen.

## Hier ein etwas gemeines Beispiel

```typo3_typoscript
10 = TEXT
10.data = DB:pages:34:media
10.listNum = -1 + 1 * 3
```

Hier kommt nicht wie erwartet 2 raus (Punktrechnung vor Strichrechnung), sondern 0. Die Funktion calc arbeitet die Zahlen immer von vorne nach hinten ab und feddisch. -1 + 1 = 0 * 3 = 0.
