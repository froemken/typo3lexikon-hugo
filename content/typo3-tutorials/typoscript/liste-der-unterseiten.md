+++
title = "Liste Unterseiten"
date = 2024-01-09T20:57:35+01:00
aliases = ["liste-der-subpages-anzeigen.html"]
+++

Damit sich meine Navigation nicht so aufbläht mit bis zu 5 Unterseiten, musste ich die Navigationsstruktur mit in meinen Contentbereich integrieren. Allerdings musste ich mich dann ständig darum kümmern, dass die Liste mit Unterseiten aktuell bleibt. Das ging mir auf den Keks und so habe ich mir ein FCE gebastelt, dass mit alle Seiten der aktuellen Seite nach Namen sortiert anzeigt. Eine Aufgabe weniger :-)

Hier der Code:

```typo3_typoscript
10 = CONTENT
10 {
  table = pages
  select {
    orderBy = title
    pidInList = this
    recursive = 0
    selectFields = uid, title
  }

  renderObj = TEXT
  renderObj {
    field = title
    required = 1
    typolink.parameter.field = uid
    wrap = <li>|</li>
  }
  wrap = <ul>|</ul>
}
```
