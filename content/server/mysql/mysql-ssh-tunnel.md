+++
title = "Zugriff auf MySQL per SSH-Tunnel"
linkTitle = "MySQL über SSH-Tunnel"
date = 2024-01-09T21:01:46+01:00
slug = "mysql-ueber-ssh-tunnel"
aliases = ["mysql-ueber-ssh-tunnel.html"]
+++

Bei vielen Hostern kann man den MySQL-Server von extern erreichen. So z.B. bei dem TYPO3-Hoster [1blu](http://www.1blu.de/). Wer sich dann mal mit `SHOW GLOBAL STATUS` den Wert von `Aborted_connects` anschaut, wird mit Werten im hohen 1000er-Bereich begrüßt. Dieser Wert sagt aus, wie oft der MySQL-Server den Versuch eine Verbindung aufzubauen abgebrochen hat. Zum Beispiel, weil das Passwort falsch ist. Ich sage mal ganz vorsichtig: Im Falle von 1blu werden wohl knapp 95% dieser Connects Hackversuche sein. Warum ich das behaupte? Bei den Hostern [jweiland.net](http://jweiland.net/) und [domainfactory](http://www.df.eu/) sind die Server nicht extern erreichbar und der Wert `Aborted_connects` liegt oft unter 10.

Ein MySQL-Server, der nur per `localhost` erreichbar ist, ist demnach sicherer.

In meinem Fall habe ich ein Projekt, dessen Datenbank knapp 6 GB beträgt. Recherchen lieferten mir folgende 2 Webseiten

- [Lösung: jweiland.net](https://jweiland.net/typo3/codebeispiele/datenbanken.html#c2565)
- [Lösung: Domainfactory](http://www.df.eu/de/service/df-faq/webhosting/webspace-zugriff/ssh/)

## Meine Lösung

Ihr benötigt 2 Konsolenfenster. In dem ersten Fenster gebt ihr ein:

```shell
ssh -l [Benutzername] -L 3307:127.0.0.3:3306 [Domainname]
```

Bei mir auf meinem Rechner läuft lokal bereits ein MySQL-Server weshalb ich mich hier lokal für den Port 3307 entschieden habe. Bei jweiland.net und bei df.eu läuft der MySQL 5-Server auf der IP-Adresse 127.0.0.3 und horcht auf den Port 3306.

Mit diesem Befehl haben wir nun eine Portweiterleitung konfiguriert. Alle Anfragen an den lokalen Port 3307 werden zu dem Port 3306 auf dem Server weiter geleitet.

Falls ihr mit Public-Key-Authentifizierung arbeitet und den Server in die `.ssh/config` eingetragen habt, dann könnt ihr den Part mit dem `-l` auch weglassen:

```shell
ssh -L 3307:127.0.0.3:3306 [Domainname]
```

Mit diesen Befehlen loggt Ihr Euch auf dem Server ein und befindet Euch nun auf der Shell des Remoteservers. Hier braucht Ihr nichts weiter zu machen. Das Fenster muss nur offen bleiben.

Kommen wir zum zweiten Fenster:

```shell
mysql -h127.0.0.1 -P3307 -u[DB-Benutzer] -p [DB-Name]
```

Mit `-h` greifen wir nun auf unseren lokalen Rechner zu und senden die MySQL-Verbindungsanfrage mit `-P` an den zuvor konfigurierten lokalen Port 3307. Dank der Portweiterleitung werden die Zugangsdaten jetzt an den Remoteserver gesendet und wir erhalten Zugriff.
