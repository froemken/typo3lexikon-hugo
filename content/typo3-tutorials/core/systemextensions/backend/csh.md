+++
title = "Context Sensitive Help"
linkTitle = "Csh"
date = 2024-01-09T20:16:01+01:00
aliases = ["csh.html"]
+++

Context Help. Ich klicke irgendwo hin und es öffnet sich ein Kontextmenü. Ähnlich wie in Windows. Rechtsklick auf den Desktop, Kontextmenü öffnet sich, Eintrag "Anzeigeeinstellungen anpassen" auswählen.

So zumindest die Idee `context_help`. Ihr merkt schon: Es wird kompliziert. Denn nach der Installation dieser Extension, passiert: Nichts bzw. es kommt darauf an, aber fangen wir erstmal vorne an.

Mithilfe dieser Extension erhalten die Überschriften der jeweiligen Felder im Listenmodul einen kleinen Tooltip. Beispiel: Ihr erstellt ein neues Inhaltselement. Wenn ihr nun mit der Maus über die Überschrift Header fahrt, dann verändert sich euer Mauszeiger in ein Fragezeichen. Klickt ihr nun auf diese Überschrift, erscheint ein Tooltip mit dem Text: "Enter header text for the Content Element." Je nachdem bei welcher Feldüberschrift ihr das macht, erscheint evtl. noch ein zusätzliches Icon, mit dem sich ein PopUp-Fenster öffnet. In diesem findet ihr noch weitere Informationen, Querverweise und evtl. sogar ein Bild, das dieses Feld näher erklärt.

Inhalt der Extension ist:

- Die Übersetzungen/Erklärungen für sämtliche TYPO3 eigenen Tabellen
- Ein AJAX Script, das einen alternativen Titel, eine Kurzbeschreibung und die Information zurückliefert, ob für dieses Feld weitere Informationen verfügbar sind, die dann mittels dem oben genannten Icon und dem PopUp-Fenster angezeigt werden.

Merkt ihr was? Die Generierung dieser Tooltips und das Generieren des Inhalts für das PopUp-Fenster sind gar nicht Bestandteil dieser Extension. Darum kümmert sich der Core. Genauer genommen die `EXT:backend`. Und weil diese beiden Extensions so eng miteinander arbeiten wurde das AJAX Script seit TYPO3 9 in `EXT:backend` migriert und die Sprachdateien je nach Tabelle in die jeweilige Systemextension verschoben. Übersetzungen für `fe_users` ist somit in die `EXT:frontend` verschoben worden.

Verantwortlich für das Rendern der Tooltips ist die Methode `BackendUtility::wrapInHelp()`, aber was sieht man dort noch? Eine Abhängigkeit zu der Systemextension `EXT:cshmanua`. Grob gesagt: Wenn diese Extension nicht installiert ist, dann passiert trotz installierter `EXT:context_help` mal gar nichts. Aber warum?

Die `EXT:cshmanual` kommt mit einem Backend Modul daher, mit dem ihr euch pro Tabelle und pro Feld bis zu 5 zusätzliche Informationen anzeigen lassen könnt: Beschreibung, Details, Siehe auch, Bild und Bildbeschreibung. Mittels bestimmter URL-Parameter kann diese Ansicht bis auf Feldebene heruntergebrochen werden. Dies macht sich das JavaScript `ContextHelp.js` mit der Funktion `ContextHelp.showHelpPopup` zunutze.

Also. Nochmal in Kurzform: `EXT:backend` rendert, sofern `EXT:context_help` und `EXT:cshmanual` aktiviert sind, ein paar zusätzliche HTML-Zeilen mit einer bestimmten CSS-Klasse, die dann von dem JavaScript `ContextHelp.js` ausgelesen werden. Mittels der `data` Attribute in dem HTML-Tag kann nun das AJAX-Script der `EXT:context_help` aufgerufen werden, dass die 3 Informationen: Titel, Beschreibung und weitere Informationen aus Sprachdateien unterschiedlichster Extensions zurückliefert. Wenn es mehr Informationen hat, wird mit `ContextHelp.js` ein Link zu dem Modul aus `EXT:cshmanual` generiert, die nun die Detailansicht eines ganz bestimmten Feldes zurückliefert. Das JavaScript `Popover.js` empfängt diese Daten und lässt diese dann in einem PopUp-Fenster anzeigen.

Es gibt eine Prüfung, ob der aktuell angemeldete Benutzer überhaupt das Recht hat, das Modul aus `EXT:cshmanual` aufzurufen. Wenn dies nicht der Fall ist, kann kein PopUp-Fenster mit Zusatzinformationen angezeigt werden. Es verbleibt nur der Tooltip mit einer Kurzbeschreibung.

Easy, oder? Ich finde es gut, dass die `EXT:context_help` seit TYPO3 9.0.0 in die `EXT:backend` migriert wurde. Das macht die ganze Sache etwas klarer.
