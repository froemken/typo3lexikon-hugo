+++
title = "IMAGE"
date = 2024-01-09T20:58:10+01:00
aliases = ["image.html"]
+++

Dieses Contentobjekt erstellt ein Bild inklusive IMG-Tag.

## file (imgResource)

Pfad zu dem oder den Bildern.

## params

Parameter, die Ihr dem IMG-Tag hinzufügen könnt. Z.B.

```typo3_typoscript
10 = IMAGE
10.params = class="myImage"
```

oder mehrere hinter ein ander

```typo3_typoscript
10 = IMAGE
10.params = class="myImage" id="logo" name="Firmenlogo"
```

## border

Setzt einen Wert für das IMG-Attribut "border". Anzugeben ist hier einen Zahlenwert.

## altText

Mit diesem Wert könnt Ihr dem IMG-Attribut einen alternativen Text zuweisen. Dieser alternative Text wird in einigen Browser in einem kleinen gelben Rahmen angezeigt, wenn Ihr mit der Maus ein paar Sekunden über dem Bild stehen bleibt. Außerdem wird dieser Text angezeigt, wenn das Bild nicht geladen werden konnte oder im Browser eingestellt wurde, dass Bilder nicht angezeigt werden sollen.

## titleText

Jedem IMG-Tag könnt Ihr ein Titelattribut zuweisen. Dieses Attribut solltet Ihr für jedes Bild setzen, da dieser Wert für Suchmaschinen interessant ist.

## alttext

Bitte verwendet diese Eigenschaft nicht mehr. Laut englischer Doku ist diese Eigenschaft die alte Schreibweise von altText. Man beachte hier die Groß- und Kleinschreibung. Dieser Wert wird nur noch dann verwendet, wenn altText nichts zurückliefern sollte.

## emptyTitleHandling

Bei dieser Eigenschaft sind nur 2 Werte gültig:

keepEmpty: Verwendet diesen Wert, wenn das Titelattribut leer bleiben soll.

useAlt: Das Titelattribut wird mit dem Wert aus altText überschrieben.

## longdescURL

Auch das ist ein offizielles Attribut für das IMG-Tag. Damit kann ein Link zu einer anderen HTML-Seite angegeben werden auf der dieses Bild näher beschrieben werden kann, falls Euch der altText nicht ausreichen sollte.

Die Bildbeschreibung wird aber nicht von allen Browsern unterstützt. Im Firefox müsst Ihr mit der rechten Maustaste auf das Bild klicken und den Menüpunkt "Grafik-Info anzeigen" wählen. In dem erscheinenden Fenster gibt es eine "Alternative Beschreibung". Diese URL muss kopiert werden und kann dann im Browser eingefügt und geöffnet werden.

## linkWrap

Wrappt den IMG-Tag wie die übliche wrap-Eigenschaft aus dem stdWrap-Objekt. Allerdings kann bei der Eigenschaft im vorderen Teil des Wrappings ein Wert in Form von geschweifeten Klammern angegeben werden. Dieser Wert spiegelt die Ebene innerhalb der Rootline wieder. Somit kann damit zum Beispiel ein manueller Link zu den jeweiligen Elternseiten erstellt werden. Ich hab aber noch keine Seite gesehen, die mit dieser geschweiften Klammer aufgebaut wurde. Es wird überwiegend nur die enthaltene wrap-Funktionalität verwendet.

```typo3_typoscript
10 = IMAGE
10.file = fileadmin/logo.jpg
10.linkWrap = <a href="index.php?id={2}">|</a>
```

## imageLinkWrap

Mit dieser Eigenschaft könnt Ihr das Bild z.B. in einem PopUp-Fenster anzeigen lassen. Damit diese Eigenschaft greift, muss diese erst aktiviert werden:

```typo3_typoscript
10 = IMAGE
10.file = fileadmin/logo.jpg
10.imageLinkWrap = 1
10.imageLinkWrap.[viele weitere Eigenschaften]
```

## if

Schaut Euch dazu die if-Eigenschaft an.

## wrap

Mit dieser Eigenschaft könnt Ihr den IMG-Tag "einwickeln". Zu Info: linkWrap wird noch VOR dieser wrap-Eigenschaft ausgeführt.

## stdWrap

Schaut Euch dazu das stdWrap-Objekt an.
