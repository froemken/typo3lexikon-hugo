+++
title = "RTE Htmlspecialchars"
date = 2024-01-09T20:34:17+01:00
aliases = ["rte-htmlspecialchars.html"]
+++

Dieses Thema ist eine Katastrophe, denn man muss ganz genau wissen, wann wo etwas passiert. Ihr müsst genau wissen, was die Transformation macht und Ihr müsst wissen, was passiert, wenn Ihr einen dieser HTMLparser verwendet.

Fangen wir ganz einfach an und konfigurieren unseren RTE folgendermaßen:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  showButtons = bold, italic, chMode
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext
    denyTags = u,a,img,br,div,center,pre,font,hr,sub,sup,strong,em,li,ul,ol,blockquote,strike,span
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i
    }
  }
}
```

Die Konfiguration reicht für einfache Texte, Listen und Formatierungen. Damit ich Euch auch meine ganzen Codes auf der Webseite sauber präsentieren konnte, musste ich nun auch HTML-Code in der Datenbank abspeichern können. Mit der Konfiguration von oben haben wir aber ein Problem: Tragt mal folgenden Text in Euren RTE ein:

```html
Hallo zusammen

Hallo >

Müller & Breuer

<h1>Überschrift</h1>
```

Nach dem Abspeichern werdet Ihr merken, dass die ersten 3 Zeilen zwar richtig sind, aber sich unsere letzte Zeile in eine große Überschrift verwandelt hat. Ich hab mich mal auf die Suche gemacht und mir für Euch die Datei alt_doc.php näher angeschaut. Diese Datei wird DIREKT nach dem Speichern ausgeführt. Hier habe ich mit Hilfe von print_r mal das data-Array durchleuchtet und siehe da: Der Inhalt vom RTE ist schon durch htmlspecialchars() gelaufen.

Schauen wir uns nun den Inhalt in der Datenbank an. Ich hab mir phpmyadmin geschnappt und festgestellt, dass die Tags nun zwar wieder richtig angezeigt werden aber im Quelltext von phpmyadmin waren sie immer noch htmlspecialchared. Ich will mich nicht irritieren lassen, also habe ich mich direkt auf der Konsole auf den MySQL-Server verbunden und dort sehe ich, dass die htmlspecialchared Inhalte des RTE zurück in echte Tags verwandelt wurden. So steht es auch in der Doku zu css_transform.

Unsere Überschrift steht nun als <h1>Überschrift</h1> in der Datenbank drin und wird auf dem Weg zurück in der RTE auch genau so gehandhabt. Wir müssen also einen Weg finden, dass Überschriften zwar erlaubt sind, aber wenn wir sie als Tag in unseren RTE schreiben htmlspecialchared werden.

Wir wollen uns nun mal anschauen in wie weit wir Einfluss auf dieses Verhalten nehmen können.

```typo3_typoscript
dontUndoHSC_db = 1
```

Mit dieser Eigenschaft des proc-Objektes können wir das Zurückkonvertieren der htmlspecialchared Inhalte des RTE unterbinden. Heißt: Wir haben htmlspecialchared Inhalte auch in unserer Datenbank. Wenn Ihr diese Eigenschaft setzt und Ihr Euren Inhalt abspeichert, dann habt Ihr von jetzt auf gleich den RTE voll mit htmlspecialchared Inhalten. Es sei denn:

```typo3_typoscript
dontHSC_rte = 1
```

Diese Eigenschaft ist das exakte Gegenteil von dontUndoHSC_db. Wenn Ihr eine dieser beiden Eigenschaften verwendet, dann solltet Ihr auch die andere Eigenschaft gesetzt haben. Ansonsten bekommt Ihr ein völliges Durcheinander. Also: Immer beide Eigenschaften setzen oder nicht setzen...aber auf keinen Fall mischen.
