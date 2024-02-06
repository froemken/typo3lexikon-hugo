+++
title = "Email als Bild"
date = 2024-01-09T20:57:24+01:00
aliases = ["email-als-bild.html"]
+++

Zu Zeiten von Bots und Co. werden E-Mail-Adressen und Telefonnummern automatisiert von Eurer Webseite ausgelesen und für Spamming und Telefonterror genutzt.

Das automatisierte Auslesen unserer Daten können wir nicht vollständig verhindern, wenn wir E-Mail und Rufnummer im Netz zwecks Erreichbarkeit für den Kunden angeben wollen. Aber wir können das Leben der Bots erschweren. TYPO3 bringt für die Maskierung schon ein eignes Tools mit, das im Setup des Haupttemplates aktiviert werden kann.

In diesem Beitrag möchte ich Euch eine Möglichkeit vorstellen, wie Ihr E-Mail und Rufnummer in Bilder konvertieren könnt. Meine erste Idee war mit Markern im RTE zu arbeiten, aber die Eigenschaft "constants" im Setup lässt nur reinen Text zu. Ich denke ich werde dazu mal einen Feature-Request an TYPO3 stellen. Denn dann könnte man per GIFBUILDER erzeugte Inhalte direkt im Inhaltselement integrieren.

## Lösung mit sfjquery

Folgendes Beispiel wurde auf Basis meiner Extension sfjquery realisiert.

Im statischen Template von sfjquery könnt Ihr mit einem Klick jegliche Einbindung von jQuery unterbinden und meine Extension als reine Template Maschine verwenden.

Zuerst tragt Ihr bitte folgende Zeile in den Setupbereich Eures Haupttemplates ein:

```typo3_typoscript
config.spamProtectEmailAddresses = -5
```

Ladet Euch die verdana.ttf oder eine andere beliebige Schriftart aus dem Font-Verzeichnis Eures Betriebssystems in das Fileadmin-Verzeichnis hoch. Danach installiert Ihr meine Extension sfjquery, tragt folgenden Code ein und Euer Ergebnis sieht so aus, wie in dem Bild ganz oben rechts. Vorteil an dieser Sache: Bots können die E-Mail-Adresse nicht sehen, können sie auch nicht auslesen (wie bei ab[AT]cd.ef) und der Kunde kann trotzdem drauf klicken und sein E-Mail-Programm öffnet sich mit der korrekten Adresse. Einziger Nachteil: Die Schrift auf dem Bild wird niemals so sauber aussehen, wie die Schrift im Inhaltselement.

TypoScript:

```typo3_typoscript
10 = TEMPLATE
10.template < temp.template
10.marks {
  TEL = IMAGE
  TEL.file = GIFBUILDER
  TEL.file {
    XY = [10.w]+10, 15
    backColor = #FFFFFF
    10 = TEXT
    10 {
      antiAlias = 1
      fontFile = fileadmin/templates/font/verdana.ttf
      fontSize = 12
      fontColor = #000000
      niceText = 0
      text = 02291-1234567
      offset = 1,15
    }
  }
  EMAIL < .TEL
  EMAIL.stdWrap.typolink.parameter = ab@cd.ef
  EMAIL.file.10.text = ab@cd.ef
}
```

HTML:

```html
<p class="bodytext">Max Mustermann<br>
Teststraße 13a</p>
<p class="bodytext">12345 Testort</p>
<p class="bodytext">Tel.: IMAGE<br>
E-Mail: IMAGE</p>
```

Ich verwende auf meiner Webeite eine Schriftfarbe von #333333. Aber durch die Einstellung antiAlias = 1 verschwimmt die Schrift auf dem Bild ein wenig und wird blasser. Damit sie wieder etwas dunkler wird, habe ich hier #000000 als Schriftfarbe gewählt.
