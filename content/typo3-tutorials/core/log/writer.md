+++
title = "Logging API: Writer"
linkTitle = "Writer"
date = 2024-01-06T23:46:02+01:00
slug = "writer"
aliases = ["writer.html"]
+++

Mithilfe der Writer könnt ihr entscheiden, wohin geloggt werden soll.

## DatabaseWriter

Mit diesem Writer werden eure Informationen in der Datenbank protokolliert. Dabei könnt ihr sogar angeben, in welche Tabelle geschrieben werden soll. Standardmäßig wird in die Tabelle `sys_log` geschrieben.

## FileWriter

Dieser Writer protokolliert in die Datei `typo3temp/logs/typo3_%s.log`. Dabei wird das `%s` durch einen Hashwert, der auf Basis des Encryption Keys aus dem Installtool generiert wird, ersetzt. Falls Euch dieser Dateiname nicht gefällt, könnt ihr diesen mit der Option `logFile` ändern.

Bei der Angabe einer eigenen Log-Datei müsst ihr den kompletten Pfad ab DocumentRoot angeben. Also z.B. `typo3temp/myOwnLog/events/status.log`. Mit jedem Request wird geprüft, ob es diese Datei und diese Verzeichnisstruktur gibt. Wenn nicht, dann wird Verzeichnis und Datei für Euch automatisch angelegt.

## NullWriter

Easy erklärt. Alles, was an die `writeLog()` Methode geschickt wird, wird im nächsten Schritt auch schon wieder vergessen. Beispiel wäre zum Beispiel sowas wie eine DeprecationLog. Sehr sinnvoll für den Debugmodus, aber auf LIVE will man sowas nicht haben und könnte dann mit dem NullWriter das Schreiben verhindern.

## PhpErrorLogWriter

Dieser Writer arbeitet eng mit dem Webserver zusammen. Mithilfe der Funktion `error_log()` wird die Nachricht an die in der Webserverkonfiguration hinterlegte Error Datei gesendet. Oftmals heißt diese Datei `php_error.log`. Ich empfehle diesen Writer nur dann zu verwenden, wenn es sich auch tatsächlich um einen Fehler handelt. Informationen oder Debugausgaben würden die Übersicht im Fehlerprotokol nur erschweren.

Die Funktion `error_log()` erlaubt zwar auch das Versenden der Nachricht als E-Mail, aber in dem PhpErrorLogWriter wird von dieser Option kein Gebrauch gemacht und kann somit nicht verwendet werden.

## SyslogWriter

Ja, richtig gesehen, das "L" wird kleingeschrieben, denn dieser Writer hat nichts mit der `sys_log` Tabelle von TYPO3 zu tun. Der Writer verwendet die Funktion `syslog()`, und weist somit das Betriebssystem an, die Daten zu protokollieren. Auf Linuxsystemen landet das Protokol üblicherweise im Verzeichnis `/var/log/`.
