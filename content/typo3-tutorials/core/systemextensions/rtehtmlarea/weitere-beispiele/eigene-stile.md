+++
title = "Eigene Stile"
date = 2024-01-09T20:34:00+01:00
aliases = ["eigene-stile.html"]
+++

In diesem Tutorial wollen wir unseren RTE mit eigenen Block- und Textstilen versehen. Damit das funktioniert benötigen wir eine CSS-Datei auf die der RTE zugreifen kann. Der RTE wird also im Bereich Block- und Textstilen nicht mit irgendwelchen Objekten und Eigenschaften konfiguriert, sondern er greift auf eine CSS-Datei zu und läd die dort vorhandenen Stile. Es kann schon mal sein, dass der RTE diese Datei nicht sofort ließt und ihr erstmal den Cache des Browsers löschen müsst. Manchmal muss sogar der Browser einmal neu gestartet werden. Aber wenn der RTE die CSS-Datei dann endlich mal hat, dann benötigt Ihr bei Änderungen der CSS-Datei nur noch ein Clear Cache in TYPO3.

## Absatz mit grauem Hintergrund

In diesem Beispiel wollen wir einem ganz normalen Absatz eine graue Hintergrundfarbe verpassen und die Schriftart "Courier New" verwenden. Ganz normaler Text wird im RTE mit dem p-Tag realisiert, also müssen wir in der CSS-Datei (z.B. rte.css) dem p-Tag eine Klasse zuweisen:

```css
p.grauerHG {
    background-color: #C0C0C0;
    font-family: Courier New;
}
```

Folgende pageTSconfig ist für den RTE notwendig. Ich habe hier wieder unseren Code von der RTE-Startseite genommen und schon mal weiter angepasst:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  contentCSS = fileadmin/templates/froemken/style/rte.css
  showButtons = bold, italic, chMode, blockstyle, textstyle
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext,grauerHG
    denyTags = u,a,img,br,div,center,pre,font,hr,sub,sup,strong,em,li,ul,ol,blockquote,strike,span
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i
    }
  }
}
```

Neu hinzugekommen ist contentCSS mit dem wir die CSS-Datei angeben in der die benötigten Styleangeben hinterlegt sind. Wir dürfen nicht vergessen unsere neue Klasse "grauerHG" mit in der Liste der allowedClasses einzufügen, denn sonst verschwindet unser schöner Hintergrund beim Speichern. Zum Schluss habe ich auch noch weitere Tags für den RTE aktiviert: blockstyle und textstyle. Wer mag kann auch noch das Label für diese beiden Felder aktivieren. Dazu muss unser showButtons folgendermaßen aussehen:

```typo3_typoscript
showButtons = bold, italic, chMode, blockstyle, blockstylelabel, textstyle, textstylelabel
```

## blockstyle

Bei einem Block handelt es sich um einen Absatz, wie wir Ihn aus Word kennen. Ein Absatz ist eine Aneinanderreihung von Wörtern bzw. Sätzen bis einmal Enter gedrückt wurde. Kurz: Text zwischen zwei ENTER bezeichnet man als Absatz. Um einen Absatz zu formatieren reicht es im RTE aus in den Absatz reinzuklicken. Der Cursor muss also nur irgendwo innerhalb des Absatzes stehen, damit die Blockstyle-Selectbox aktiv wird.

## textstyle

Textstile befinden sich normalerweise nur innerhalb eines Absatzes und werden mit Markierungen definiert. Dabei muss es nicht nur ein Wort sein, sondern kann auch nur einen Teil eines Wortes betreffen. Textstile lassen sich im RTE allerdings auch Absatzübergreifend definieren, allerdings sollte das nicht zum Standard werden.

## Textstile

Wir wollen nun einen Textstil erzeugen. Nehmen wir mal einen roten Hintergrund mit blauer Schriftfarbe und einem Zeichenabstand von 3 Pixeln. Während p, h1, hx... Blockstile sind, müssen wir Textstile in der CSS-Datei mit span-Tags definieren:

```css
span.ekelig {
    background-color: red;
    color: blue;
    letter-spacing: 3px;
}
```

Hier die pageTSconfig dazu:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  contentCSS = fileadmin/templates/style/rte.css
  showButtons = bold, italic, chMode, blockstyle, blockstylelabel, textstyle, textstylelabel
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext,grauerHG,ekelig
    denyTags = u,a,img,br,div,center,pre,font,hr,sub,sup,strong,em,li,ul,ol,blockquote,strike
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i,span
    }
  }
}
```

