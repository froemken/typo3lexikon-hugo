+++
title = "split"
date = 2024-01-09T20:58:10+01:00
aliases = ["split.html"]
+++

## Was macht split?

Grob gesagt, können wir diese Funktion mit der PHP-Funktion explode vergleichen. Split erhält einen Text, durchsucht diesen nach einer vorgegebenen Zeichenfolge und bietet uns dann die Möglichkeit an, die einzelnen Werte in einem bestimmten Format auszugeben.

## Ein kurzes Beispiel

Unser Eingabetext ist z.B. grün, grün, blau, gelb, rot, grün

Heißt: Unser Text enthält 6 einzelne Texte, die durch ein Komma getrennt wurden.

Dann füttern wir mal unser split:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split.token = ,
```

und unser Ergebnis auf der Webseite schaut so aus:

```html
grün grün blau gelb rot grün
```

Wir sehen, dass alle Wörter noch ein Leerzeichen besitzen. Also müssen wir entweder value von vorn herein von Leerzeichen befreien, oder wir müssen unser token mit einem noTrimWrap versehen. Ich hab mich hier für die noTrimWrap-Variante entschieden:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split.token = ,
page.10.split.token.noTrimWrap = || |
```

## Crashkurs noTrimWrap

noTrimWrap benötigt rechts und links immer das Pipe-Zeichen (Alt Gr + <). Das dritte Pipe-Zeichen (innerhalb der äußeren beiden Pipe-Zeichen) wird durch unser Komma ersetzt und Dank dem Leerzeichen dahinter wird unser Komma um das Leerzeichen ergänzt.

## cObjNum und seine Arrays (Teil 1)

Wie ich oben schon erwähnt habe, können wir split anfordern uns die einzelnen Werte in einem ganz bestimmten Format auszugeben. Dazu sei gesagt: Wenn wir ein cObjNum angeben, MUSS auch ein entsprechendes Array dazu vorhanden sein. Diese beiden Werte arbeiten immer zusammen:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split {
  token = ,
  token.noTrimWrap = || |
  cObjNum = 1
  1.current = 1
  1.wrap = <b>|</b><br />
}
```

### cObjNum = 1

Damit geben wir an, das unsere Werte mit dem Array 1 ausgegeben werden sollen

### 1.current = 1

Die 1 vorne ist unser Array, das von cObjNum angesprochen wird. Das current = 1 bedeutet, dass der Wert, der VOR unserem Komma steht, geladen werden soll.

### 1.wrap = <b>|</b><br />

Unsere Werte werden nun von <b> und </b><br /> umschlossen, damit sie fett geschrieben werden und jedes Wort eine eigene Zeile erhält. Probiert es aus. Unser Ergebnis schaut nun so aus:

```html
grün
grün
blau
gelb
rot
grün
```

### Hinweis: Wieso klappt das mit dem 1.current?

Genau diese Frage habe ich mir auch gestellt. Normalerweise muss man doch immer 1 = TEXT oder sowas angeben. Ist das hier vergessen worden? NEIN! Denn diese Arrays hier (1, 2, 3, 4, …) sind vom Typ CARRAY und nicht wie erwartet vom Typ COA. CARRAY hat den Vorteil, das es die Funktionen der stdWrap DIREKT anbietet. Schaut mal in die Doku...da stehts „contains stdWrap-properties“:

http://typo3.org/documentation/document-library/references/doc_core_tsref/4.1.0/view/7/11/

## cObjNum und seine Arrays (Teil 2)

Ich habe geschrieben, dass wir die Ausgabe beliebig gestalten können. Also wollen wir hier mal verschiedene Stile für die Ausgabe definieren:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split {
  token = ,
  token.noTrimWrap = || |
  cObjNum = 1 || 2 || 3
  1.current = 1
  1.wrap = <b>|</b><br />
  2.current = 1
  2.wrap = <i>|</i><br />
  3.current = 1
  3.wrap = <u>|</u><br />
}
```

cObjNum besitzt an dieser Stelle noch zusätzlich die Eigenschaft des sogenannten optionSplit. Damit könnt Ihr angeben in welcher Reihenfolge, die verschiedenen Stile angesprochen werden sollen. Wichtig bei optionSplit ist zu wissen, dass bei mehr als in optionSplit angegebenen Werte, die restlichen Werte immer mit dem letzten Wert aus optionSplit erzeugt werden. In unserem Beispiel erhalten wir folgende Ausgabe::

```html
grün
grün
blau
gelb
rot
grün
```

## cObjNum und seine Arrays (Teil 3)

