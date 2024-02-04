+++
title = "Ohne Indexierung läuft nix"
linkTitle = "indexed_search"
date = 2024-01-09T20:29:50+01:00
alwaysopen = false
aliases = ["indexed-search.html"]
+++

Die `indexed_search` wird über den Extension-Manager installiert und evtl. wird auf diesem Weg die `doc_indexed_search` gleich mit installiert. Die Konstanten lassen wir erst mal so wie sie sind.

In vielen Dokumentationen heißt es, dass die `indexed_search` folgendermaßen aktiviert werden muss:

```typo3_typoscript
page.config.index_enable = 1
```

Dieses Beispiel ist in meinen Augen nicht ganz korrekt, da `page.config` eher für den aktuellen Frame verwendet wird. In den heutigen Webseiten sind Frames aber nicht gern gesehen. Von daher finde ich, wenn keine Frames verwendet werden, folgendes TypoScript besser:

```typo3_typoscript
config.index_enable = 1
```

Hier im Bild seht ihr wie sich der Cache eurer Seiten mit jedem Besuch der einzelnen Seite aufbaut. Hier könnt ihr auch überprüfen, ob die `indexed_search` sauber arbeitet. In meinem Fall habe ich erst 2 Seiten meiner Webseite besucht.

## Plugin einfügen

Erstellt dazu eine neue Seite und fügt dort das Content-Element "Plug-In einfügen" ein. Bitte nicht das CE "Suchformular" auswählen, das ist was anderes. Im Bild seht ihr wie es aussehen sollte.

Bitte geht nun in die Seiteneigenschaften eurer Suchseite und setzt dort die Checkbox: "no-cache" bzw. "nicht cachen". Ich persönlich finde es immer sehr nervig, wenn man in einem Suchergebnis Suchergebnisse einer zuvor ausgeführten Suche findet.

Die Extension `indexed_search` arbeitet immer nur mit gecacheten Webseiten. Soll heißen: Solange ihr eure Seite nicht mit dem Browser besucht habt, solange wird diese Seite auch nicht in den Suchergebnissen gelistet. Ihr müsst also, damit alles funktioniert, alle eure Seiten mindestens einmal besucht haben, damit `indexed_search` auch alle Seiten durchsuchen kann.

Es gibt kleine Tools, die euch das Besuchen eurer Webseiten abnehmen können, aber dazu später mehr.

## Sprache einstellen

Bei mir wurden alle Texte und Bedienelemente der `indexed_search` in englischer Sprache angezeigt. Sollte das auch bei euch der Fall sein, dann tragt noch folgende Zeile in euer Seitentemplate ein:

```typo3_typoscript
config.language = de
```

Da wir die `indexed_search` frisch installiert haben, müsst ihr die fehlende deutsche Sprache für die `indexed_search` nachinstallieren. Dazu geht's in den Extension-Manager dann Übersetzungen und dann auf den Button "Aus Bibliothek aktualisieren" klicken.

Nach einem "clear Cache" sollte die `indexed_search` nun in deutscher Sprache im Frontend erscheinen.

## Aussehen verändern

Wir können im TS (Setup) ein klein wenig das Aussehen unseres Plugins steuern. Hierzu genügt normalerweise ein Blick in die Dokumentation, die wir extra mitinstallieren mussten und finden dort nur 3 Einstellungen, um bestimmte Elemente ein- bzw. ausszublenden. Ich finde das schon ein bisschen krass, denn wenn ihr mit dem Objektbrowser die `indexed_search` aufruft, dann findet ihr 11 Einstellungen vor.

Pfad: `Template -> Startseite -> TYPO-Script Objekt-Browser -> DropDown = Setup/Konfiguration -> plugin -> tx_indexed_Search -> show`

Hier könnt ihr z.B. auf "rules" klicken und im folgenden Fenster die 1 in eine 0 ändern. Auf diese Weise werden euch die "Regeln" wie und auf welche Weise ihr die Suchbegriffe einzugeben habt, ausgeblendet.

Wenn ihr jetzt noch die Einstellung `advancedSearchLink` auf 0 setzt, dann schaut eure `indexed_search` schon eine ganze Ecke aufgeräumter aus.

## indexed_search konfigurieren

Ich möchte euch hier ein paar ganz einfache Konfigurationen zeigen:

```typo3_typoscript
plugin.tx_indexedsearch.search.rootPidList = 1
```

Normalerweise werden immer alle gecachten Webseiten durchsucht. Wenn ihr aber dieser Eigenschaft eine Seiten-ID gebt, dann wird nur diese und alle Unterseiten durchsucht.

```typo3_typoscript
plugin.tx_indexedsearch._DEFAULT_PI_VARS.results = 5
```

Diese Einstellung steht standardmäßig auf 10. Es werden also 10 Suchergebnisse pro Seite angezeigt. Ändert diesen Eintrag auf 5, wenn ihr weniger Suchergebnisse pro Seite anzeigen lassen wollt. Dieser Eintrag ist nicht dokumentiert. Die Eigenschaft "results" in "blind" funktioniert nicht oder besitzt eine andere Funktion.
