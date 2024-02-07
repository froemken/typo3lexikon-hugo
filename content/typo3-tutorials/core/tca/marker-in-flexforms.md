+++
title = "Marker in Flexforms"
date = 2024-01-09T20:42:59+01:00
aliases = ["marker-in-flexforms.html"]
+++

## Marker in foreign_table_where

In der TYPO3 API Dokumentation gibt es für die Felder vom Typ "select" die Möglichkeit Datensätze von Tabellen anzeigen zu lassen. Dabei stößt man allerdings sehr schnell an seine Grenzen, da erstmal alle Datensätze aus dieser Tabelle nun in unserer Selectbox erscheinen. Um diesen Datenüberfluss ein bisschen zu steuern gibt es die zusätzliche Option "foreign_table_where". Hier können die Entwickler Einfluss auf die SQL-Abfrage nehmen und die Auswahl auf z.B. alle Datensätze einer bestimmten Sprache reduzieren:

```php
'foreign_table_where' => 'AND tx_myext_sys_language_uid = 0',
```

Das ist ein sehr einfaches Beispiel und jedem Admin wird hier die Dynamik fehlen. Deshalb liefert TYPO3 auch gleich ein paar besondere Marker mit, die ich hier etwas ausführlicher erklären möchte:

```php
'foreign_table_where' => 'AND tx_myext_sys_language_uid = 0'
```

Das ist ein sehr einfaches Beispiel und jedem Admin wird hier die Dynamik fehlen. Deshalb liefert TYPO3 auch gleich ein paar besondere Marker mit, die ich hier etwas ausführlicher erklären möchte:

## ###REC_FIELD_[fieldname]###

Dieser Marker greift grundsätzlich nur auf die Felder der aktuell verwendeten Tabelle zu und das ist in unserem Fall tt_content. Ihr könnt damit also nicht auf die Felder der Tabelle pages zugreifen und Ihr könnt auch nicht auf die anderen Felder zugreifen, die sich in der gleichen Flexform befinden. Euer Marker könnte also folgendermaßen aussehen:

```php
###REC_FIELD_sys_language_uid###
###REC_FIELD_tx_myext_myfield###
```

Doch Achtung! Es gibt Felder in der Tabelle tt_content, die vom Typ "group" sind. Diese Felder liefern die Daten in der Tabelle immer im folgenden Format zurück

## [Tablename]_[UID]|[Titel] (z.B. pages_22|Produkte)

Sollte klar sein, dass man damit keinen gescheiten SQL-Befehl aufbauen kann, wenn so ein Müll dabei rauskommt:

Select ... from ... WHERE ... AND tx_myext.pid = 'pages_22|Produkte';

Ich habe zwar einen Workaround ausgearbeitet, aber der funktioniert nur, wenn in solchen Tabellenfeldern eine Zahl drin steht:

'foreign_table_where' => 'AND tx_myext_category.pid = (SELECT pages FROM tt_content WHERE uid=###THIS_UID###)',

bzw. in einer Flexform

