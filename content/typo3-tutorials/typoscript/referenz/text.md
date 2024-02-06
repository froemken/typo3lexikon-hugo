+++
title = "TEXT"
date = 2024-01-09T20:58:10+01:00
aliases = ["text.html"]
+++

## Eigenschaften

### value

Mit `value` könnt Ihr Eurem Contentobjekt einen Text zuweisen. Diesen Text könnt ihr dann mit den folgenden `stdWrap`-Eigenschaften nachbearbeiten.

### stdWrap

Nicht die `value` Eigenschaft, sondern das TEXT-Objekt selbst beinhaltet die `stdWrap` Eigenschaften.

## Einfaches Beispiel

```typo3_typoscript
10 = TEXT
10.value = Hallo zusammen
10.wrap = <p>|</p>
```

Ergebnis:

```html
<p>Hallo zusammen</p>
```
