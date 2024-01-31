+++
title = "security_check"
date = 2024-01-09T20:52:49+01:00
aliases = ["security-check.html"]
+++

Die Extension `security_check` überprüft mit einem Klick die komplette TYPO3-Installation und listet Euch auf, welche Dateien die falsche Berechtigung haben, welche Einstellungen in der php.ini noch zu machen sind, welche Dateien gelöscht werden sollten, welche BE-User noch als Passwort "password" eingetragen haben und security_check überprüft auch die Datenbank.

Nach der Installation erhaltet ihr in der linken Navigation einen neuen Menüpunkt `Sicherheits check`. Als `Startbildschirm` wird Euch angeboten, euer System nun einem Test zu unterziehen. Bei meiner Installation erschient nach meiner Überprüfung: `Maximum execution time exceeded`. Na super, da wird `security_check` wohl ein bissl zu lange gebraucht haben, um alle meine Subversion-Verzeichnisse zu durchforsten und aufzulisten.

Ich habe eine entsprechende Mail schon an den Entwickler geschickt und vorgeschlagen, die Tests nacheinander oder mithilfe von AJAX zu realisieren. Eine Antwort ist bisher noch nicht eingetroffen. Da ich diese Extension aber als sehr sinnvoll halte, habe ich die Tests einfach aufgesplittet und sind nun einzeln über die Selectbox auswählbar.

Update: Ich habe die Extension komplett auf UTF-8 getrimmt, damit sollten nun auch die deutschen Sonderzeichen sauber angezeigt werden.