Hier haben wir ein schon etwas komplexeres Beispiel:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split {
  token = ,
  token.noTrimWrap = || |
  cObjNum = 1 |*| 2 || 3 |*| 1
  1.current = 1
  1.wrap = <div style="color: #FF0000;">|</div>
  2.current = 1
  2.wrap = <div style="color: #00FF00;">|</div>
  3.current = 1
  3.wrap = <div style="color: #0000FF;">|</div>
}
```

Ich will das Thema optionSplit hier nicht allzu deutlich erklären, deshalb nur kurz: Mit `|*|` splitte ich die Werte auf in Anfang `|*|` Mitte `|*|` und Ende. Da wir wieder einmal mehr Werte haben als in optionSplit angegeben, werden die Werte in der Mitte mehrfach hintereinander abgearbeitet. Soll heißen: Sie wechseln sich ab. Mehr zu optionSplit findet Ihr hier:

https://docs.typo3.org/m/typo3/reference-typoscript/main/en-us/Functions/OptionSplit.html

Und so schaut unser Ergebnis nun aus:

```html
grün
grün
blau
gelb
rot
grün
```

## min und max

Mit diesen Werten können wir angeben, wie viele Werte min oder max angezeigt werden sollen. Auch hier wieder ein kleines Beispiel, indem nur die ersten VIER unserer Wörter in rot angezeigt werden:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split {
  token = ,
  token.noTrimWrap = || |
  cObjNum = 1
  max = 4
  1.current = 1
  1.wrap = <div style="color: #FF0000;">|</div>
}
```

Ergebnis:

```html
grün
grün
blau
gelb
```

Ich habe mir die split-Funktion mal näher angeschaut und festgestellt, das es keinen Sinn macht einen max-Wert anzugeben, der größer ist als die Anzahl in unserem value. Das Script arbeitet dann einfach mit der Anzahl Wörter weiter und interessiert sich nicht für den max-Wert. Dasselbe auch bei min. Min wird nur dann verarbeitet, wenn min größer ist als die Anzahl in unserem value. Deshalb setze ich min in unserem nächsten Beispiel auf 10:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split {
  token = ,
  token.noTrimWrap = || |
  cObjNum = 1
  min = 10
  1.current = 1
  1.wrap = <div style="color: #FF0000;">|</div>
}
```

Ergebnis:

```html
grün
grün
blau
gelb
rot
grün




```

Was soll das? Es werden doch trotzdem nur 6 Wörter angezeigt, werdet ihr fragen. Ja dann schaut doch mal in Euren Quellcode und dort werdet ihr das DIV aus dem wrap noch 4 weitere Male vorfindet.

Wofür das allerdings gut sein, weiß ich nicht. Aber im nächsten Kapitel versuche ich mal ein leidlich vernünftiges Beispiel zu bringen.

## Register SPLIT_COUNT

Mit jedem Durchlauf der Wörter wird ein Wert im TYPO3 internen Register um 1 erhöht. Diesen Wert wollen wir uns mal schnappen und mit unseren Wörtern zusammen ausgeben:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split {
  token = ,
  token.noTrimWrap = || |
  cObjNum = 1
  1.current = 1
  1.dataWrap = <div style="color: #FF0000;">{register:SPLIT_COUNT} = |</div>
}
```

Ergebnis:

```html
0 = grün
1 = grün
2 = blau
3 = gelb
4 = rot
5 = grün
```

