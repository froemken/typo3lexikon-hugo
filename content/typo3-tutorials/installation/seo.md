+++
title = "Suchmaschinenoptimierung"
linkTitle = "SEO"
date = 2024-01-09T20:48:23+01:00
aliases = ["suchmaschinenoptimierung.html"]
+++

## Wie bringe ich meine Page auf die erste Seite?

Ich war mit meiner TYPO3-Seite immer so auf Seite 5 der Google Suchergebnisse, wenn Ihr nach "TYPO3 Waldbröl" gesucht habt. Aber der Fehler lag nicht bei Google, sondern an mir selbst. Deshalb hier ein paar Tipps, wie auch Ihr Eure Webseite ein bissl weiter nach vorne bringen könnt.

## Erklärung PageRank

Der PageRank ist eine Berechnung von Google, um herauszufinden, ob eine Seite oft verlinkt wurde oder nicht. Das ist vielleicht etwas salopp ausgedrückt, aber im groben und Ganzen kommt das schon irgendwie hin.

Stellen wir und mal vor jede Webseite im Internet hat einen Wert von 1, den Sie den anderen Webseiten zuweisen kann. Dieser Wert hat noch nichts mit PageRank zu tun! Jetzt erstellen wir auf unserer Seite nur einen Link zu einer völlig anderen Webseite. Dann erhält diese Webseite einen PageRank von 1. ABER: Wenn unsere Seite 5 Links zu den unterschiedlichsten Webseiten hat, dann erhalten all diese Seiten nur einen PageRank von 0.2.

Webseiten wie spiegel.de werden sehr häufig verlinkt und haben deshalb einen sehr hohen PageRank (derzeit 8/10). Wenn von solch einer Webseiten nur ein Link auf Eure Webseite geht, dann erhaltet Ihr ebenfalls einen hohen PageRank. Nach dieser Googleinternen Berechnung müsste das dann 5 oder 6 sein.

Diesen Vorteil machen sich sehr viele SEO-Agenturen zu nutzen und preisen Ihr Angebot mit "Ihre Seite auf Platz 1" an. Das wird bestimmt die erste Zeit lang funktionieren...aber je mehr Seiten diese Agentur hat, desto weniger ist deren PageRank wert und Eure Webseite rückt mit jeder weiteren Webseite in den Suchergebnissen nach hinten ab.

## Sauberer Quellcode

Google überprüft die Quellcodequalität Eurer Webseite. Wenn diese nicht sauber programmiert ist, landet Ihr mit Eurer Webseite weiter hinten in den Suchergebnissen. Diese Quellcodequalität ist so wichtig, dass sogar Webseiten mit einem höheren PageRank, aber mit einem schlechten Quellcode, weiter hinten aufgelistet werden, als Webseiten mit einem niedrigeren PageRank und einem erstklassigem Quellcode.

TYPO3 kann Euch dabei unterstützen und Euren Quellcode so gut es geht "konvertieren". Dazu habe ich folgende Einstellungen in meinem Template-Setup aktiviert:

```typo3_typoscript
config.xhtml_cleaning = all
config.doctype = xhtml_trans
config.xhtmlDoctype = xhtml_trans
config.doctypeSwitch = 0
config.renderCharset = utf-8
config.metaCharset = utf-8

[browser = msie]
config.doctypeSwitch = 1
[global]
```

## Der Webseiten Titel

Der title-Tag ist das wohl mächtigste Element auf Eurer Webseite, das Euch richtig gut nach vorne bringen kann. Nachteil bei TYPO3: Per default schaut der Titel immer in diesem Konstrukt aus:

`[Seitentitel der im Template angegeben wurde] : [Titel der aktuellen Seite]`

Da Euer Haupttemplate sich durch all Eure Seiten und Unterseiten vererbt, fängt Euer title-Tag immer mit dem Seitentitel an, wie er im Template definiert wurde. Google aber wertet das erste Wort und evtl noch das Zweite und Dritte im title-Tag höher an als die Worte ab Position 4 und aufwärts. Wenn Euer Seitentitel aus dem Template schon aus 2-3 Worten besteht, dann könnt ihr mit den Seitentiteln der einzelnen Seiten nicht mehr viel reißen. Also benötigen wir eine TYPO3-Konfiguration, die die title-Tags etwas anders aufbauen:

