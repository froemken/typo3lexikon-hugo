+++
title = "Table cache_hash allready exists"
date = 2024-01-09T20:45:53+01:00
aliases = ["table-cache-hash-allready-exists.html"]
+++

Nach meinem Update des XAMPP-Servers lief TYPO3 überhaupt nicht mehr. Das hat sich dadurch ausgezeichnet, dass auf meiner Webseite zig Fehlermeldungen bzgl. nicht vorhandenen Tabellen standen. cache_hash, cache_imagesize, sys_log und einige andere waren nicht mehr vorhanden.

Mich hat es sehr gewundert, dass es sich nur um Tabellen handelt, die für TYPO3 nicht lebensnotwendig sind. Weiterhin glaube ich, dass es sich sogar nur um die Tabellen handelt, die TYPO3 seit Version 4.2.0 auf InnoDB umstellen will. Bei meiner vorigen XAMPP-Version schlug das immer fehl, doch seit XAMPP 1.7.0 scheint das kein Problem mehr zu sein.

Mithilfe von phpMyAdmin konnte ich mein Problem weiter eingrenzen. Denn dort wurden mir 65 Tabellen angezeigt, aber dadrüber stand, dass sich in meiner Datenbank 72 Tabellen befinden. Das erklärt auch warum die CREATE-Befehle des TYPO3-Installtools mit Table allready exists abgebrochen werden.

Um diese Fehlermeldungen zu beseitigen müsst Ihr im mysql/data-Verzeichnis nach Eurer Datenbank suchen. Ihr werdet feststellen, das zu jeder Tabelle immer drei Dateien gehören, nur die Beschädigten stehen dort mit nur einer frm-Datei. Bitte löscht nun NUR die FRM-Dateien, die auf Eurer TYPO3-Webseite angemeckert werden.

Nach einem Neustart des MySQL-Dienstes und einem erneuten COMPARE im TYPO3-Installtool sollte Eure Webseite wieder funktionieren.
