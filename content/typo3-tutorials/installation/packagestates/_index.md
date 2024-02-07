+++
title = "Packagestates"
date = 2024-01-06T19:11:24+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["packagestates.html"]
+++

In TYPO3 Versionen vor 6.0 gab es in typo3conf/localconf.php einen Arrayeintrag in dem alle aktivierten Extensions komma separiert aufgeführt wurden. Bei 50 aktivierten Extensions wird diese Liste halt auch entsprechend lang. Wichtig war dort auch die Reihenfolge. Eine Extension, die weiter vorne war, deren TCA wurde auch vor dem TCA einer Extension geladen, die sich weiter hinten in der Liste befand.

Mit TYPO3 6.0 wurde aus der localconf.php die LocalConfiguration.php. Dort findet man nun unter anderem die Extension Konfiguration jeder einzelnen Extension. Ob eine Extension nun jedoch in TYPO3 aktiv ist oder nicht, wird über die neue Datei PackageStates.php im typo3conf Verzeichnis gesteuert. Auch hier gilt: Eine Extension, die weiter oben steht, deren TCA wird eher geladen, als das TCA einer Extension, die weiter unten gelistet ist.

Pro Extension gibt es einen Eintrag status, der entweder auf active oder inactive steht. Um eine Extension über die Shell zu deaktivieren, ändert Ihr den Eintrag für die gewünschte Extension entsprechend auf inactive. Danach bitte noch das Verzeichnis typo3temp/Cache/Code/ löschen.

In TYPO3 8 hat sich die Struktur der PackageStates.php sehr geändert. Ganz unten steht bei Version nicht mehr 4, sondern 5. In der Liste sind nur noch aktive Extensions aufgeführt. Wenn Ihr eine Extension deaktivieren wollt, dann müsst Ihr den 3-Zeiler für den Extensioneintrag aus der PackageStates.php entfernen. Danach bitte noch das Verzeichnis `typo3temp/var/Cache/Code/` löschen.
