+++
title = "dbal"
date = 2024-01-09T20:14:04+01:00
aliases = ["dbal.html"]
+++

TYPO3 konnte damals nativ nur auf MySQL basierten Datenbanksystemen betrieben werden. Mittels der Systemextension `EXT:dbal` wurde die Datenbankklasse von TYPO3 komplett überschrieben, womit TYPO3 auch mit anderen Datenbanksystemen wie PostgreSQL, SQLite, Oracle und anderen zusammenarbeiten konnte. Die benötigten PHP Treiber für diese Datenbanksysteme kamen aus der `EXT:adodb`, die ebenfalls mit TYPO3 8.4 ins TER verschoben wurde.

`EXT:dbal` ist die Schnittstelle zwischen TYPO3 und ADOdb gewesen und konnte mittels einer speziellen Konfiguration in der `localconf.php` bzw. `AdditionalConfiguration.php` konfiguriert werden. Dabei wurde nicht immer das komplette TYPO3 auf ein anderes Datenbanksystem umgebogen, nein, es ging viel mehr darum, vereinzelte Tabellen diverser Fremdsysteme in TYPO3 zu implementieren.

Solange der Extensionentwickler die TYPO3 API für die Datenbank verwendet hat, konnte er so ohne zusätzliche Angaben oder Spezialkonfiguration in der Programmierung selbst, direkt mittels `exec_SELECTgetRows` und den anderen Methoden der `DatabaseConnection.php` auf diese Fremdtabellen zugreifen. Wenn diese Fremdtabellen und ihre Felder auch innerhalb des Listenmoduls verfügbar sein sollten, dann musste auch die TCA entsprechend vorhanden sein.

Ein großes Problem ist die Aufbereitung der SQL Queries. Diese mussten so aufbereitet werden, dass diese ohne Probleme von adodb verwendet werden können. Diese Aufbereitung erforderte eine genaue Analyse der Queries mittels PHP, was pro Query gerne mal über 1000 Zeilen PHP Code erforderte. Für war, wer TYPO3 komplett auf ein anderes Datenbanksystem laufen ließ musste schon ein paar Extrasekunden Geduld mitbringen.
