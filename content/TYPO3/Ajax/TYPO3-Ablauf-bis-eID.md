+++
title = "Ablauf von TYPO3 bis zum Einsatz von eID"
linkTitle = "TYPO3 Ablauf bis eID"
date = 2024-01-06T18:51:15+01:00
slug = "typo3-ablauf-bis-eid"
weight = 1
+++

Hier eine Aufstellung über die wichtigsten Schritte, die TYPO3 durchläuft, bis der Punkt 
zur eID-Schnittstelle erreicht ist:

### index.php

- Prüfen, ob mindestens PHP 5.5 verfügbar ist
- Laden des Composer Autoloader. Ab jetzt könnt Ihr ohne require() sämtliche TYPO3-Klassen 
  laden
- Erstellen des Http/Application Objektes für das Frontend inkl. Laden der run() Methode

### sysext/frontend/Classes/Http/Application.php

- Der Konstante TYPO3_MODE den Wert "FE" zuweisen
- Weiterleitung zum Installtool, falls benötigte Konfigurationen noch nicht vorhanden sind
- Registrierung zweier RequestHandler: Frontend-Aufruf und eID-Aufruf

### sysext/core/Classes/Core/Bootstrap.php

- Zusammenführen der DefaultConfiguration, LocalConfiguration und AdditionalConfiguration
- Einrichten des Caching Frameworks
- Laden des PackageManagers. Bis hierhin weiß TYPO3 nur, welche Pakete in welcher 
  Reihenfolge vorhanden und geladen sind. Die TCA, respektive die ext_tables.php, wurde
  noch nicht geladen.
- Befüllen der Datenbank-Konstanten (TYPO3_db, TYPO3_db_username, TYPO3_db_password, 
  TYPO3_db_host) mit den Werten aus dem Installtool.
- Konvertieren einiger cHash Konfigurationen. 
- Setzen der Zeitzone
- Einrichten der in TYPO3 zur Verfügung stehenden Sprachen. Nicht jedoch das Laden der 
  Sprachen an sich. 
- Falls im Installtools ein höheres PHP memory_limit gesetzt wurde wird nun versucht, 
  diesen Wert mit Hilfe von ini_set explizit zu setzen, sofern dies vom Server überhaupt 
  zugelassen wird. 
- Alle ext_localconf.php Dateien laden, zusammenführen und cachen 
- Laden weiterer Konstanten wie TYPO3_DLOG, TYPO3_ERROR_DLOG und TYPO3_EXCEPTION_DLOG 
- Falls jemand mit Hilfe seiner ext_localconf.php globale Variablen wie PAGES_TYPES, TCA, 
  TBE_MODULES, TBE_STYLES, BE_USER, TBE_MODULES_EXT, TCA_DESCR, LOCAL_LANG oder TYPO3_AJAX 
  definiert hat, so werden diese nun komplett gelöscht. Das ist Aufgabe der ext_tables.php. 
- Herstellen der Datenbankverbindung. Ab jetzt könnt Ihr mit dem DatabaseConnection-Objekt 
  eigene SQL-Abfragen erzeugen. 

Nach einem kurzen Abstecher zurück in das Application-Objekt wird nun die run() Methode 
vom Bootstrap-Objekt geladen.

- Die Methode resolveRequestHandler() macht nun eine Schleife über die beiden oben 
  genannten RequestHandler und prüft, welcher von denen für den aktuellen Request am 
  besten geeignet ist. Ganz grob gesagt: Wenn in der URL ein eID-Parameter enthalten 
  ist, dann wird der eID-RequestHandler geladen, ansonsten der Standard-RequestHandler.
- Laden des TimeTrackers
- Laden des PreProcess-Hooks. Hiermit könnt Ihr und andere User den Request inkl. GET,
  POST und Co verändern, bevor dieser durch Euer eID-Script weiter verarbeitet wird.

### sysext/frontend/Classes/Http/EidRequestHandler.php

- Es wird nun ein leeres Response-Objekt erzeugt. Hier könnt Ihr im späteren Verlauf 
  Headerdaten, Statuscodes und den eigentlichen Inhalt für die Ausgabe hinterlegen.
- Prüfung, ob die angegebene eID in einer der ext_localconf.php Dateien mithilfe von 
  $GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include'] registriert wurde. Wenn nicht gibt's
  den Fehler: eID not registered
- Wenn es sich bei der eID Konfiguration, um ein Callable handelt, wird dieses aufgerufen.
  Wenn es sich um einen Pfad handelt, dann wird diese Datei mithilfe von include geladen
- Was jetzt passiert, liegt in Euren Händen
