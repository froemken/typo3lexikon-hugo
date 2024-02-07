+++
title = "Cache Flooding"
date = 2024-01-09T20:44:46+01:00
aliases = ["cache-flooding.html"]
+++

## Cache Flooding Protection - Insecure

Wenn Ihr Euer TYPO3 so konfiguriert habt, dass Ihr Euch bei Fehlern per Email benachrichtigen lasst, dann kann es nach einem TYPO3-Update zu folgender Meldung kommen:

Site: Stefans TYPO3-Seite

Issues:
[ERR]  Cache Flooding Protection                - Insecure

## TYPO3 Sicherheitsupdate vom 13.09.2016

Mit dem TYPO3-Update vom 13.09.2016 wurde eine Sicherheitslücke geschlossen, die verhindert, dass die cf_cache_pages-Tabelle zum Überlaufen gebracht werden kann. Beispiel: Bei Hoster XY gibt es eine Beschränkung der Datenbankgröße auf 1 GB. Mittels der Sicherheitslücke könnte der Hacker die cf_cache_hash über verschiedene URL-Aufrufe soweit volllaufen lassen, bis die Datenbank sagt: Kein Platz mehr. Ab dann, wäre ein Login am Frontend oder auch Backend nicht mehr möglich, da auch diese Operationen Session-Daten in die Datenbank reinschreiben, aber da die Datenbank bereits voll ist, wird die Anmeldung nicht mehr funktionieren.

Um dieses Cache Flooding zu verhindern, empfiehlt TYPO3 bei der Berechnung der cHashes die Seiten-UID mit in diese Berechnung einfließen zu lassen. Bei Neuinstallationen ist das eh schon der Fall, aber bei bestehenden Seiten müsst Ihr Euch selbst darum kümmern. Es handelt sich dabei um diese Zeile, die Ihr entweder über das Installtool oder auch über die AdditionalConfiguration.php aktivieren solltet:

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['cHashIncludePageId'] = true;
```

Jedoch ist Vorsicht geraten. Durch Aktivierung dieser Option werden alle bisherigen cHashes ungültig und es kann zu Problemen kommen:

## Innerhalb einer Extension

Falls eine Extension mit Hilfe der CacheHashCalculator API Links erstellt, dann kennt diese Extension die neue Option cHashIncludePageId noch gar nicht. Somit werden diese Extensions auch weiterhin cHashes erstellen in denen die Seiten-UID nicht mit einberechnet wurden. Fazit: TYPO3 wird bei Aufruf eines solchen Links, diesen, je nach Konfiguration im Installtool (siehe unten), für ungültig erklären (Fehler 404).

## Ihr verwendet RealUrl

Leert (TRUNCATE) die tx_realurl_* Tabellen und die beiden cf_cache_hash*-Tabellen

Warnung: Etwas Bauchschmerzen habe ich schon dabei. Hin und wieder kommt es vor, dass die realurl_conf.php verändert wird. Die vorher gültigen Cacheeinträge werden durch diese Änderung aber nicht gelöscht und sind auch weiterhin gültig. Alte Links von Fremdwebseiten oder auch Suchmaschinen funktionieren nur deshalb noch, weil diese alten Einträge noch in den RealUrl-Cachetabellen enthalten sind. Nach Leerung dieser Tabellen werden diese Links auf jeden Fall ins Leere laufen. Mit Hilfe diverser Webmaster Tools könnt Ihr sehen, welche Links auf einmal einen 404 zurückliefern. Erstellt für diese Seiten z.B. eine 301-Weiterleitung in der .htaccess.

## Ihr verwendet kein RealUrl

Der cHash-Parameter befindet sich in vielen Eurer Links. Gerade bei Detailansichten. Da diese URLs auch in diversen Suchmaschinen indexiert sind, kommt es nun auf einen wichtigen Parameter im Installtool an:

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['pageNotFoundOnCHashError'] = 1;
```

Wenn dieser Parameter aktiv ist, dann werden alle externen Links auf Eure Seite einen 404-Fehler auslösen. Grundsätzlich finde ich es gut diesen Parameter aktiv zu haben, aber in dieser besonderen Situation solltet Ihr den lieber deaktivieren. Im deaktivierten Zustand ist der cHash im Link der externen Seite zwar ungültig, aber Eure Webseite funktioniert zumindest.

Warnung: Jede Seite mit einem ungültigen cHash wird ungecacht ausgeliefert! Das kann je nach Seite und verwendeter Extension erhöhte Serverlast bedeuten.
