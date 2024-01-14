+++
title = "Iptables"
date = 2024-01-09T20:59:11+01:00
slug = "iptables"
+++

Mit iptables wird die Firewall auf Linux Betriebssystemen konfiguriert.

## Kommandos

Listet alle Rollen auf, gruppiert nach Chains:

```shell
iptables -L
```

Liste alle Rollen einer bestimmten Chain auf:

```shell
iptables -L [Chain]
iptables -L DOCKER
```

Falls ihr sehr viele Rollen habt, oder Dank `fail2ban` sehr viele IP-Adressen geblockt sind, dann die Auflistung sehr lange dauern. In diesem Falle fügt ihr noch ein `-n` an, um die Namensauflösung (IP zu Domainname) zu unterdrücken:

```shell
iptables -L -n
iptables -L DOCKER -n
```

Für das Hinzufügen von Rollen verwende ich gerne diesen [iptables Generator](https://www.perturb.org/content/iptables-rules.html).

Einfach IP-Adresse hinzufügen, noch ein "sudo" davor und Euch wird der Befehl automatisch zusammengebaut.

Löschen könnt ihr Rollen mit `-D`

```shell
iptables -D DOCKER-USER 3
```

Wobei die 3 die Rolle in der 3ten Zeile bedeutet.

Alle Rollen, die ihr hinzufügt, sind nur temporär im Arbeitsspeicher aktiv. Startet ihr euren Server neu, sind alle eure Änderungen futsch. Bei Ubuntu/Debian könnt ihr folgenden Befehl ausführen, um eure Änderungen persistent zu machen:

```shell
sudo iptables-save
iptables und Docker
```

Docker registriert sich selbst mit einigen Chains in `iptables`. Die wichtigsten sind wohl `DOCKER` und `DOCKER-USER`. Lieb gemeinter Rat: Lasst die Finger von der `DOCKER` Chain und arbeitet ausschließlich mit der `DOCKER-USER` Chain.

Schonmal mit `Solr` gearbeitet? Ich empfinde die Berechtigungsstruktur als schwierig und blocke darum den Port `8983` für alle IP-Adressen außer dem Server auf dem Solr benötigt wird. Dazu füge ich folgende Rollen hinzu:

```shell
sudo iptables -I DOCKER-USER 1 -i eth0 -p tcp -s 1.2.3.4 --dport 8983 -j ACCEPT
sudo iptables -I DOCKER-USER 4 -i eth0 -p tcp -s 0.0.0.0/0 --dport 8983 -j REJECT
```

Mit `-I` gebe ich die zu verwendende Chain an.

Mit `-i` das zu verwendende ethernet. Mit `ifconfig` kommt ihr an diesen Wert dran. Sucht dort nach der öffentlichen IP-Adresse Eures Servers

Mit `-p` gebe ich das Protokoll an: `tcp` oder `udp`. Nur doch diesen Parameter steht Euch das Argument `--dport` überhaupt erst zur Verfügung.

Mit `-s` gebe ich die Quell-IP des Servers an, der den Solr Dienst benötigt. Hier nur beispielhaft mittels 1.2.3.4

Mit `--dport` gebe ich den destination Port des Servers an, auf dem der Solr Dienst läuft.

Mit `-j` gebe ich an, wie die Firewall, sofern die Rolle übereinstimmt, reagieren soll. Mögliche Werte sind hier `ACCEPT`, `DROP` und `REJECT`.

Ich bevorzuge `REJECT` immer vor `DROP`. Bei Verwendung von `DROP` sieht es im Browser immer so aus als würde die Webseite noch laden und irgendwann nach Minuten kommt dann endlich ein Timeout. Da nehme ich lieber `REJECT` und teile jedem sofort mit, dass keine Verbindung hergestellt werden kann.

Die Zahlen hinter der Chain geben eine Position an, wo diese Rolle der Chain hinzuzufügen ist. Würdet ihr die Zeilen ohne diese Angabe hinzufügen, würde die letzte Zeile mit dem REJECT ganz oben in der Chain hinzugefügt werden. Wenn eine Rolle, die alles blockt, an erster Position steht, dann wird keine weitere Rolle mehr überprüft. Die Rolle für den erlaubten Server wird also gar nicht mehr abgearbeitet und wird ebenfalls geblockt. Darum: Alles was erlaubt ist noch ganz oben und alles was verboten ist nach ganz unten.

Falls ihr mit den Positionen etwas durcheinander kommt, weil es schlicht zu viele sind, dann könnt ihr den Parameter `--line-numbers` hinzufügen:

```shell
iptables -L DOCKER-USER -n --line-numbers
```

## iptables und DDEV

Ja, DDEV ist nicht für den LIVE-Betrieb konzipiert und trotzdem habe ich ein paar TYPO3 Testinstanzen öffentlich laufen. Damit die DDEV Projekte an die öffentliche Ethernet Schnittstelle gebunden werden können, muss bei DDEV in der `~./.ddev/global_config.yaml` die Zeile `router_bind_all_interfaces` auf `true` gesetzt werden. Damit ist es nun möglich FQDN für Euer Projekt in der `.ddev/config.yaml` zu registrieren:

```yaml
additional_fqdns:
- typo3104.my-domain.de
```
Leider werden für diese Projekte auch automatisch die Ports 80, 443, 8025, 8026 und 8036 geöffnet. Während Port 80 und 443 durchaus erwünscht sind, sollte gerade der Port 8036, mit dem jeder Zugriff auf PhpMyAdmin hätte, geblockt werden. DDEV selbst schlägt vor, den dba (PhpMyAdmin) Container in der `~/.ddev/global_config.yaml` für alle Ethernets (Netzwerk-Interfaces) zu deaktivieren:

```yaml
omit_containers: [dba]
```

Wenn ihr aber trotzdem unbedingt PhpMyAdmin haben wollt, dann blockt diesen Port bitte für alle IP-Adressen, außer eurer eigenen IP-Adresse:

```shell
sudo iptables -I DOCKER-USER 9 -i eth0 -p tcp -s 0.0.0.0/0 --dport 8036 -j REJECT
sudo iptables -I DOCKER-USER 1 -i eth0 -p tcp -s 123.234.123.234 --dport 8036 -j ACCEPT
```

Falls sich eure IP-Adresse jeden Tag ändert, könnt ihr euch mithilfe von Google Cloud zum Beispiel eine kleine f1-micro Instanz mit Docker und OpenVPN bauen. Die erste Instanz ist im Monat komplett kostenlos. Ist zwar arsch langsam, aber fürs Surfen absolut ausreichend und ihr habt ne statische IP-Adresse. Falls ihr ne eigene Webseite habt, auf die ihr mittels SSH draufkommt, dann macht euch mal bzgl. SOCKS Proxy schlau. Auf diese Weise hättet ihr dann auch ne kostenfrei statische IP-Adresse, die ihr dieser Rolle hinzufügen könnt.

Vergesst nicht den oberen Befehl auch für die Ports 8025 und 8026 durchzuführen, damit Fremde nicht an die versendeten Email dran kommen.
