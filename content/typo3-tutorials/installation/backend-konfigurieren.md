+++
title = "Backend Konfigurieren"
date = 2024-01-09T20:47:49+01:00
aliases = ["backend-konfigurieren.html"]
+++

Um das Backend auf deutsch zu ändern, muss zu allererst die deutsche Sprache installiert werden. Dazu gehen wir auf den Erweiterungsmanager, wählen dann oben im DropDown-Menü "Translation handling" aus und können dann "Deutsch - [german]" auswählen.

Wenn Ihr die STRG-Taste gedrückt haltet, könnt Ihr auch mehrere Sprachen markieren und installieren.

Mit einem Klick auf "Save selection" ist NUR die Sprache gespeichert, aber die Sprachdateien sind noch nicht auf dem Server verfügbar. Das passiert im 2ten Step.

Mit "Check status against repository" wird Euch angezeigt welche Sprachdateien für Eure Extensions verfügbar sind und im nächsten Schritt heruntergeladen werden können. Es gibt schon mal Probleme, wenn Ihr direkt auf "Update from Repository" klickt. Deshalb immer zuerst auf "Check status against Repository" und dann erst auf "Update from Repository" klicken.

Sollte es Probleme mit der Spracheneinbindung geban, dann schau Euch mal diese Seite an.

## Alle Extensions finden

Richtig vermutet: In der Standardauslieferung von TYPO3 findet Ihr nur die Extensions, die den TYPO3-Sicherheitstest bestanden haben. Da dieser aber Geld kostet, werden Euch bei der Extensionsuche nur die Wenigsten angezeigt. Um alle anzuzeigen müsste Ihr in den Extension Manager, dann oben im DropDown auf "Settings" umstellen und dann den Haken bei "Enable extensions without review (basic security check)" markieren und ganz unten noch auf "Update" klicken.

Danach wechselt Ihr oben im DropDown-Menü auf "Import Extensions" und klickt den Haken bei "Show obsolete" an. Das macht vielleicht nicht unbedingt Sinn, aber an genau solchen nicht gesetzten Haken liegt es später, dass Ihr eine in einem Tutorial erwähnte Extension in diesem Menüpunkt nicht findet. Ich will immer auf alle Extensions zugreifen und zu Gesicht bekommen. Also mach ich den Haken rein.

Der nächste Schritt ist der Klick auf "Retrieve/Update". Das kann schon mal 20-25 Sekunden dauern bis die derzeit über 4100 Extensions eingelesen sind. Es werden in diesem Schritt nur die Informationen zu den Extensions eingelesen nicht aber die Extensions selbst.

## Welche Extensions?

Folgende Extensions sind für mich unentbehrlich und wichtig für jeden Aufbau einer neuen Webseite:

- quixplorer
- phpmyadmin

Diese Extension können bzw. sollten nach dem Fertigstellen der Webseite wieder deaktiviert oder besser noch gelöscht werden.
