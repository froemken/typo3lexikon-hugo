+++
title = "Die Log Levels"
linkTitle = "Log Levels"
date = 2024-01-06T23:38:01+01:00
slug = "loglevels"
weight = 1
+++

Die Logging API arbeitet mit unterschiedlichen Levels, mit denen Ihr Eure Informationen
nach Wichtigkeit gruppieren könnt. Im Folgenden die Levels wie sie in der Klasse
`\TYPO3\CMS\Core\Log\LogLevel` als Konstante deklariert wurden.

## Notfall/Emergency: 0

**Anwendungsfall:** Absoluter Notfall. Das System ist unbrauchbar.

**Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::EMERGENCY`

**Gekürzter Methodenaufruf:** 

```php
emergency($message, $data);
```

## Alarm/Alert: 1

**Anwendungsfall:** Es muss sofort reagiert werden. Die Webseite ist komplett down. Die Datenbank ist nicht erreichbar.

**Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::ALERT`

**Gekürzter Methodenaufruf:**

```php
alert($message, $data);
```

## Kritisch/Critical: 2

**Anwendungsfall:** Unerwarteter Absturz. Thrown Exception

**Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::CRITICAL`

**Gekürzter Methodenaufruf:**

```php
critical($message, $data);
```

## Fehler/Error: 3

**Anwendungsfall:** Laufzeitfehler. Runtime error

**Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::ERROR`

**Gekürzter Methodenaufruf:**

```php
error($message, $data);
```

## Warnung/Warning: 4

**Anwendungsfall:** Verwendet diesen Level z.B. bei Aufruf veralteter APIs oder wenn etwas nicht wie gewünscht läuft, aber nicht unbedingt als Fehler gilt.

**Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::WARNING`

**Gekürzter Methodenaufruf:**

```php
warning($message, $data);
```

## Beachten/Notice: 5

**Anwendungsfall:** Dinge, die man sich mal anschauen sollte, aber nichts, um das man sich Sorgen machen müsste.

**Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::NOTICE`

**Gekürzter Methodenaufruf:**

```php
notice($message, $data);
```

## Information/Info: 6

**Anwendungsfall:** Ein Benutzer hat sich angemeldet. SQL Befehle

**Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::INFO`

**Gekürzter Methodenaufruf:**

```php
info($message, $data);
```

## Debug: 7

**Anwendungsfall:** Detailierte Statusmeldungen.

**Konstante:** `\TYPO3\CMS\Core\Log\LogLevel::DEBUG`

**Gekürzter Methodenaufruf:**

```php
debug($message, $data);
```
