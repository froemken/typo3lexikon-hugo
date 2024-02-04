+++
title = "adodb"
date = 2024-01-09T20:13:10+01:00
aliases = ["adodb.html"]
+++

Die Systemextension `EXT:adodb` beinhaltete eine für TYPO3 angepasste Version der PHP Bibliothek [ADOdb](https://adodb.org/dokuwiki/doku.php?id=index). Es handelt sich dabei um eine Art verschiedener Treiber für unterschiedliche Datenbanksysteme. TYPO3 kann nativ nur mit MySQL arbeiten, jedoch konnte mittels dieser Extension die Datenbankklasse von TYPO3 so überschrieben werden, dass TYPO3 auch mit anderen Datenbanksystemen wie PostgreSQL, SQLite, Firebird, verschiedenen ODBC Schnittstellen und anderen zusammenarbeitet, ja sogar ein TYPO3 auf Basis von Oracle habe ich mal gesehen.

Es ging nicht immer darum, dass das komplette TYPO3 System nun auf einer anderen Datenbank läuft. Dank der Systemextension `EXT:dbal` konnte TYPO3 so konfiguriert werden, dass z.B. nur eine ganz bestimmte Tabelle eines anderen Datenbanksystems in TYPO3 integriert wurde.

Mit TYPO3 8.4 gewann Doctrine Einzug in das TYPO3 System. Eine in PHP Kreisen anerkannte Datenbank Abstraktionsschicht, die sehr stabil läuft und auch von anderen PHP Projekten wie Symfony verwendet wird. Mit diesem Einzug wurde die `EXT:adodb` ins TER ausgelagert und kann bei Bedarf wieder nachinstalliert werden.
