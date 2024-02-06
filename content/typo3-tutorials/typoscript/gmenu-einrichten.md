+++
title = "GMENU einrichten"
date = 2024-01-09T20:56:59+01:00
aliases = ["gmenu-einrichten.html"]
+++

In einem meiner letzte Projekte musste ich nach langer Zeit mal wieder ein GMENU einrichten mit einem Untermenü. Damit nicht auch Ihr solange daran hängen müsst, habe ich Euch mal mein Script hier zur Verfügung gestellt.

```typo3_typoscript
temp.leftNavi = HMENU
temp.leftNavi {
  special = directory
  special.value = 6
  wrap = <ul>|</ul>

  1 = GMENU
  1 {
    NO = 1
    NO {
      wrap = <li>|</li>
      XY = 227, 60
      10 = IMAGE
      10.file = fileadmin/templates/bilder/navi-bg-normal.png
      10.file.width = 227
      10.file.height = 60
      20 = TEXT
      20 {
        text.field = title
        offset = 32,51
        fontColor = #666666
        fontFile = fileadmin/templates/font/Anivers_Regular.ttf
        fontSize = 20
        spacing = 2
      }
    }
    RO < .NO
    RO.wrap = <li>|
    RO.15 = IMAGE
    RO.15 {
      file = fileadmin/templates/bilder/navi-bg-hover.png
      file.width = 10
      file.height = 60
    }
  }

  1.ACT < .1.RO
  1.ACT.wrap = <li>|

  2 < .1
  2.wrap = <ul>|</ul>
  2.NO = 1
  2.NO.wrap = <li>|</li>
  2.NO.XY = 227,20
  2.NO.20.fontSize = 16
  2.NO.20.offset = 32,16

  2.RO < .2.NO
  2.RO.15 >
  2.RO.15 = IMAGE
  2.RO.15 {
    file = fileadmin/templates/bilder/navi-sub-bg-hover.png
    file.width = 14
    file.height = 20
    offset = 32,0
  }

  2.RO.20.offset = 46,16

  2.ACT < .2.RO
}
```
