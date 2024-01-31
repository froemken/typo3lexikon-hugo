+++
title = "xflextemplate"
date = 2024-01-09T20:53:55+01:00
aliases = ["xflextemplate.html"]
+++

Bei teilweise 20 FCEs in einem Projekt war es an der Zeit mal eine neue Idee für ContentElemente zu suchen. Dabei bin ich auf `xflextemplate` gestoßen. Aber wie funktioniert das Teil überhaupt? Ganz ehrlich? Verstanden habe ich die Ext noch nicht ganz, aber ich konnte erste Teilerfolge erzielen, die ich hier präsentieren möchte:

Nach der Installation dieser Extension wird eine neue Tabelle erstellt, in der noch das Feld "file" fehlt. Jeder der sich leitlich mit der ext_tables.sql auskennt wird wissen wie das geht. Ich persönlich hab einfach den title kopiert und in file umbenannt. Wenn ihr das nicht macht, könnte es passieren, dass ihr entsprechende Fehlermeldungen auf dem FrontEnd erhaltet.

Bevor Ihr weitermacht, wählt xFlexTemplate noch in den statischen Templates aus.

In der linken Navigation findet ihr nun eine neue Fläche mit dem Namen: xFlexTemplate. Ihr findet nun 5 Tabs vor, die ich jetzt mal kurz erleutere:

Tab1
> Hier könnt Ihr einen Namen für Euer Template hinterlegen. Aber bedenkt, dass der Name in dieser Liste einmalig sein muss.

Tab 2
> Bei Bedarf könnt Ihr hier einen beschreibenden Text hinterlegen

Tab 3
> Hier könnt Ihr TypoScript für das Template hinterlegen. Ihr habe hier alle TS-Möglichkeiten wie im Main-Template auch.

Tab 4
> Fügt hier Euer Template für das ContentElement ein. TemplaVoila-Fans werden hier eine Ähnlichkeit zu den FCEs wiederfinden.

Tab 5
> Laut Doku ist das der wichtigste Part der Extension. Warum dieser Tab dann nicht an Position 3 ist, ist mir schleierhaft.

Auf jeden Fall müsst Ihr hier Eure Felder definieren. Eine genauere Erklärung kommt.

### Ein Beispiel

Wir wollen einfach mal zwei Texte mit diesem System ausgeben. Eigentlich würde ich gerne noch einen Titel mit reinnehmen, aber das klappt irgendwie nicht mit dieser Ext.

Also dann, gehen wir zuerst in den letzten Tab 5 und legen einen neuen Eintrag an mit dem Titel: "Text1", ElementType ist "Text", Palette lassen wir leer und in RenderType kommt auch "Text" rein.
Danach erstellen wir noch ein solches Element direkt da drunter und setzen den Titel auf "Text2" mit genau den gleichen Einstellungen wie bei "Text1"

Jetzt rüber in Tab 4:

```html
###TEXT1###
<hr />
###TEXT2###
```

Diese beiden Marker müssen genauso heißen wie die Titel, die wir in Tab 5 definiert haben. Ich schlage hier vor, diese Marker wie hier im Beispiel in GROSSSCHRIFT zu hinterlegen.

## TypoScript in Tab 3:

language.Text1.beLabel.default = Text oben
language.Text2.beLabel.default = Text unten

Diese Zeilen sind nicht zwingend erforderlich, aber ohne sie habt Ihr später als Überschrift der einzelen Felder einfach nur einen grauen Balken.

Jetzt wollen wir den ertsen Text fett machen und den zweiten Text einfach so lassen wie er ist. Dazu reicht folgendes TS:

```typo3_typoscript
Text1.wrap = <strong>|</strong>
Text2.wrap = |
```

Fertig ist unser xFlexTemplate.

## CE einsetzen

Nachdem wir unser Template gespeichert haben, können wir nun auf eine beliebige Seite gehen und ein neues ContentElement auswählen. Leider erscheint bei mit nur das Symbol. Kein Text, keine Beschreibung und oben habe ich 2 Warnings...naja...dann klicken wir mal auf das Symbol.

Im folgenden Fenster müssen wir nun unser erstelltes Template aus einer Selectbox auswählen. Nach einer Meldung, die wir mit "OK" bestätigen erscheinen nun unsere beiden erstellten Textboxen.

Einfach Inhalt einfügen, Speichern, Cache leeren und im FrontEnd bestaunen.

## Funktionalität

Ich glaube, dass die Extension direkt mit der API von TYPO3 zusammen arbeitet (so auch die Doku). Soll heißen in der SelectBox kann ich die Feldart auswählen, wie sie im Backend (ContentElement) erscheinen soll. Das reine Anzeigen klappt auch mit dem Wert "input".
Die SelectBox renderType scheint sowas wie 10 = TEXT bzw. 10 = COA abzubilden. Wenn das nicht gemacht wird ([none]), dann müsst Ihr per TypoScript angeben, welchen RenderType das Element erhalten soll:

```typo3_typoscript
Text1 = TEXT
```

Was und wie das mit dem Paletten funktioniert weiß ich auch nicht und die Doku verweist auf die Doku der Core API.

## Persönliches

Ich glaube, dass diese Extension viel Potenzial hat. Doch leider sind Versuche mit dem ElementType "Input" gnadenlos in die Hose gegangen. Ich habe es nach zig Versuchen nicht geschafft den Text einer einfachen InputBox auf der Webseite darzustellen. Die Doku ist noch sehr "ungefüllt" und auch die Hoffnung auf ein Tutorial stirbt zuletzt. Ich habe dem Extwickler meine Fehlermeldungen zugeschickt, wir können gespannt sein, was in Zukunft noch alles passieren wird.
