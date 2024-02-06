+++
title = "RTE Links"
date = 2024-01-09T20:34:06+01:00
aliases = ["rte-links.html"]
+++

Links werden mit folgender Grundkonfiguration aktiviert:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  showButtons = bold, italic, chMode, link
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext
    denyTags = u,img,br,div,center,pre,font,hr,sub,sup,strong,em,li,ul,ol,blockquote,strike,span
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i,a
    }
  }
}
```

Ihr seht: Ich musst den a-Tag in die Liste der allowTags hinzufügen und aus der Liste der denyTags entfernen. Damit wir Links überhaupt verwenden können musste ich noch den Buttons für Links der Liste der showButtons hinzufügen.

## Der böse User und die Klassen

Jetzt sind Links zwar grundsätzlich möglich, aber wir haben noch den User als Risiko nicht ausgeschlossen. Nachdem Ihr einen Link hinzugefügt habt, schaltet mit chMode mal in den HTML-Modus um und fügt dem Link noch die Klasse "tollerLink" hinzu. Nach dem Speichern werdet Ihr sehen, dass diese Klasse mitgespeichert wurde und das obwohl wir mit allowedClasses NUR die bodytext-Klasse erlaubt haben.

Grundsätzlich gebe ich Euch da Recht, aber die allowedClasses-Eigenschaft bezieht sich nur auf Absätze wie p, div, pre und dem Tag span. Damit auch hier nur DIE Klassen erlaubt werden, die wir haben wollen, benötigen wir einen HTMLparser und welcher ist dafür genau der Richtige? Richtig...der entryHTMLparser_db:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  showButtons = bold, italic, chMode, link
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext
    denyTags = u,img,br,div,center,pre,font,hr,sub,sup,strong,em,li,ul,ol,blockquote,strike,span
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i,a
      tags.a.fixAttrib.class.list = tollerLink1,tollerLink2,tollerLink3
      tags.a.fixAttrib.class.default = tollerLink1
    }
  }
}
```

Erklärung:

Mit der Eigenschaft "tags" kann ich auf ein beliebigen HTML-Tag zugreifen und Dank der "fixAttrib"-Eigenschaft die enthaltenen Attribute nach belieben verändern, bearbeiten und überprüfen. In unserem Fall habe ich mich für "list" entschieden. Hier kann ich nun alle Klassen eintragen, die für das a-Tag erlaubt werden sollen. Wenn ein Benutzer meint eine andere Klasse verwenden zu müssen, dann wird seine Klasse mit der ersten Klasse aus unserer Liste ersetzt. Wird überhaupt keine Klasse gesetzt bzw. ist das komplette class-Attribut nicht vorhanden, dann wird Dank der zweiten Eigenschaft "default" das fehlende class="tollerLink1" gesetzt.

## Klassen auswählbar machen

Toll, jetzt haben wir 3 Klassen definiert die auf dem Weg zur Datenbank erlaubt sind, aber wir können dem User doch nicht abverlangen, diese Klassen im HTML-Modus des RTE einzutragen. Deshalb müssen wir unsere Konfiguration nochmals bearbeiten:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.classesAnchor {
  tollerLink1 {
    class = tollerLink1
    type = page
    titleText = Link1 zu einer Seite
  }
  tollerLink2 {
    class = tollerLink2
    type = page
    titleText = Link2 zu einer Seite
  }
  tollerLink3 {
    class = tollerLink3
    type = page
    titleText = Link3 zu einer Seite
  }
}

