+++
title = "Die Log Levels"
linkTitle = "Log Levels"
date = 2024-01-06T23:38:01+01:00
slug = "loglevels"
+++

Die Logging-API arbeitet mit *unterschiedlichen Levels*, mittels denen Sie Ihre Informationen nach Wichtigkeit gruppieren können. Unten sind die Levels, die in `\TYPO3\CMS\Core\Log\LogLevel` als Konstante deklariert wurden.

## Log Levels

* **Notfall/Emergency: (0)**
  * **Anwendungsfall:** Absoluter Notfall. Das System ist unbrauchbar.
  * **Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::EMERGENCY`
  * **Gekürzter Methodenaufruf:** `emergency($message, $data);`

* **Alarm/Alert: (1)**
  * **Anwendungsfall:** Es muss sofort reagiert werden. Die Webseite ist komplett down. 
  Die Datenbank ist nicht erreichbar.
  * **Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::ALERT`
  * **Gekürzter Methodenaufruf:** `alert($message, $data);`

* **Kritisch/Critical: (2)**
  * **Anwendungsfall:** Unerwarteter Absturz. Thrown Exception
  * **Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::CRITICAL`
  * **Gekürzter Methodenaufruf:** `critical($message, $data);`

* **Fehler/Error: (3)**
  * **Anwendungsfall:** Laufzeitfehler. Runtime error
  * **Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::ERROR`
  * **Gekürzter Methodenaufruf:** `error($message, $data);`

* **Warnung/Warning: (4)**
  * **Anwendungsfall:** Verwendet diesen Level z.B. bei Aufruf veralteter APIs oder wenn
  etwas nicht wie gewünscht läuft, aber nicht unbedingt als Fehler gilt.
  * **Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::WARNING`
  * **Gekürzter Methodenaufruf:** `warning($message, $data);`

* **Beachten/Notice: (5)**
  * **Anwendungsfall:** Dinge, die man sich mal anschauen sollte, aber nichts, um das
  man sich Sorgen machen müsste.
  * **Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::NOTICE`
  * **Gekürzter Methodenaufruf:** `notice($message, $data);`

* **Information/Info: (6)**
  * **Anwendungsfall:** Ein Benutzer hat sich angemeldet. SQL Befehle
  * **Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::INFO`
  * **Gekürzter Methodenaufruf:** `info($message, $data);`

* **Debug: (7)**
  * **Anwendungsfall:** Detaillierte Statusmeldungen.
  * **Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::DEBUG`
  * **Gekürzter Methodenaufruf:** `debug($message, $data);`
