+++
title = "Wrap Bodytext"
date = 2024-01-09T20:56:34+01:00
aliases = ["wrap-bodytext.html", "wrap-um-bodytext.html"]
+++

## Ein zusätzlicher Wrap um den Bodytext

Ich bin letztens gefragt worden, ob ich nicht eine Möglichkeit kenne mit Hilfe von TypoScript NUR den reinen Inhalt eines Inhaltselementes OHNE die enthaltene Überschrift zu wrappen. Damit könne man das Layout viel besser stylen. Zum Beispiel mit Hilfe einer weiteren CSS-Klasse.

Wie Ihr hier auf dieser Seite sehen könnt erscheint der bodytext in einem hellen grün. Die Überschriften bleiben davon jedoch ausgeschlossen. Diese Möglichkeit wird häufig benötigt, wenn Ihr den Inhalt einrahmen/umrahmen wollt.

## Der normale Wrap

Wenn Ihr mit css_styled_content arbeitet, wird der bodytext in dieser Form gerendert:

```html
<div class="csc-default" id="c89">
    <div class="csc-header csc-header-n1">
        <h1 class="csc-firstHeader">Bearbeitete Seiten anzeigen</h1>
    </div>
    <p class="bodytext">Der Inhaltstext</p>
</div>
```

Egal mit welchem Wrap wir in css_styled_content arbeiten, es gibt keine Möglichkeit den p-Tag mit der bodytext-Klasse nochmals zu wrappen. Zwar kann ich mit dem Contentelement TEXT den Inhalt nochmals wrappen, aber in einem IMAGE-Element gibts nur Userfunktionen. Evtl. gibts auch hier eine Möglichkeit den Inhalt zu wrappen, aber stellt Euch mal vor wie lange Ihr brauchen würdet, für jedes Inhaltselement die richtige Wrapping-Eigenschaft zu finden, wenn überhaupt vorhanden.

## Der neue Wrap

Ich habe gut 2 Stunden rumexperimentiert und habe schlussendlich diese Lösung gefunden:

```typo3_typoscript
lib.stdheader.stdWrap.append = TEXT
#lib.stdheader.stdWrap.append.if.isTrue.field = section_frame
lib.stdheader.stdWrap.append.value = ###WRAP###
tt_content.stdWrap {  
  split {   
    token = ###WRAP### 
    cObjNum = 1 || 2
    1 { 
      current = 1
    }    
    2 {
      current = 1      
      wrap = <div class="myClass">|</div>    
    } 
  }
}
```

Mit diesem Konstrukt füge ich der Überschrift ganz am Ende einen Marker ###WRAP### hinzu. Mit dem split-Objekt suche ich nun nach diesem Marker und kann damit die Überschrift sauber vom Inhaltstext abtrennen. Mit cObjNum kann ich nun entscheiden, was mit dem ersten (1: Überschrift) und was mit dem zweiten (2: dem Inhalt) passieren soll. Der erste Bereich wird einfach 1zu1 zurückgegeben (current = 1). Nur der zweite Bereich (der Inhalt) wird nun mit dem zusätzlichen div gewrappt.

Ich habe in den Quellcode noch eine kommentierte Zeile eingefügt. Damit könnt Ihr bei Bedarf festlegen, dass der Inhaltswrap nur dann ausgeführt wird, wenn z.B. ein Rahmen ausgewählt wurde.
