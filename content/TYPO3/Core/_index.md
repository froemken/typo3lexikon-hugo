+++
title = "Core"
date = 2024-01-06T19:10:57+01:00
slug = "core"
alwaysopen = false
+++

## TYPO3-Internas: Der Core

Der Quellcode von TYPO3 ist gewaltig. Gewaltig groß. Umfassend.

Für sämtliche Anwendungsfälle gibt es eine eigene API. Ihr braucht Zugriff auf die 
Datenbank? Dann schaut Euch das Objekt DatabaseConnection an. Ihr wollt mit Hilfe 
von PHP Euer eigenes TypoScript verarbeiten? Dann schaut mal bei dem ContentObjectRenderer 
vorbei. Ihr braucht Methoden für Workspaces, Versioning und LanguageOverlay im Backend? 
Das BackendUtility bringt genügend Methoden dafür mit. Ihr liebt Konventionen? Dann 
haltet Euch daran, erstellt eigene AMD-Module in Euren Extensions und bindet diese 
JavaScript-Dateien direkt mit Hilfe von RequireJS ins Backend ein. Alles was Ihr dafür
braucht findet Ihr im PageRenderer. Für die Verschlüsselung von Passwörtern gibt es eigene
Systemextensions.

Seit TYPO3 6.2 ist keine TYPO3-Klasse mehr da, wo sie einmal war. Alle Klassen wurden
sinnvoll gruppiert und in Systemextensions verschoben.

Hin und wieder entdecke ich ein paar nützliche Objekte und Methoden, die ich Euch hier
nicht vorenthalten will. Viel Spaß beim Stöbern.
