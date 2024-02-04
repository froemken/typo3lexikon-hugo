+++
title = "Dateien indexieren mit index_externals?"
linkTitle = "Index external"
date = 2024-01-09T20:30:06+01:00
aliases = ["index-external.html"]
+++

Bevor ich diese Option erkläre, möchte ich hier einige Hürden nennen, die mir in den Weg gestellt wurden. Hier mal ein Auszug aus der TSref-Dokumentation zu dieser Option:

```
index_externals | boolean | If set, external media linked to on the pages is indexed as well.
```

Zu gut deutsch: Wenn externe Medien (z.B. pdf, xls, ...) von Seiten verlinkt wurden, werden diese Dateien auch indexiert.

Noch ein Auszug aus dem Wiki von TYPO3:

```
If external files are linked on normal pages, such as links to pdf files, normal indexed search will index them provided you have setup the appropriate external tools.
```

Hier heißt es, dass die "normale" indexed_search Dateien indexiert, solange man sich darum gekümmert hat, die externen Tools, die für die Indexierung der Dateien benötigt werden, auf dem Server auch verfügbar sind.

Grundsätzlich ist das korrekt, aber diese Option muss im TS-Setup auch erstmal aktiviert werden.

## Eine wage Vermutung

Ich persönlich vermute, dass die `indexed_search` damals nur den Seiteninhalt indexieren konnte und es dafür die Option `config.index_enable = 1` gibt. Später hat man herausgefunden, wie man externe Links im Seiteninhalt verfolgen kann, um dann auch externe Webseiten indexieren zu können. Um diese spezielle Möglichkeit nutzen zu können wurde eine weitere Option in das TS-Setup eingefügt:

```typo3_typoscript
config.index_externals = 1
```

Diese Namensgebung ist OK, weil ja externe Links/Inhalte ausgelesen werden konnte.

Der nächste Schritt in der Entwicklung der `indexed_search` sah dann so aus, dass auch Dateiinhalte indexiert werden konnten. Ich weiß nicht, warum für diese neue Option nicht eine Option wie `config.index_media = 1` erstellt wurde. Auf jeden Fall wurde die Indexierung der lokalen Dateien nun auch über die Option `index_externals` realisiert. Das hat nun den Nachteil, dass man denken könnte, externe Dateien werden auch indexiert.

Falls es jemand genauer weiß, dann kontaktiert mich bitte und klärt mich auf.

## Was macht indexed_externals?

Ich habe meine Probleme mit dieser Option in der TYPO3 Developer-Liste gestellt und dort folgende Antwort erhalten (sinngemäß übersetzt):

```
Ich verstehe es so, dass "external" folgendes bedeuten könnte: "Kein Teil des Seiteninhalts". Ich stimme Dir zu, dass diese Beschreibung ein bisschen unverständlich geschrieben wurde.
```

Also: Nach mehreren Tests und dieser Aussage bedeutet `config.index_externals = 1`, das zusätzlich zur reinen Seitenindexierung auch die Inhalte der lokalen Dateien, die vom Seiteninhalt aus verlinkt wurden, indexiert werden.

## indexed_search entsprechend einrichten

Die `indexed_search` selbst kann die Inhalte von Dateien nicht indexieren bzw. lesen. Um die Inhalte von Dateien zu extrahieren, gibt es spezielle Tools, die auf dem Server installiert sein müssen. So gibt es z.B. das Paket xpdf, das die beiden Tools pdftotext und pdfinfo beinhaltet.

Folgende Tools müssen auf dem Server installiert sein, wenn ihr alle per `indexed_search` vorkonfigurierten Dateitypen auslesen wollt:

- catdoc
- pdftotext and pdfinfo
- ppthtml
- unrtf
- unzip
- xlhtml

Den Pfad eines jeden Tools erhaltet ihr auf der Konsole mit:

```shell
which pdftotext
```

Wird kein Pfad zurückgegeben, dann müsst ihr dieses Tool noch auf dem Server installieren. Dieser zurückgelieferte Pfad ohne den Dateinamen muss nun in die Extension-Konfiguration der `indexed_search` eingetragen werden. Sollte der Befehl `which catdoc` also `/usr/local/bin/catdoc` zurückliefern, dann tragt bitte nur `/usr/local/bin/` in die Extension-Konfiguration ein.

Jetzt wo der Server fertig eingerichtet ist und die `indexed_search` entsprechend konfiguriert wurde, könnt ihr das Indexieren von Dateien in dem TS-Setup aktivieren:

```typo3_typoscript
config.index_enable = 1 config.index_externals = 1
```

## Weitere Konfiguration

In der Extension-Kofiguration gibt es ein paar interessante Optionen, die nur in Verbindung mit `index_externals = 1` Sinn machen:

```typo3_typoscript
maxExternalFiles = 5
```

Wenn ihr eine Downloadseite mit über 5 Dateien habt, dann werden mit dieser Option nur die ersten 5 Dateien indexiert. Setzt den Wert entsprechend hoch, damit mehr Dateien indexiert werden können. Ja nach Server und Anzahl der Dateien kann ein höherer Wert den Server temporär verlangsamen oder zu lange für die Indexierung benötigen. Letzteres hätte zu Folge, dass die Indexierung mit einem `max_execution_time` abbricht.

```typo3_typoscript
ignoreExtensions = xls,doc,rtf
```

Standardmäßig werden einfach alle Dateien indexiert, für die es auch Tools auf dem Server gibt, solange der Pfad in der Extension-Konfiguration stimmt. Möchtet ihr z.B. xls- und doc-Dateien nicht indexieren, dann könnt ihr die Dateiendungen hier kommasepariert eintragen.

```typo3_typoscript
indexExternalURLs = checked
```

Nur mal vorweg: Diese Option kann keine externen Dateien einlesen. Mit dieser Option lassen sich nur Webseiteninhalte vom Typ `text/html` einlesen. Habt ihr also einen externen Link zu einer ganz normalen Webseite in euren Seiteninhalt eingepflegt, dann verfolgt `indexed_search` diesen Link und indexiert diese fremde Webseite.
