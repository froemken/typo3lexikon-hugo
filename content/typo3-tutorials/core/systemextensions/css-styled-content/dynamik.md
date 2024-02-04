+++
title = 'Dynamik'
date = 2024-01-09T20:19:47+01:00
aliases = ["dynamik.html"]
+++

Ich hab bei mir die Extension `templavoila_framework` installiert und bin bei der Analyse über etwas sehr Interessantes gestoßen:

Mit dieser Extension können unter anderem Inhaltselemente erstellt werden, die aus 2-4 Spalten bestehen. Das Schöne daran: Wenn in diese Spalten Bilder eingefügt werden, dann werden die automatisch an die Größe der Spaltenbreite angepasst und das ohne einfach die Bildgröße über `height` und `width` des `img` Tags einzustellen.

Ich habe eine kleine Beispielwebseite zusammengestrickt und die Breite der Webseite auf 400px begrenzt. Diese Begrenzung spiegelt die schwarze Linie auf dem Bild hier rechts wider. Mit dem Konstanten-Editor habe ich auch die Standardbildbreite von 600px auf 400px verringert. Das hat aber den Hintergrund, das meine Beispielseite sonst nicht komplett auf das Bild passen würde.

Mein erstes Inhaltselement ist einspaltig und beinhaltet ein Bild, das als "oben mittig" ausgerichtet wurde und Dank meiner 400px-Einstellung damit die komplette Webseitenbreite ausfüllt.

Das zweite Inhaltselement ist ein FCE mit 2 Spalten. Der Inhaltspflege wurde als "Page-Content Elements (Pos.: 0)" realisiert und hat folgenden Aufbau:

```html
<html>
  <head>
    <title>hallo</title>
  </head>
  <body>
    ###CONTENTLINKS###
    ###CONTENTRECHTS###
  </body>
</html>
```

Stylesheet

```css
#linkeSpalte {
    float: left;
    width: 190px;
}

#rechteSpalte {
    float: right;
    width: 190px;
}

.clearer {
    clear: both;
    height: .1px;
    font-size: 0;
    line-height: 0;
}
```

Ihr seht, dass ich jede Spalte auf 190px begrenzt habe. Ihr seht aber auch, dass die beiden Bilder sich überlagern und sich nicht für die 190px Breite interessieren. Dank dem eingeblendeten Lineal seht ihr dass die Bilder weiterhin ihre 400px Breite haben.

## Register in css_styled_content

Geht im Backend mal auf `Template` dann auf `Rootseite` und wählt oben aus der DropDown-Box den "Template-Analyser" aus und klickt dann auf den Eintrag: `EXT:css_styled_content/static/`. Danach erhaltet ihr eine schier unendlich lange Liste mit TypoScript Variablen.

Such jetzt mal mithilfe von STRG+F nach dem Suchwort: `LOAD_REGISTER`. Wenn ihr richtig geklickt habt, dann werdet ihr 2 Einträge finden.

Jetzt sucht mal nach dem Suchwort `register:`. Jede Wette, dass ihr nun weit mehr Einträge finden werdet. Aber warum wird sooft auf Variablen zugegriffen, wenn diese doch nirgends mit `LOAD_REGISTER` erstellt wurden.

Die Lösung ist ganz einfach: Diese Variablen sind für uns!

Schaut euch mal den Codeabschnitt für `maxW` im "Typoscript Objekt-Browser" an (Bild rechts). Dort seht ihr auch meine Bildbreite von 400 Pixeln. Aber wenn ihr mal auf die Pluszeichen davor klickt, öffnet sich eine interessante Information: Dort steht ein `override`. Heißt: Wenn wir selbst diese Variable erstellen, dann wird meine Voreinstellung von 400 Pixeln überschrieben.

## Crashkurs: LOAD_REGISTER

Mit `LOAD_REGISTER` könnt ihr Variablen erstellen auf die ihr später im TypoScript wieder zugreifen wollt. Ein Beispiel:

```typo3_typoscript
10 = LOAD_REGISTER
10.meineVariable = Ich bin der Inhalt
20 = TEXT
20.data = register:meineVariable
30 = RESTORE_REGISTER
```