AND tx_myext_category.pid = (SELECT pages FROM tt_content WHERE uid=###THIS_UID###)

Dieses Problem habe ich auch im Bucktracker bereits näher dokumentiert.

## ###THIS_UID###

Dieser Marker gibt die UID des aktuellen tt_content-Datensatzes wieder. Solltet Ihr Euren Datensatz gerade erst angelegt und noch nicht gespeichert haben, dann gibt dieser Marker 0 zurück. Wie Ihr den Marker verwendet könnt seht Ihr in meinem Workaround im letzten Abschnitt.

## ###THIS_CID###

Dieser Marker ist nicht dokumentiert und das wird evtl. auch seinen Grund haben. Denn er liefert in meinen Beispielen immer 0 zurück. Ich bin nur auf diesen Marker aufmerksam geworden, weil ich ihn in der class.t3lib_befunc.php gefunden habe.

## ###CURRENT_PID###

Mit diesem Marker erhaltet Ihr die UID der Seite (pages) auf der bzw. in der Eure Datensätze abgespeichert werden

## ###STORAGE_PID###

Jeder Seite kann in den Seiteneigenschaften eine "Allgemeine Datensatzsammlung" hinterlegt werden. Dieser Marker ließt die UID dieser Datensatzsammlung aus.

Vorteil: Der Marker ist Rootline kompatibel. Heißt: Wenn Ihr auf Eurer Seite keine Datensatzsammlung definiert habt, dann wird auf der Elterseite nach einer Datensatzsammlung gesucht. Das geht solange, bis die Rootseite erreicht wurde.

Nachteil: Gerade TemplaVoila benötigt einen Eintrag bei "Allgemeine Datensatzsammlung", damit Ihr auf jeder Seite entscheiden könnt, welches Template Ihr verwenden wollt. Hinterlegt Ihr also eine neue Datensatzsammlung, dann wird der Eintrag für TemplaVoila überschrieben und Ihr könnt keine Templates mehr auswählen.

Entscheidet also selbst, ob es unbedingt der ###STORAGE_PID### sein muss.

## ###SITEROOT###

Dieser Marker gibt bei Vielen von Euch bestimmt 0 aus. Damit dieser Marker etwas sinnvolles zurückgibt, müsst Ihr in den Seiteneigenschaften Eurer Rootseite den Haklen bei "Ist Anfang der Webseite" setzen. Diese Checkbox seht Ihr nicht sofort. Ihr müsst entweder auf das Icon "Weitere Optionen" bei der "Allgemeinen Datensatzsammlung" klicken oder die Checkbox ganz unten "Zweite Optionspalette anzeigen" aktivieren.

## ###PAGE_TSCONFIG_ID###

Ihr könnt in der Seiten TSconfig einen Wert für diesen Marker setzen. Dieser Wert wird mit Hilfe von intval in einen Integer konvertiert. Sowas wie Text oder kommaseparierte UIDs sind hier nicht möglich.

Im pageTSconfig muss der Wert folgendermaßen gesetzt werden:

```typo3_typoscript
TCEFORM.[table].[field].PAGE_TSCONFIG_ID = [INT]
```

Hier ein Beispiel, wenn der Wert für einen Marker in der Plugin-Flexform sein soll:

```typo3_typoscript
TCEFORM.tt_content.pi_flexform.PAGE_TSCONFIG_ID = 22
```

Hier ein Beispiel wie es aussehen muss, wenn Ihr den Marker für einen Datensatz Eurer Extension benötigt:

```typo3_typoscript
TCEFORM.tx_myext.myfield.PAGE_TSCONFIG_ID = 22
```

## ###PAGE_TSCONFIG_IDLIST###

Ihr könnt in der Seiten TSconfig einen Wert für diesen Marker setzen. Dieser Wert wird mit Hilfe von $GLOBALS['TYPO3_DB']->cleanIntList() in eine Komma separierte Liste von Integern konvertiert. Sowas wie Text ist hier nicht möglich.

Im pageTSconfig muss der Wert folgendermaßen gesetzt werden:

```typo3_typoscript
TCEFORM.[table].[field].PAGE_TSCONFIG_IDLIST = [INT,INT,INT,...]
```

Hier ein Beispiel, wenn der Wert für einen Marker in der Plugin-Flexform sein soll:

```typo3_typoscript
TCEFORM.tt_content.pi_flexform.PAGE_TSCONFIG_IDLIST = 20,21,22
```

Hier ein Beispiel wie es aussehen muss, wenn Ihr den Marker für einen Datensatz Eurer Extension benötigt:

```typo3_typoscript
TCEFORM.tx_myext.myfield.PAGE_TSCONFIG_IDLIST = 20,21,22
```

## ###PAGE_TSCONFIG_STR###

Ihr könnt in der Seiten TSconfig einen Wert für diesen Marker setzen. Dieser Wert wird mit Hilfe von $GLOBALS['TYPO3_DB']->quoteStr() überprüft und sichert Euren SQL-Befehl etwas ab.

Im pageTSconfig muss der Wert folgendermaßen gesetzt werden:

```typo3_typoscript
TCEFORM.[table].[field].PAGE_TSCONFIG_STR = [TEXT]
```

Hier ein Beispiel, wenn der Wert für einen Marker in der Plugin-Flexform sein soll:

```typo3_typoscript
TCEFORM.tt_content.pi_flexform.PAGE_TSCONFIG_STR = %hallo%
```

Hier ein Beispiel wie es aussehen muss, wenn Ihr den Marker für einen Datensatz Eurer Extension benötigt:

```typo3_typoscript
TCEFORM.tx_myext.myfield.PAGE_TSCONFIG_STR = %hallo%
```