Mit dataWrap können wir TEXT und TYPO3-Variablen zusammen ausgeben lassen, aber leider fangen unsere Zahlen mit 0 an...naja...solange die +calc-Funktion in TypoScript kein .data unterstützt, wird das auch erst mal so bleiben :-(

PS: Im Kapitel „Ordered List mit split“ habe ich eine Alternative für Euch

Aber mit diesem Beispiel können wir nochmal auf das Thema: min zurückgreifen. Setzen wir unser min = 10 doch nochmal hier ein. Seht Ihr wie noch die 6, 7, 8, 9 OHNE einem unserer Wörter erscheint?! Ich denke genau für solche Dinge ist die min-Funktion da.

## ReturnKey

Das Thema ist sehr simpel. Kurz: Wollt Ihr nicht, dass split die kompletten Wörter durchgeht, sondern direkt ein Wort anspringt, dann setzt returnKey einfach auf den entsprechenden Wert. Bei unseren 6 Wörter zählt split folgendermaßen: 0,1,2,3,4,5. Wenn wir also das Wort gelb haben wollen, dann müssen wir returnKey = 3 setzen. Wenn ihr mit den Zahlen nicht klarkommt, dann könnt Ihr ja einfach das Script aus dem vorigen Kapitel nehmen, dort werden Euch die Zahlen angezeigt.

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split {
  token = ,
  token.noTrimWrap = || |
  cObjNum = 1
  returnKey = 3
  1.current = 1
  1.wrap = <div style="color: #FF0000;">|</div>
}
```

Ergebnis:

```html
gelb
```

Aber was ist denn jetzt los? Wieso ist unser Wort nicht rot? Ganz einfach: In der Doku steht „SOFORT“. Soll heißen: Mit returnKey greife ich SOFORT und OHNE Umwege auf das gewünschte Wort zu. Für unser Script heißt das...es würde reichen wenn wir es entsprechend abspecken:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.split {
  token = ,
  token.noTrimWrap = || |
  returnKey = 3
}
```

## Ordered List mit split

In diesem Kapitel habe ich für Euch noch eine Idee, wie man die Wörter beginnend mit 1 durchnummerieren kann:

```typo3_typoscript
page.10 = TEXT
page.10.value = grün, grün, blau, gelb, rot, grün
page.10.wrap = <ol>|</ol>
page.10.split {
  token = ,
  token.noTrimWrap = || |
  cObjNum = 1
  wrap = <li>|</li>
  1.current = 1
}
```

Ergebnis:

```html
1.) grün
2.) grün
3.) blau
4.) gelb
5.) rot
6.) grün
```

Hinweis:
Die split-Funktion selbst besitzt kein stdWrap, deshalb musste ich das wrap für die ordered list in das stdWrap vom Objekt TEXT packen. Wie Ihr auch sehen könnt habe ich diesmal das wrap von split verwendet und nicht im Array selbst (1.wrap). Das wrap von split funktioniert GRUNDSÄTZLICH für ALLE Einträge. Ein wrap in den Arrays selbst (1.wrap) jedoch klappt nur dann, wenn es explizit durch cObjNum aufgerufen wurde.

## Split mit Datenbankabfrage

So, jetzt wird’s mal ein bisschen „hardcore“. Denn wir wollen uns nun alle Frontend-User in einer ordered list anzeigen lassen und hier das Script dazu:

```typo3_typoscript
temp.daten = TEXT
temp.daten.cObject = CONTENT
temp.daten.cObject {
  table = fe_users
  select {
    pidInList = 9
  }
  renderObj = TEXT
  renderObj {
    field = name
    required = 1
    wrap = ,|
  }
  stdWrap.substring = 1
}
page.10 = TEXT
page.10 < temp.daten
page.10.wrap = <ol>|</ol>
page.10.split {
  token = ,
  cObjNum = 1
  wrap = <li>|</li>
  1.current = 1
}
```

So, im Bereich temp.daten holen wir uns die Daten aus der fe_users-Tabelle. Wie genau das mit dem Objekt CONTENT funktioniert entnehmt Ihr bitte der TYPO3-Doku. Meine FE-User liegen in Ordner mit der Id 9. Da wir alle User haben wollen, brauchen wir im Bereich select nichts weiter was zu schreiben. Mit renderObj „formatiere“ ich die einzelnen Datensätze. Wir wissen aber, dass wir nachher unsere User wieder per Komma trennen müssen. Also habe ich hier dem renderObj gesagt, dass es bitte VOR allen Usern ein Komma macht, denn Ihr wisst ja noch, es wird nur der Teil VOR dem Komma übernommen. Jetzt haben wir aber leider als erstes Zeichen auch ein Komma, vor dem aber nichts steht. Das entferne ich mit substring = 1. Heißt: Überspringe das erste Zeichen und liefer mir den Rest.

Ich musste das Objekt CONTENT in ein TEXT-Objekt packen, weil sonst unsere Zuweisung (< temp.daten) nicht funktioniert. Das klappt nämlich nur, wenn die Objekte, die gleichen sind.

## Hinweis zum Komma

In meinen Scripten verwende ich meistens das Komma. Das MUSS aber nicht zwingend das Komma sein...es kann auch eine Tilde (~) oder eine Buchstabenfolge wie „abz“ sein. Sobald Euer Script größer wird oder Ihr Inhalte aus der Datenbank holt, könnte es sein, dass in diesem Inhalt auch ein Komma ist und dann wird Euer Inhalt rigeros gesplittet. Um das zu verhindern, empfehle ich Euch für größere Projekte sowas wie ###TRENNER### zu verwenden.

Hier das verändertes Beispiel von oben:

```typo3_typoscript
temp.daten = TEXT
temp.daten.cObject = CONTENT
temp.daten.cObject {
  table = fe_users
  select {
    pidInList = 9
  }
  renderObj = TEXT
  renderObj {
    field = name
    required = 1
    wrap = ###TRENNER###|
  }
  stdWrap.substring = 13
}
page.10 = TEXT
page.10 < temp.daten
page.10.wrap = <ol>|</ol>
page.10.split {
  token = ###TRENNER###
  cObjNum = 1
  wrap = <li>|</li>
  1.current = 1
}
```

Bitte beachtet, dass ihr in diesem Fall auch den substring hochsetzen müsst. Es müssen nun 13 Buchstaben übersprungen werden.
