+++
title = "Bearbeitete Seiten"
date = 2024-01-09T20:57:50+01:00
aliases = ["bearbeitete-seiten-anzeigen.html"]
+++

Da ich keinen Blog mehr habe, könnt Ihr nicht mehr sehen, welche Seite ich zuletzt bearbeitet oder neu erstellt habe. Dieses Problem habe ich in Angriff genommen und mit ein bisschen TypoScript abgefragt, welche Content-Elemente sich verändert haben. An Hand dieser Content-Elemente habe ich dann die Seite ermittelt und lasse diese nun auf der Startseite auflisten.

Folgendes TypoScript habe ich in mein FCE eingefügt:

```typo3_typoscript
10 = CONTENT
10 {
  table = tt_content
  select {
    orderBy = MAX(tt_content.tstamp) DESC
    groupBy = tt_content.pid
    pidInList = 1
    recursive = 10
    max = 7
    where = nav_hide = 0
    selectFields = pages.uid, pages.title
    leftjoin = pages ON (tt_content.pid = pages.uid)
  }

  renderObj = TEXT
  renderObj {
    field = title
    required = 1
    typolink.parameter.field = uid
    typolink.ATagParams = class=internal-link
    wrap = <li>|</li>
  }
  wrap = <div class="csc-header"><h1>Meine zuletzt bearbeiteten Inhalte</h1></div><ul>|</ul>
}
```
