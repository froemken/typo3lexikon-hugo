+++
title = "AdminPanel"
date = 2024-01-09T20:47:30+01:00
aliases = ["adminpanel.html", "/typo3-tutorials/typo3-einrichten/admpanel.html"]
+++

Mit dem admPanel habt Ihr die Möglichkeit die Inhalte Eurer Webseite direkt im Frontend zu bearbeiten.

Damit das funktioniert müssen folgende Bedingungen erfüllt sein:

Ihr müsst im gleichen Browser 2 Tabs offen haben. In dem einen müsst Ihr im TYPO3-Backend angemeldet sein und in dem anderen Tab das Frontend aufrufen

Backend und Frontend müssen den gleichen Domainnamen haben, auch wenn für diese eine Webseite mehrere Domainnamen gültig sind. Auch die Subdomains müssen gleich sein. Achtet hier insbesondere auf die Verwendung von mit oder ohne "www".

Backend und Frontend müssen das gleiche Protokoll aufweisen. Also beide http oder beide https
Das Admin Panel muss mit Hilfe von TypoScript aktiviert werden: config.admPanel = 1
Installiert die Systemextension "feedit", damit das Admin Panel mit den Bearbeitungsfunktionen erweitert wird
Sofern Ihr als Admin im Backend angemeldet seit, erhaltet Ihr nun alle Funktionen des Adminpanel im Frontend.

## feedit

feedit ist eine Systemextension von TYPO3 und wird mit jeden TYPO3-Paket direkt mitgeliefert. Ihr braucht sie nur noch im Extension-Manager zu aktivieren.

Ohne feedit funktioniert das admPanel zwar, aber Ihr könnt den Inhalt nicht im Frontend bearbeiten. Alle anderen Funktionen wie "Seiteneigenschaften bearbeiten", "Vorschau", "Cache" und "Listenansicht" lassen sich auch ohne feedit bearbeiten.

Installiert Euch die Systemextension feedit, klappt die Karte "Eingabe" auf, setzt die Checkboxen wie hier im Bild und Euer Seiteninhalt lässt sich nun auch im Frontend mit Hilfe der neu hinzugekommenen Symbole bearbeiten, verschieben und löschen.

## admPanel und nicht Adminuser

Nach dem Eintrag von config.admPanel = 1 im TS Setup ist das admPanel für nicht Adminuser zwar aktiv aber im Fronend noch nicht sichtbar. Das hat was mit Sicherheit zu tun und muss für jedes Modul einzeln oder alle Module auf einmal für den jeweiligen Benutzer oder Benutzergruppe sichbar gemacht werden.

Folgende Module werden in der TYPO3-Dokumentation genannt:

- preview (verborgene Inhalte/Vorschau zu Zeitpunkt x)
- cache
- publish
- edit (SysExt: feedit sollte geladen sein)
- tsdebug
- info

Hinweis

Alle nun folgenden Einstellungen werden entweder in der TSconfig des Backendbenutzers oder in der Backendbenutzergruppe eingetragen. Nicht aber im TS Setup. Dort gibt es zwar auch das Objekt admPanel und laut Doku soll es auch alle ADMPANEL-Eigenschaften besitzen, aber nach sämtlichen Tests und Rumsucherei habe ich herausgefunden, dass dem nicht so ist. Eigenschaften, die hier gesetzt werden haben keine Auswirkung.

## Vorschau und Bearbeiten erlauben

Tragt dazu Folgendes in den TSconfig-Bereich des Users oder der Gruppe ein.

```typo3_typoscript
admPanel.enable.preview = 1
admPanel.enable.edit = 1
```

Wenn Ihr diese beiden Zeilen in den TSconfig-Bereich einer Backendusergruppe eingetragen habt und wollt nun ein paar Backendusern aber verbieten das admPanel zu verwenden, dann könnt Ihr dies mit folgendem Eintrag in die TSconfig der jeweiligen Backenduser erreichen:

```typo3_typoscript
admPanel.hide = 1
admPanel extrem
```