```typo3_typoscript
# Keinen automatischen Seitentitel erzeugen
config.noPageTitle = 2
# Eigener Seitentitel
page.headerData.10 = TEXT
page.headerData.10.field = subtitle // title
page.headerData.10.wrap = <title>|</title>
```

Mit diesen Einstellungen wird der Standardtitel deaktiviert und wir müssen uns um den Aufbau eines Titels selber kümmern. Ich habe mich hier dafür entschieden, dass erst der Untertitel verwendet wird und wenn dieser nicht angegeben wurde, dann wird der reguläre Titel der Seite verwendet.

## Der Titel und Individualität

Ich hab mich immer bemüht, dass mein Name und meine Arbeit im Internet gefunden wird. So wurde ich natürlich sehr weit oben aufgeführt, wenn die Suchenden nach "Stefan Frömken" und "TYPO3 in Waldbröl" gesucht haben. Aber was hat es mir gebracht? Nichts! Kein Mensch sucht nach meinem Namen und mit den paar Firmen in Waldbröl werden vielleicht 1-2 Suchanfragen im Jahr mit TYPO3 und Waldbröl gemacht worden sein.

Der Markt im Internet ist derzeit überflutet. Alle wollen sich an der ersten Position sehen. Aber solange Ihr Euch nicht vom "Standard" abhebt, wird das nie etwas. Das ist der Grund, warum ich eine Seite erstellt habe, auf der ich Tutorials zu den verschiedensten Problemen zu TYPO3 erstellt habe.

## Das Ergebnis:

Meine Seite für mein Gewerbe wird ca. 150-200mal im Monat angeklickt

Meine neue TYPO3-Seite dagegen wird über 2000mal im Monat besucht und es werden stetig mehr.

Woran mag das liegen? Ganz einfach: Die Hauptwörter/Keywords meine gewerblichen Seite sind derzeit noch auf viel zu übergeordneten Worten getrimmt. Sucht man nach TYPO3, dann wird meine Seite auf Grund der Masse anderer Webseiten, die auch auf TYPO3 getrimmt sind, sehr weit hinten angezeigt. Meine TYPO3-Seite hingegen stellt mit seinen Fehlermeldung, seinen Fachbegriffen und den Tutorials eine Individualität dar. Sucht jemand nach einer Fehlermeldung, erscheint meine Seite teilweise sogar an der ersten Stelle.

## Keywords

Die Diskussion über Keywords ist derzeit geteilter Meinung. Während die einen auf Keywords schwören, sagen die anderen, dass Google sich überhaupt nicht mehr für Keywords interessiert. Ich persönlich bin auch für Letzteres, aber binde diese trotzdem ein, weil meine Webseite ja nicht nur vom Googlebot besucht wird, sondern auch von Yahoo und all den anderen Searchbots.

Im Folgenden die Einstellungen für meine Seite:

```typo3_typoscript
page.meta.KEYWORDS.cObject = TEXT
page.meta.KEYWORDS.cObject {
  field = keywords
  noTrimWrap = |typo3 stefan frömken waldbröl oberberg oberbergischer kreis tutorial ||
}
```

Mit diesen Einstellungen gebe ich für alle Seiten 8 Keywords fest vor und kann mit dem Keywordsfeld in den jeweiligen Seiteneigenschaften weitere seitenindividuelle Keywords angeben. Bitte achtet darauf im Bereich von 10 Keywords zu bleiben.

Ich persönlich habe mich hier für die Anordnung der Keywords OHNE Komma entschieden. So muss ich nicht meinen kompletten Namen, meinen Vornamen UND meinen Nachnamen angeben, sondern einfach beides hintereinander angeben und feddich.

## Die Beschreibung

Die Description ist schon wieder wichtiger als die Keywords. Auch hier gilt eine individuelle Beschreibung pro Seite. Gebs Ihr keine Beschreibung an, dann sucht sich Google einfach einen Text von Eurer Webseite aus. So kann es dann schon mal passieren, dass bei den Suchergebnissen, dann "Hallo zusammen und herzlich willkommen auf meiner Webseite" steht. Wer wird wohl jetzt noch Lust haben, diese Site zu besuchen?

