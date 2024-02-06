+++
title = "Grafische Überschrift"
date = 2024-01-09T20:56:46+01:00
aliases = ["grafische-ueberschrift.html"]
+++

Wenn Ihr die Überschrift eines Inhaltelementes grafisch darstellen wollt, weil Ihr lieber Eure eigene Schriftart verwenden wollt, dann solltet Ihr Euch das Script hier mal anschauen. Das coole bei diesem Script ist, das die Überschrift auch bei mehrzeiligem Text das Bild dynamisch vergrößert. Um den Zeilenumbruch braucht Ihr Euch nicht zu kümmern. Alles was 400 Pixel überragt wird vom GIFBUILDER automatisch in die nächste Zeile umgebrochen.

```typo3_typoscript
lib.stdheader.10.1 >
lib.stdheader.10.1 = IMAGE
lib.stdheader.10.1 {
  file = GIFBUILDER
  file {
    XY = [20.w]+70 ,[20.h]+10
    backColor = #FFFFFF
    10 = IMAGE
    10 {
      file = fileadmin/templates/bilder/header-image.png
      file.width = 50
      file.height = 26
    }
    20 = TEXT
    20 {
      breakWidth = 400
      fontColor = #666666
      fontFile = fileadmin/templates/font/Anivers_Regular.ttf
      fontSize = 22
      offset = 60,21
      text.current = 1
    }
  }
}
```