RTE.default {
  showButtons = bold, italic, chMode, link
  classesAnchor = tollerLink1,tollerLink2,tollerLink3
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext
    denyTags = u,img,br,div,center,pre,font,hr,sub,sup,strong,em,li,ul,ol,blockquote,strike,span
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i,a
      tags.a.fixAttrib.class.list = tollerLink1,tollerLink2,tollerLink3
      tags.a.fixAttrib.class.default = tollerLink1
    }
  }
}
```

Holy shit, was ist das?

Fangen wir wieder einfach an. Ihr seht, dass ich nun eine weitere Eigenschaft mit dem Namen RTE.default.classesAnchor eingefügt habe. Diese Eigenschaft hat was mit dem PopUp-Fenster für Links zu tun. Denn dort gibt es eine Selectbox, aus der Ihr von den zuvor definierten Klassen in RTE.classesAnchor auswählen könnt (siehe Bild). Ihr könnt also unter RTE.classesAnchor so viele Klassen definieren wie Ihr wollt, aber wenn Ihr diese Klassen in RTE.default.classesAnchor nicht eintragt, dann erscheint diese Klasse auch nicht in der Selectbox des Link-PopUp-Fensters.

## RTE.classesAnchor

Diesem Thema widme ich eine eigene Überschrift, denn es ist nicht in 1-2 Sätzen erklärt.

In RTE.classesAnchor werden eindeutige Link-Namen vergeben. Ich sage deshalb "Namen", weil sie nichts mit dem class-Attribut unserer Links zu tun haben. Hier könnt Ihr Eurem Link einen TYPO3-internen Namen geben wie "tannenzapfen", "linkGruen" oder "link-seite-extern". Es liegt allein in Eurer Entscheidung, ob Ihr den Namen gleich der Klasse setzt. Allein um eine saubere Struktur zu haben würde ich es aber empfehlen.

Gut: Wir haben in unserem Beispiel also den Linknamen "tollerLink1" und der hat eine Eigenschaft mit dem Namen "class". Hier gebt Ihr nun die Link-CSS-Klasse an. In meinem Beispiel ist der Name gleich der Klasse...also "tollerLink1".

Die "type"-Eigenschaft bezieht sich auf die Tabs, die Ihr im PopUp-Fenster habt: Seite (page), Datei (file), Externe URL (url), E-Mail (mail). Darüber hinaus gibt es auch noch den type "spec" für selbsterstellte Linkarten. Aber da gehe ich hier nicht weiter drauf ein. In unserem Fall ist type=page und somit wird die Klasse tollerLink1 auf dem Tab für interne Seitenlinks angezeigt.

Wichtig: In der Eigenschaft "type" darf nur EIN Wert stehen. Bitte keine kommaseparierten Listen eintragen. Falls Euer Wert trotzdem angezeigt werden sollte, dann liegt das daran, dass der Linkname den gleichen Namen hat wie die Linkklasse. Testet es und tragt in diesem Falle mal url,email in "type" ein und Ihr werdet feststellen, dass Euer Linkname auf allen Tabs erscheint, oder bei unterschiedlichen Namen gar nicht mehr erscheint.
Zu guter Letzt haben wir noch die Eigenschaft "titleText". Der Wert dieser Eigenschaft wird in das title-Attribut des a-Tags hinzugefügt.

Zusätzlich zu diesen 3 Eigenschaften gibt es auch noch die Eigenschaft "image". Damit könnt Ihr ein Bild angeben, dass vor dem eigentlichen Link angezeigt werden soll. Mit der letzten Eigenschaft "altText" könnt Ihr dann dem img-Tag noch das title-Attribut hinzufügen.

Nur mal zum Testen: Ändert von dem Linknamen "tollerLink2" die Eigenschaft type mal auf "mail" und Ihr werdet sehen, dass diese Linkklasse nur noch für E-Mails zur Verfügung steht.

Wichtig:
Die Linknamen werden nur EINMAL angegeben und danach brauchen wir die nicht mehr. Kommt also bitte nicht auf die Idee, die Linknamen in die Liste der classesAnchors einzutragen. Denn dann erscheint der Linkname in der Selectbox in JEDEM Tab des PopUp-Fensters. Die Berechtigung "type" greift dann auch nicht mehr! Deshalb hier noch mal das gleiche Beispiel von oben nur mit anderen Linknamen:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.classesAnchor {
  wasEinLink {
    class = tollerLink1
    type = page
    titleText = Link1 zu einer Seite
  }
  tannenzapfen {
    class = tollerLink2
    type = mail
    titleText = Link2 zu einer Seite
  }
  TYPO3rulez {
    class = tollerLink3
    type = page
    titleText = Link3 zu einer Seite
  }
}

RTE.default {
  showButtons = bold, italic, chMode, link
  classesAnchor = tollerLink1,tollerLink2,tollerLink3
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext
    denyTags = u,img,br,div,center,pre,font,hr,sub,sup,strong,em,li,ul,ol,blockquote,strike,span
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i,a
      tags.a.fixAttrib.class.list = tollerLink1,tollerLink2,tollerLink3
      tags.a.fixAttrib.class.default = tollerLink1
    }
  }
}
```