Deshalb hier eine Idee, das zu ändern:

```typo3_typoscript
page.meta.DESCRIPTION = Stefan Frömken stellt auf seiner TYPO3-Seite Tutorials zu verschiedenen Themen von TYPO3 vor. Einfache Texte und Themen bezogene Bilder runden seine Tipps und Tricks ab
page.meta.DESCRIPTION.override.field = description
page.meta.DESCRIPTION.override.trim = 1
```

Ich gebe hier für alle Seiten erstmal eine fest vorgegebene Beschreibung vor OHNE "Hallo und Co." Wenn in den einzelnen Seiteneigenschaften dann eine Beschreibung hinterlegt wird, wird meine Defaultbeschreibung hier mit der aus der Seite überschrieben. Trim löscht dann noch überflüssige Leerzeichen am Anfang und Ende der Beschreibung.

## Überschriften

Jede einzelne Seite sollte eine Überschrift enthalten. Bitte macht hier nicht den Fehler und erstellt alle paar Absätze eine Überschrift mit H1. Sowas merkt Google und stuft Euch direkt wieder weiter hinten ein. Meine Lösung ist: Jede Seite sollte eine Überschrift mit H1 haben. Alle anderen Überschriften bitte mit H2 und H3 realisieren.

Tipp: Grafische Überschriften kann Google nicht lesen. Müssen es aber unbedingt grafische Überschriften sein, dann stellt das Bild bitte in die H1-Tags:

```html
<h1><img src="#" alt="Überschrift" title="Überschrift"></h1>
```

## Heuristik

Die Suchmaschinen können mittlerweile "lesen" was auf der Webseite steht. So können die Bots erkennen, ob Ihr die Keywords einfach auf einer Webseite auflistet oder ob Ihr sie sauber in den Text eingebaut habt. Also: Macht Euch die Mühe und erstellt gute Inhalte.

## Technisches

Es gibt im Internet viele Möglichkeiten seine Homepage durchleuchten zu lassen. Folgende kostenlosen Anbieter solltet Ihr Euch zumindest mal anschauen:

Seitwert
Seittest

Mittlerweile arbeite ich aber nur noch mit den Google Webmastertools. Dort gibt es Tools zum Überprüfen der Webseite, ob der Quellcode einigermaßen in Ordnung ist und ob sich der Seitentitel nicht auf jeder Deiner Seite wiederholt.

Weiterhin könnt Ihr dort auch sehen, wo an welcher Stelle in den Suchergebnissen Eure Webseite angezeigt wurde, wenn die aufgelisteten Suchbegriffe eingegeben wurden. So könnt Ihr immer sehen wohin der Trend geht und Ihr könnt Eure Webseite daraufhin anpassen. So wird meine Seite zum Beispiel sehr oft bei der Suche nach "indexed_search konfigurieren" angeklickt. Da mein eigentlicher Inhalt aber sehr rudimentär ist sollte ich diese Seite bald überarbeiten.

## robots.txt

Google empfiehlt in seinen Webmastertools eine robots.txt im Rootverzeichnis der Webseite anzulegen. Damit kann den verschieden Crawlern mitgeteilt werden auf welche Verzeichnisse sie keinen Zugriff haben. Laut SelfHTML gibt es nur 2 gültige Befehle für diese Datei:

```shell
User-agent
Disallow
```

Vorteile für das Ranking bringt diese Datei zwar nicht, aber Ihr könnt mit dieser Datei z.B. den Zugriff auf das Bilderverzeichnis verbieten. Vielleicht wollt Ihr auch Euer Forum nicht in den Suchergebnissen sehen.

Eine robots.txt, die alle Seiten für alle Crawler erlaubt schaut so aus:

```shell
User-agent: *
Disallow:
```

Wenn alles verboten sein soll, dann ändert die Datei entsprechend ab:

```shell
User-agent: *
Disallow: /
```

## Sitemap

In den Webmastertools von Google könnt Ihr auch eine XML-basierte Sitemap hinterlegen. Schaut Euch dazu mal die Extension mc_googlesitemap an. Diese erstellt solch eine XM_Sitemap und kann dann in den Google Webmastertools hinterlegt werden.
