+++
title = "Verzeichnisstruktur"
date = 2024-01-09T20:22:11+01:00
aliases = ["verzeichnisstruktur.html"]
+++

In meiner ersten Extbasedokumentation habe ich noch versucht eine Extension aufzubauen, die sich nicht an die Verzeichnisstruktur hält und habe damit die Extension komplett vor die Wand gefahren. Ich habe daraus gelernt und kann Euch nur raten auch Euch an die von Extbase vorgegebene Verzeichisstruktur zu halten.

Jede Klasse in Extbase ist so aufgebaut, dass sie jeder wiederfinden kann. Nehmen wir z.B.

Tx_Extbase_MVC_Controller_ArgumentsValidator

"Tx" steht für Extension

"Extbase" für den Extensionkey. Wir fangen also zu erst an in typo3conf/ext/ nach dieser Extension zu suchen, dann in typo3/ext/ und zu Guter Letzt finden wir das Extensionverzeichnis in typo3/sysext/

Ab jetzt gelten die nächsten Abschnitte (bis auf den Letzten) des Klassennamens als Pfad innerhalb des Verzeichnisses "Classes". Wir öffnen nun also den Pfad typo3/sysext/extbase/Classes/MVC/Controller/

Der letzte Abschnitt spiegelt den Dateinamen ohne .php wieder.

Wie finden unsere Klasse demnach in dieser Datei:

typo3/sysext/extbase/Classes/MVC/Controller/ArgumentsValidator.php

## Die Verzeichnisse

Erstellt zu aller erst ein neues Extensionverzeichnis in typo3conf/ext/. Euer Extensionverzeichnis muss immer kleingeschrieben werden und darf keine Sonderzeichen enthalten. Zwar ist der Unterstrich in dem Namen erlaubt, aber dieser wird in Extbase konvertiert. Jeder Unterstrich wird entfernt und der darauf folgende Buchstabe wird großgeschrieben. Entscheidet Ihr Euch z.B. für den Namen hallo_du_da, dann müsst Ihr in Extbase überall dort, wo der Extensionkey benötigt wird den Namen: halloDuDa verwenden. Da diese Schreibweise gerne mal sehr schnell vergessen wird, kommt es immer wieder zu Problemen bei der Extensionumsetzung. Also: Tipp von mir: Ein Wort, alles klein und keine Unterstriche für den Extensionkey bzw. Verzeichnisnamen.
