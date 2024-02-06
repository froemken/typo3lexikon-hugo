+++
title = "Enable-Word-Clean"
date = 2024-01-09T20:32:02+01:00
aliases = ["enablewordclean.html"]
+++

Beim Kopieren von externen Texten wie z.B. aus Word oder auch von anderen Webseiten in den RTE werden alle HTML-Tags übernommen. Im RTE erscheinen die riesigen bunten Überschriften, die Bilder sind sichtbar, Floatings werden übernommen. Naja..eigentlich kann man sagen, dass wir mit Copy&Paste ein fremdes Design in unseren RTE implementieren.

Mit enableWordClean = 1 werden ein paar wenige dieser Fremdstile beim Einfügen in den RTE entfernt.

Was die Wenigsten jedoch nicht wissen ist, dass enableWordClean schon seit ein paar Jahren auch das Objekt HTMLparser mitliefert. Denn damit könnt Ihr die Funktionalität von enableWordClean gewaltig aufbohren.

Schauen wir uns folgende Konfiguration an:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  showButtons = bold, italic, underline, chMode
  enableWordClean = 1
  enableWordClean.HTMLparser = 1
  enableWordClean.HTMLparser {
  allowTags = p,b,strong,i,em,u
    #tags.span.allowedAttribs = id
  }
}
```

Wir haben hier einen RTE mit den Buttons für Fett, Kursiv und Unterstrichen. Demnach wäre es doch schön, wenn Ihr nach dem Einfügen von Texten einer Fremdwebseite auch nur Inhalte im RTE erhaltet, die diese 3 Möglichkeiten beinhalten. Dafür ist die Zeile allowTags zuständig. Versucht es doch mal und fügt nun z.B. einen Newsbeitrag von www.spiegel.de oder sowas ein.

Cool, oder? Alle Überschriften und Bilder sind weg. Wir haben nur noch den reinen Text.

Aber Moment: Wenn Ihr mit dem Button chMode in den HTML-Quellcode wechselt befinden sich zumindest noch span-Tags im Quellcode und das obwohl wir diese gar nicht erlaubt haben.

Das liegt nicht an Euch, sondern an der Verarbeitungsweise des RTEs was die Konfigurationseinstellungen von HTMLparsern angeht. Denn hier funkt Euch die Methode keepSpanTagsWithId aus dem Objekt ContentParser dazwischen. Originalzitat aus dem Quelltext: "Such tags are used by the RTE in order to restore the cursor position when the cleaning operation is completed."

Also irgendwie braucht der RTE diese id-Attribute in span-Tags, um die Cursorposition nach dem Einfügen wiederherzustellen. Ich kann das nicht ganz nachvollziehen, aber diese Methode ist da und wir müssen erst mal damit leben. In meinen Augen bringt diese Methode jedoch ein Problem mit: Denn diese Methode merkt nun, dass der span-Tag nicht erlaubt ist und fügt diesen nun explizit hinzu. allowTags wird um span erweitert, span wird aus der noAttrib- und removeTags-Eigenschaft wieder entfernt und somit kann span tun und lassen, was es will. Es darf alle möglichen Klassen und Attribute enthalten. Das id-Attribut kann auch nicht mit unset entfernt werden.

Wir sind nun also gezwungen einen unerwünschten Tag zu konfigurieren, deshalb oben noch die kommentierte Zeile. Erst wenn Ihr diese aktiviert, ist wirklich NUR noch das id-Attribut erlaubt und mehr nicht.
