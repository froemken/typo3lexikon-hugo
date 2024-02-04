+++
title = "lib.stdheader"
date = 2024-01-09T20:19:58+01:00
slug = "libstdheader"
aliases = ["libstdheader.html"]
+++

Bevor ich euch ein paar Beispiele zeige, wie ihr die Verarbeitung des Header manipulieren könnt, möchte ich euch erstmal zeigen, wie lib.stdHeader aufgebaut ist und funktioniert. Schaut euch dazu mal das Bild hier oben rechts an. Das ist ein Auszug aus dem ObjectBrowser von TYPO3.

## [2] = LOAD_REGISTER

Mit LOAD_REGISTER wird eine Variable erstellt, auf die später im TS wieder zugegriffen werden kann. Es wird hier eine Variable mit dem Namen "headerStyle" erstellt. Wie ihr seht, greift headerStyle auf das Feld header_position zu. Dieses Feld spiegelt das DropDown-Feld "Ausrichtung" in der Überschriftenbearbeitung wider. In noTrimWrap seht ihr, dass dieser Wert in das `style` Attribut eingebunden wird. Dank dem required, wird dieses Attribut nur dann eingebunden, wenn für die Überschrift auch eine Ausrichtung hinterlegt wurde.

## [3] = LOAD_REGISTER

Wie oben schon wird hier die Variable headerClass mit dem Wert csc-firstHeader gefüllt, aber nur dann (if) wenn es sich um das erste Contentelement auf dieser Seite handelt. Schaut euch mal an wie TYPO3 das gelöst hat...einfach auf das [+]-Zeichen vor dem if klicken

## [5] = TEXT

Pro Überschrift könnt ihr auch ein Datum angeben, das über der eigentlichen Überschrift angezeigt werden kann. Auch hier ist wieder ein if zu finden, das sich darum kümmert nur dann den Inhalt anzuzeigen, wenn auch ein Datum gesetzt wurde. Wie ihr seht wird das Datum mit p-Tags gewrappt. Das wollen wir gleich mal ändern.

## [10] = CASE

In diesen CASE-Objekten ist die Eigenschaft "key" immer sehr wichtig. Wie ihr seht verweist diese auf das Feld header_layout. Dieses Feld spiegelt das DropDown-Feld für den Überschriften-Typ wieder. Klappt dieses DropDown mal auf und ihr habt die Aufwahl zwischen:

- Normal (ID 0)
- Layout 1 (ID 1)
- Layout 2 (ID 2)
- Layout 3 (ID 3)
- Layout 4 (ID 4)
- Layout 5 (ID 5)
- Verborgen (ID 100)

Wer sich den Quelltext dieser DropDown-Liste mal angeschaut hat findet die IDs wieder, wie ich sie hier zusätzlich mit angegeben habe. Diese IDs finden wir jetzt auch in unserem CASE wieder:

[1] = TEXT
[2] = TEXT
...

Heißt: Wähle ich in der Liste das 2te Layout aus, dann wird das TS hinter `[2] = TEXT` ausgeführt und ihr erhaltet eine Überschrift, die in einem h2-Tag gewrappt wird. Wird kein Layout ausgewählt, dann tritt die Eigenschaft `key.ifEmpty = 1` in Kraft und gibt per Default die Verarbeitung der Überschrift durch das TS hinter `[1] = TEXT` vor.

In der `fontTag` Eigenschaft seht ihr wie TYPO3 nun auf die Variablen, von den beiden `LOAD_REGISTER` zugreift.

[98] = RESTORE_REGISTER

Mit dieser Eigenschaft wird die zuletzt mit `LOAD_REGISTER` erstellte Variable wieder gelöscht.

[99] = RESTORE_REGISTER

Da mit dieser Eigenschaft immer nur die letzte Variable gelöscht wird, wir aber zwei Variablen mit `LOAD_REGISTER` registriert haben, muss die `RESTORE_REGISTER` Eigenschaft ein zweites Mal angewendet werden.

## Verborgen

Genau, die Eigenschaft für "Verborgen" fehlt noch. Diese ist in dem `stdWrap` zu finden im `if` Abschnitt. Hier wird abgefragt, ob das Headerlayout einen anderen Wert hat als 100. Wenn ja, dann wird die Überschrift angezeigt und wenn die ID vom Headerlayout genau 100 ist, dann wird die Überschrift nicht angezeigt.

## Beispiele

Wie oben schon erwählt, will ich euch zeigen, wie wir aus den p-Tags für das Datum div-Tags machen. Dazu braucht ihr nur auf die `wrap` Eigenschaft zu klicken, ersetzt die `p` Tag durch `div` Tags und speichert es ab. Dadurch wurde in das Setup-Feld des Templates automatisch folgende Zeile hinzugefügt:

```typo3_typoscript
lib.stdheader.5.wrap = |
```

ihr seht also: Kleinere Änderungen sind kein Thema. Auf diese Weise könnt ihr auch die Klassennamen sehr leicht ändern.

## Nummerierte Überschriften

Hier habe ich ein Skript für euch, dass die einzelnen Contentelemente durchnummeriert:

```typo3_typoscript
lib.stdheader.10.setCurrent.preCObject = TEXT
lib.stdheader.10.setCurrent.preCObject.data = COBJ:parentRecordNumber
lib.stdheader.10.setCurrent.preCObject.noTrimWrap = || |
```

Statt

```
Ich bin eine Überschrift
ganz viel Text

Ich bin eine weitere Überschrift
ganz viel Text
```

steht nun folgendes im Frontend

```
1 Ich bin eine Überschrift
ganz viel Text

2 Ich bin eine weitere Überschrift
ganz viel Text
```
