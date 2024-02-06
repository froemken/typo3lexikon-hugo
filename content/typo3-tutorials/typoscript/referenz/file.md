+++
title = "FILE"
date = 2024-01-09T20:58:10+01:00
aliases = ["file.html"]
+++

Dieses Contentobjekt liest den Inhalt einer Datei ein. Dabei wird Folgendes berücksichtigt: Wenn die angegebene Datei ein Bild ist, dann wird diese in Form eines IMG-Tags ausgegeben. Bei allen anderen Dateien wird deren Inhalt ausgegeben.

## Eigenschaften

### file (resource)

Pfad zu der Datei. Beispiel: `fileadmin/templates/template.html`

### linkWrap (linkWrap)

Im Gegensatz zur normalen wrap-Eigenschaft bietet linkWrap noch eine zusätzliche Funktion an. So wird in dem Textelement vor dem Pipe-Zeichen (|) nach Zahlen gesucht, die in geschweiften Klammern stehen. Diese Zahlen spiegeln die Navigationsebene wider und werden mit der jeweiligen Seiten-UID in der Rootline ersetzt.

```typo3_typoscript
page.10 = FILE
page.10.file = fileadmin/templates/template.html
page.10.linkWrap = <a href="{0}"><b>|</b></a>
```

Beispiel: Wenn wir uns in der Ebene 1 befinden, dann wird mit diesem Script die Seiten UID der Elternseite zurückgegeben. In meinem Fall die UID 6. Das obige Script gibt etwas in dieser Form wieder:

```html
<a href="6"><b>[Inhalt der Datei]</b></a>
```