Wie Ihr seht musste ich die Klasse "ekelig" in die Liste der allowedClasses hinzufügen. Ich habe Euch oben schon geschildert, das Textstile mit dem span-Tag arbeiten. Deshalb musste ich den span-Tag der Eigenschaft "allowTags" hinzufügen und aus der Liste der denyTags entfernen. Ohne diese Änderungen würde unser Effekt beim Speichern einfach verschwinden.

## Überschriften und Blockstile

Ein Absatz kann nicht nur der p-Tag sein. Nein! Es gibt noch weitere Absatzarten. Dazu folgende Aufstellung:

p: Normaler Textabsatz
h1: Überschrift größe 1
h2: Überschrift größe 1
h3: Überschrift größe 1
h4: Überschrift größe 1
h5: Überschrift größe 1
h6: Überschrift größe 1
pre: Leerzeichen bleiben erhalten
ul/ol: Auflistungen
div: eine Textbox
address: Für Adressen
blockquote: eingerückter Text für z.B. Zitate

Um auf diese Absatzarten zuzugreifen benötigen wir noch den Button "formatblock":

```typo3_typoscript
showButtons = bold, italic, chMode, formatblock, blockstyle, blockstylelabel, textstyle, textstylelabel
```

Wie Ihr hier im Bild erkennen könnt erhaltet Ihr nun eine weitere Selectbox mit der Ihr aus den vorgegebenen Absatzarten wählen könnt. Im Zusammenspiel mit dem Blockformat ist eins wichtig: Die Blockformate ändern sich je nach Auswahl der Absatzart. So kann die Klasse "code" zum Beispiel nur für die Absatzart "Vorformatiert" und die Klasse "blauerHintergrund" nur für die Absatzart "Überschrift 1" definiert werden. Lasst uns solch ein Beispiel mal machen:

CSS-Datei:

```css
pre.code {
    background-color: #DFDFDF;
    color: #000000;
    font-family: Courier New;
}

h1.blauerHintergrund {
    background-color: #0000FF;
    color: #000000;
    font-family: Arial;
}

span.ekelig {
    background-color: red;
    color: blue;
    letter-spacing: 3px;
}
```

pageTSconfig:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  contentCSS = fileadmin/templates/style/rte.css
  showButtons = bold, italic, chMode, formatblock, blockstyle, blockstylelabel, textstyle, textstylelabel
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext, blauerHintergrund, ekelig, code
    denyTags = u,a,img,br,center,font,hr,sub,sup,strong,em,li,ul,ol,strike
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i,blockquote,pre,h1,h2,h3,h4,h5,h6,span,div,address
    }
  }
}
```

Zurück im RTE klicken wir unseren Absatz an und stellen fest, dass die Selectbox blockstyle ausgegraut bleibt. Richtig. Denn für den einfachen Absatz (p-Tag) haben wir keine Klasse in der CSS-Datei erstellt. Nun wählen wir mal "Vorformatiert" aus und sofort haben wir in der Blockstyle-Selectbox die Möglichkeit auf die Klasse "code" umzuswitchen. Wenn wir nun auf den anderen Blockstil wechseln wollen, müssen wir vorher den Absatz "Vorformatiert" mit "Blockformat entfernen" löschen und können nun auf den Absatz "Überschrift 1" wechseln. Nun bietet uns Blockstyle die Klasse "blauerHintergrund" an.

Das mit dem vorigen Löschen von Absatzarten ist etwas verwirrend, aber wenn Ihr das nicht macht, dann werden die Klassen mit jedem Klick auf eine Absatzart einer vorhandenen Klasse hinzuaddiert und das Ergebnis resultiert im Chaos. Also löscht die Absatzarten vor einem Wechsel.

In unserem TSconfig seht ihr, dass ich alle Absatzarten der Liste der allowTags hinzufügen musste. Auch die Klassen "blauerHintergrund" und "code" musste ich der Eigenschaft allowClasses hinzufügen.

# Ein Textstil für alle

Um eine Klasse für alle Block- und Textstile zu erlauben, dürfen wir für unsere gewünschte Klasse keine Block- und span-Tags angeben. Hier ein Beispiel:

CSS-Datei:

```css
.ekelig {
    background-color: red;
    color: blue;
    letter-spacing: 3px;
}
```

Da solche globalen Klassen das Design einer Webseite sehr schnell versauen können, hat TYPO3 eine Eigenschaft erfunden, die diese globalen Klassen grundsätzlich erstmal deaktiviert. Wir müssen diese also erstmal freischalten:

```typo3_typoscript
showTagFreeClasses = 1
```

Jetzt können wir im RTE die Klasse sowohl für alle Absatzarten als auch für alle Textstile definieren.
