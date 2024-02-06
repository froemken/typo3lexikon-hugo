+++
title = "parseFunc"
date = 2024-01-09T20:58:10+01:00
aliases = ["parsefunc.html"]
+++

Während die meisten Eigenschaften und Funktionen von stdWrap nur "äußerlich" auf den Inhalt Einfluss nehmen können, greift parseFunc direkt auf den Inhalt zu und kann HTML-Tags in andere Tags umwandeln, bestimmte Worte ersetzen und/oder einigen Tags weitere Attribute hinzufügen.

parseFunc bringt folgende Eingenschaften bzw. Funktionen mit:

## externalBlocks

```typo3_typoscript
10 = TEXT
10.value = Ich bin ein <b>fettes</b> <div>Wort</div> und <b>ich</b> auch
10.parseFunc {
  externalBlocks = b,div
  externalBlocks.b.stdWrap.wrap = ###|###
  externalBlocks.div.stdWrap.wrap = ---|---
}
```

In value habe ich mal einen Text reingeschrieben, wie er in der Datenbank stehen könnte. Ich habe eine Stunde lang rumgesucht, bis ich begriffen habe, wie das mit den externalBlocks funktioniert. Denn wiedereinmal wird nirgends beschrieben, dass die externalBlocks zuvor definiert werden müssen (siehe Zeile mit b,div). Nur wenn diese Zeile vorhanden ist, nur dann funktioniert auch das stdWrap in den beiden folgenden Zeilen.

Das Ergebnis sieht ziemlich durcheinander aus, aber Ihr könnt hier sehr gut erkennen wie und wo die Wraps aktiv wurden:

```html
Ich bin ein ###<b>fettes</b>###---
<div>Wort</div>
--- und ###<b>ich</b>### auch
```

## callRecursiv

```typo3_typoscript
10 = TEXT
10.value = Ich bin ein <b>fettes</b> <div>Wort</div> und <b>ich</b> auch
10.parseFunc {
  externalBlocks = b
  externalBlocks.b.callRecursive = 1
  externalBlocks.b.callRecursive.tagStdWrap.wrap = ###|###
  externalBlocks.b.stdWrap.wrap = ---|---
}
```

Mit callRecursive werden die einzelnen Blöcke (hier <b>) nochmal an die Function parseFunc gesenden. Allerdings sind diesmal nur noch ein paar kleine Optionen verfügbar. In unserem Beispiel aktiviere ich callRecursive mit 1 und gebe mit tagStdWrap an, wie der beginnende Tag (<b>) mit stdWrap bearbeitet werden soll. In unserem Fall wrappe ich wieder alles, damit Ihr nachvollziehen könnt wie wo was passiert.

Das Ergebnis schaut nun so aus:

```html
Ich bin ein ---###<b>###fettes</b>--- <div>Wort</div> und ---###<b>###ich</b>--- auch
```

## Noch ein Beispiel

```typo3_typoscript
10 = TEXT
10.value = Ich bin ein <b>fettes</b> <div>Wort</div> und <b>ich</b> auch
10.parseFunc {
  externalBlocks = b
  externalBlocks.b.callRecursive = 1
  externalBlocks.b.callRecursive.tagStdWrap.wrap = ###|###
  externalBlocks.b.callRecursive.dontWrapSelf = 1
  externalBlocks.b.stdWrap.wrap = ---|---
}
```

Mit dontWrapSelf geben wir an, dass die umschließenden Tags (hier <b> und </b>) entfernt werden. Es bleibt also nur noch der reine Inhalt übrig. Da die beiden Tags nun weg sind, hat auch die Eigenschaft tagStdWrap keine Funktion mehr...denn ohne Tags kann auch nichts gewrappt werden.

Das Ergebnis

```html
Ich bin ein ---fettes--- <div>Wort</div> und ---ich--- auch
```

## HTMLtableCells

Mit HTMLtableCells wird definiert, dass es sich bei dem Inhalt um eine Tabelle handelt. Das ist auch der Grund, warum ich das erste Beispiel (10 = TEXT) in div-Tags gesetzt habe. Wie Ihr aber im zweiten Beispiel (20 = TEXT) erkennen könnt, reicht es auch direkt den table-Tag zu verwenden.

```typo3_typoscript
10 = TEXT
10.value = <div><table><tr><td>1 - 1</td><td>1 - 2</td></tr><tr><td>2 - 1</td><td>2 - 2</td></tr></table></div>
10.parseFunc {
  externalBlocks = div
  externalBlocks.div.HTMLtableCells = 1
  externalBlocks.div.HTMLtableCells.1.stdWrap.wrap = <p style="color: #FF0000;">|</p>
}
20 = TEXT
20.value = <table><tr><td>1 - 1</td><td>1 - 2</td></tr><tr><td>2 - 1</td><td>2 - 2</td></tr></table>
20.parseFunc {
  externalBlocks = table
  externalBlocks.table.HTMLtableCells = 1
  externalBlocks.table.HTMLtableCells.default.stdWrap.data = page:title
  externalBlocks.table.HTMLtableCells.2.tagStdWrap.addParams.style = background: #C0C0C0
  externalBlocks.table.HTMLtableCells.2.stdWrap.setCurrent = Neuer Text
  externalBlocks.table.HTMLtableCells.2.stdWrap.data = current:1
}
```

Mit HTMLtableCells wird definiert, dass es sich bei dem Inhalt um eine Tabelle handelt. Das ist auch der Grund, warum ich das erste Beispiel (10 = TEXT) in div-Tags gesetzt habe. Wie Ihr aber im zweiten Beispiel (20 = TEXT) erkennen könnt, reicht es auch direkt den table-Tag zu verwenden.

Hinter HTMLtableCells wird nun angegeben welche Spalte der Tabelle bearbeitet werden soll. 1 für die erste, 2 für die Zweite und so weiter. Mit der Angabe von default, kann eine Standardverarbeitung für die Zellen definiert werden.

Für jede Zelle stehen wieder die Funktionen stdWrap, tagStdWrap und callRecursive zur Verfügung.
