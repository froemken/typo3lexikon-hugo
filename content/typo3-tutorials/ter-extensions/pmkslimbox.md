+++
title = "pmkslimbox"
date = 2024-01-09T20:52:26+01:00
aliases = ["pmkslimbox.html"]
+++

## Bilder im Fokus

Ich habe schon einige Lightboxen (auch slimbox genannt) ausprobiert und nach ein paar Rücksprachen mit den Extensionentwicklern bin ich schließlich bei der `pmkslimbox` stehen geblieben.

### Features

Das JavaScript hinter dem statischen Template "slimbox (pmkslimbox)" ist nur 4 KB groß und wurde von demselben Programmierer erfunden, der auch das kompakte MooTools-Framework erfunden hat. Die Bedingungen hinter der slimbox waren: Es sollte sehr klein, effizient, standardkonform (XHTML-kompatibel), komplett konfigurierbar, bequem und 100 % kompatibel mit dem originalen Script der Lightbox 2 sein.

### Installation

Über den Extensionmanager installieren. Danach kommt für euch eine Entscheidungsfrage: Wollt ihr diesen Effekt für alle Bilder auf eurer Webseite haben, dann müsst ihr das statische Template "Slimbox (pmkslimbox)" in eurem Roottemplate einbinden, oder wollt ihr den Effekt nur in eurer z.B. Galerie haben. Dann erstellt für diese Seite ein +extTemplate und bindest das statische Template dort ein.

Wenn ihr nun ein Bild einbindet und die Checkbox "Klick-vergrößern" anwählt, dann sollte nach einen Clear Cache der Effekt schon auf eurer Webseite funktionieren.

### Wichtig

Das statische Template muss unter dem `css_styled_content` Template eingefügt werden.

### Konfiguration

Die statischen Templates bringen derzeit 24 Einstellmöglichkeiten mit und können über den Konstanteneditor konfiguriert werden. Im Gegensatz zu einigen anderen Slimboxen bietet die `pmkslimbox` auch eine Option an, wie der Effekt zur Slimbox ablaufen soll. Normalerweise werden diese Boxen immer erst in der vertikalen und danach in der horizontalen linear vergrößert. Bei dieser Extension könnt ihr aber aus über 20 Effekten auswählen. Ich hab mich hier mal für `elastic` und einer Ausführungszeit von 1 Sekunde (1000 Millisekunden) entschieden:

```typo3_typoscript
plugin.pmkslimbox.resizeTransition = Elastic.easeIn
plugin.pmkslimbox.resizeDuration = 1000
```

Auch sehr schön ist die Möglichkeit, dass Bilder ausgedruckt und/oder abgespeichert werden können:

```typo3_typoscript
plugin.pmkslimbox.enablePrintButton = 1
plugin.pmkslimbox.enableSaveButton = 1
```

### Die 4 statischen Templates

#### SlimBox (pmkslimbox)

Das ist das Standard Template, das wohl die meisten verwenden und auch verwenden sollten, denn nur in diesem Script sind all die Features enthalten wie ich sie unter der Überschrift "Features" auch aufgelistet habe. Drucken und Abspeichern von Bilder kann hier in diesem Script zusätzlich aktiviert werden.

#### SlimBoxPlus (pmkslimbox)

Die Basis für dieses Template ist das slimbox-Script hier drüber. Allerdings wurde es von einem anderen Entwickler um ein paar weitere Funktionen erweitert:

- IFrame-Support
- Auf TYPO3 gemünzte Druck- und Speicheroptionen
- Angabe von Höhe und Breite des Contents im ref-Attribut

#### MediaBox (pmkslimbox)

Auch diese SlimBox basiert auf dem Script des Standardtemplates und kann nicht nur Bilder anzeigen, sondern auch Videos, LiveCams und die verschiedensten Mediadateien.

#### SlimBoxMagnify (pmkslimbox)

Dieses Script ist nur knapp 1.5 KB groß und vergrößert einfach nur das Bild in einem kleinen PopUp-Fenster. Der Effekt ist der Gleiche wie bei dem Script, das von seiten TYPO3 gleich mitgeliefert wird. Einen Vorteil grade dieses Script hier zu verwenden habe ich noch nicht gefunden.
