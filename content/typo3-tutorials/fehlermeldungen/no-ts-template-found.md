+++
title = "No TS template found"
date = 2024-01-09T20:45:08+01:00
aliases = ["no-ts-template-found.html"]
+++

Wenn Ihr diesen Fehler erhaltet, dann überprüft folgende Einstellungen:

Ab TYPO3 4.5 ist TYPO3 CMS komplett UTF-8 fähig. Löscht bitte den Eintrag setDBinit aus Eurer localconf.php und leert den kompletten Cache. Bitte nicht einfach über das Installtool die Werte rauslöschen oder den Wert auf einen leeren String setzen, das bringt nix.

Im Haupttemplate den kompletten Templatedatensatz öffnen und unter dem Tab "Optionen" überprüfen, ob die Checkbox "Wurzelebene" aktiv ist.

Wenn Ihr RealUrl deinstalliert habt, dann bitte auch die entsprechenden Optionen aus der .htaccess rausschmeißen.
Alle Tabellen, die mit cf_* anfangen mit Hilfe von phpMyAdmin oder ähnlichem leeren (NICHT löschen!!!).