In der 2ten Zeile weise ich den Text "Ich bin der Inhalt" der Variable `meineVariable` zu. Mit `register:[Variablenname]` kann ich dann wieder mithilfe der `data` Eigenschaft aus dem `stdWrap` Objekt wieder auf den Inhalt zugreifen. Damit der Variablenname nicht noch für alle weiteren folgenden Zeilen zur Verfügung steht, kann ich die Variable mit `RESTORE_REGISTER` wieder löschen.

## Die Register nutzen

Nach dieser langen Einleitung wollen wir das Gelernte nun endlich auch nutzen. Schauen wir uns dazu mal mein FCE an. Im Mappingbereich können wir in den neueren TemplaVoilaversionen auf "Data processing" klicken und sehen mit welchem TypoScript-Code der Inhalt aus der Datenbank ausgewählt wird und genau hier können wir ansetzen.

Ersetzt den 3-Zeiligen Code mal durch den Folgenden:

```typo3_typoscript
5 = LOAD_REGISTER
5.maxImageWidth = 190
10= RECORDS
10.source.current=1
10.tables = tt_content
15 = RESTORE_REGISTER
```

## Das erste Ergebnis

Nach einem Clear-Cache könnt ihr euch das Ergebnis (hier im rechten Bild) anschauen.

Ihr seht selbst, dass die beiden Bilder nun perfekt in die beiden Spalten reinpassen.

Das Einzige, was noch fehlt, ist die etwas einfachere Konfiguration dieser Breite. Denn derzeit müsst ihr immer noch tief in die Einstellungen des FCEs eingreifen. Die bessere Möglichkeit wäre wohl die Konfiguration über TypoScript im Haupttemplate.

```typo3_typoscript
5 = LOAD_REGISTER
5.maxImageWidth = {$imageWidth}
10= RECORDS
10.source.current=1
10.tables = tt_content
15 = RESTORE_REGISTER
```

$_CONSTANTS ist ein besonderes Präfix, der es euch erlaubt, die durch einen Punkt getrennten Variablennamen mithilfe von TypoScript in eurem Haupttemplate zu füllen.

Geht also auf Template dann auf Rootseite wählt `Info/modify` und dann `Setup` und fügt dort folgende Zeile ein:

```typo3_typoscript
_CONSTANTS.imageWidth = 50
```

Nach einem ClearCache erscheinen die beiden Bilder in den Spalten mit einer Breite von nur noch 50 Pixeln.

## Vereinfacherung der Konfiguration (Teil 2)

Da der Setupbereich schon mal etwas größer werden kann, wäre es vielleicht gar nicht verkehrt, diese Variable auch als Konstante verfügbar zu machen. Ersetzt die eine Zeile in Eurem Setupbereich durch die Folgende:

```typo3_typoscript
_CONSTANTS.imageWidth = {$imageWidth}
```

und in den Konstantenbereich des Templates fügt ihr folgenden Code ein:

```typo3_typoscript
imageWidth = 75
```

Als Ergebnis sind die Bilder nun 75 Pixel breit.

## und noch eine Möglich mit unserem FCE zu kommunizieren

Löscht im FCE die Zeile mit den Konstanten wieder raus und ändert den TypoScript-Code folgendermaßen ab:

```typo3_typoscript
5 = LOAD_REGISTER
5.maxImageWidth = {$TSconst.imageWidth}
10 = RECORDS
10.source.current=1
10.tables = tt_content
15 = RESTORE_REGISTER
```

Im Setupbereich eures Templates könnt ihr nun direkt auf diese besondere Variable zugreifen:

```typo3_typoscript
plugin.tx_templavoila_pi1.TSconst.imageWidth = {$imageWidth}
```

## Ein letztes Schmankerl

Damit sich unsere Konstante auch bequem über den Konstanten-Editor von TYPO3 konfigurieren lässt, müsst ihr den Einzeiler in den Konstanten folgendermaßen ändern:

```typo3_typoscript
# cat=fce/imageWidth; type=int+; label=Bildbreite in Spalten:Mit dieser Konstante könnt ihr den Bildern in den Spalten eine neue Größe zuweisen
imageWidth = 50
```

Jetzt erhaltet ihr im Konstanten-Editor im DropDown-Menü einen neuen Eintrag fce. Dort seht ihr nun unseren neuen Eintrag und könnt diesen nun bequem ändern.