Normalerweise reichen die hier genannten Infomationen völlig aus. Nur selten oder in wirklich großen Webseiten werden wohl die folgenden Infomationen benötigt. Deshalb auch der Titel "extrem" :-)

Alle Einstellungen wie z.B. "ist die Checkbox zum Anzeigen der Edit-Symbole angeklickt oder nicht" werden in der Spalte "uc" des jeweiligen Backendusers (be_users) abgespeichert. Heißt: Wenn Ihr eine Checkbox angeklickt habt, Euch abmeldet und irgendwann wieder anmeldet, dann ist die gewählte Checkbox immer noch aktiviert.

Jetzt kommt die Eigenschaft "override" hinzu. Diese Option überschreibt nun Eure gemachten Einstellungen im admPanel respektive die Daten aus dem Feld "uc". Heißt: Wenn Ihr in der TSconfig mit override arbeitet, dann ist die gewählte Eigenschaft immer aktiv (nur aktivierbar...nicht aber deaktivierbar)...egal ob im admPanel ein Haken drin ist oder nicht. Dazu habe ich folgenden Codeschnipsel in der class.tslib_adminpanel.php gefunden:

```php
if ($GLOBALS['BE_USER']->extAdminConfig['override.'][$pre . '.'][$val] && $val) {
    return $GLOBALS['BE_USER']->extAdminConfig['override.'][$pre . '.'][$val];
}
if ($GLOBALS['BE_USER']->extAdminConfig['override.'][$pre]) {
    return $GLOBALS['BE_USER']->extAdminConfig['override.'][$pre];
}
```

Ihr seht hier, dass die if-Anweisungen nur dann ausgeführt werden, denn der gesuchte Wert ein true bzw. 1 wieder gibt. Hier liegt der Grund, warum Ihr mit override bestimmte Optionen nicht grundsätzlich abschalten könnt.

Schauen wir uns mal folgende TSconfig an:

```typo3_typoscript
admPanel.override.edit.displayFieldIcons = 1
admPanel.override.edit.displayIcons = 1
```

Im Bild oben rechts könnt Ihr nun sehen, dass die Symbole zum Bearbeiten der Contentelemente trotz der deaktivierten Checkboxen im admPanel verfügbar sind.

Jetzt könnte man denken: "Ja hey...jetzt wo die Symbole zum Bearbeiten doch grundsätzlich da sind, wozu brauch ich dann noch das admPanel?" Richtig gedacht und deshalb ändern wir das TSconfig mal um in:

```typo3_typoscript
admPanel.override.edit.displayFieldIcons = 1
admPanel.override.edit.displayIcons = 1
admPanel.hide = 1
```

Das admPanel ist verschwunden, die Funktionen zum Bearbeiten des Inhalts sind aber weiterhin verfügbar.

Welche Funktionen pro Kategorie (edit, preview, ...) verfügbar sind schaut Ihr Euch am besten direkt im Quellcode an. Ich empfehle hier das Firebug-AddOn für den Firefox. Einfach auf die gewünschte Option klicken und Ihr erhaltet zum Beispiel folgenden HTML-Code:

```html
<input type="checkbox" checked="checked" value="1" name="TSFE_ADMIN_PANEL[preview_showHiddenPages]">
```

Der erste Teil des Namens "preview_showHiddenPages" gibt Euch die Kategorie (hier: preview) und der zweite Teil die Option wieder (hier: showHiddenPages). Demnach muss im TSconfig folgendes eingetragen werden:

```typo3_typoscript
admPanel.override.preview.showHiddenPages = 1
```

### Noch ein letztes Beispiel

```typo3_typoscript
admPanel.override.preview.showHiddenRecords = 1
admPanel.override.edit.displayFieldIcons = 1
admPanel.override.edit.displayIcons = 1
admPanel.hide = 1
```

Das admPanel ist versteckt, aber Ihr habt die Symbole zum Bearbeiten des Inhalts und Ihr könnt verborgene Datensätze editieren. Das ist auch der Grund warum auf der Webseite nun "Preview" erscheint...denn Ihr seht nun nicht mehr das 1zu1-Abbild der originalen Webseite
