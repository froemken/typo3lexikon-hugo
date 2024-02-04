+++
title = "Crawler einrichten"
linkTitle = "Crawler"
date = 2024-01-09T20:29:57+01:00
aliases = ["crawler.html"]
+++

Die Extension `crawler` ist für den Betrieb von `indexed_searc` nicht zwingend erforderlich, aber diese Extension kann euch eine Menge Arbeit abnehmen. Wie ihr in meiner Dokumentation zur indexed_search bestimmt schon gelesen habt, wird der Cache einer Seite nur durch den Besuch dieser Seite im Frontend indexiert. Das mag bei kleinen Seiten noch OK sein, aber sobald ihr mit sehr vielen Seiten und vor allem geschützten Seiten (restricted pages) arbeitet, wird das mit der Indexierung schon eine ganze Ecke schwieriger. Denn dann müsst ihr jede Seite mit einem User einer jeden Frontend-Benutzergruppe besuchen, damit jeder angemeldete User auch nur diese Suchergebnisse erhält, für die er die passende Berechtigung hat.

## Backenduser erstellen

Die Crawlerextension kann den kompletten Seitenbaum durchforsten. Diese Arbeit kann schon mal ein paar Minuten dauern und würde unter normalen Bedingungen die in der php.ini angegebene PHP-Ausführungszeit `max_execution_time` sehr wahrscheinlich überschreiten. Um diesem Problem aus dem Weg zu gehen, bringt der `crawler` ein paar Programme mit, die direkt auf der Konsole des Servers ausgeführt werden können.

Serveradministratoren müssen sich darum kümmern, dass das PHP-Modul auch als CLI verfügbar ist. Im Normalfall ist dies aber schon installiert.

Damit der `crawler` auf alle (auch die geschützten) Seiten zugreifen kann, benötigt dieser einen eingerichteten Backenduser mit dem Namen `_cli_crawler`. Bitte legt diesen Backenduser an und vergebt ein Passwort. Dieser User muss kein Admin sein und er braucht auch keine Benutzergruppenzuordnung. Vergebt einfach den Namen und ein Passwort. Ende.

## Das leidige Thema mit der cli_dispatch.phpsh

TYPO3 bringt von Grund auf eine Datei mit, mit der sich vorkonfigurierte PHP-Programmabschnitte unabhängig von der php.ini ausführen lassen. Diese Datei findet ihr hier:

`typo3/cli_dispatch.phpsh`

Alle Programmabschnitte müssen in TYPO3 definiert sein. Ohne Definition lässt sich der gewünschte Programmabschnitt nicht ausführen.

## Wie führe ich die cli_dispatch.phpsh aus?

Ausführen lässt sich die Datei nur über die Konsole des Servers. Entweder öffnet ihr eine Shell/Terminal oder ihr ruft euch die Shell über SSH auf. Ohne SSH wird's schwierig und ihr könnt evtl. noch etwas über das Programm phpshell reißen.

Auf einem Ubuntuserver könnte der Aufruf so aussehen:

```shell
sudo -u www-data php5 /var/www/typo3/cli_dispatch.phpsh crawler
```

Ohne eine angegebene Option wie hier `crawler` erhaltet ihr eine Fehlermeldung:

```
The first argument must be a valid key.
```

Falls ihr oder euer angegebene Benutzer keine Berechtigungen hat, das Script auszuführen wird das ebenfalls quittiert:

```
PHP Warning:  require(/var/www/typo3conf/temp_CACHED_ps4dc7_ext_localconf.php): failed to open stream: Permission denied in /var/www/typo3_src-4.4.4/t3lib/config_default.php on line 584
```

Ich hab auch schon diese Fehlermeldung erhalten:

```
PHP Notice:  Undefined index: PWD in /var/www/typo3_src-4.4.4/typo3/cli_dispatch.phpsh on line 93

PHP Fatal error:  Uncaught exception 'Exception' with message 'localconf.php is not found!' in /var/www/typo3_src-4.4.4/t3lib/config_default.php:409

Stack trace:
#0 /var/www/typo3_src-4.4.4/typo3/init.php(206): require()
#1 /var/www/typo3_src-4.4.4/typo3/cli_dispatch.phpsh(116): require('/var/www/typo3_...')
#2 {main}
thrown in /var/www/typo3_src-4.4.4/t3lib/config_default.php on line 409
```

Das passiert z.B. wenn man `cli_dispatch.phpsh` in dieser Variante aufruft:

```shell
sudo -u www-data ./cli_dispatch.phpsh crawler
```

Deshalb immer den vollständigen Pfad zur `cli_dispatch.phpsh` angeben.

## Wie finde ich die Namen der Programmabschnitte heraus?

Im Quellcode habe ich eine kleine Lücke dazu gefunden. Gebt einfach als Option irgendeinen Quasch ein, wie z.B.:

```shell
sudo -u www-data /var/www/typo3/cli_dispatch.phpsh trallala
```

Als Ergebnis erhaltet ihr alle erlaubten Optionen:

```shell
The supplied 'cliKey' was not valid. Please use one of the available from this list:
Array
(
[0] => lowlevel_refindex
[1] => lowlevel_cleaner
[2] => lowlevel_admin
[3] => crawler
[4] => crawler_im
[5] => crawler_flush
)
```

Die ersten drei Einträge gehören zu TYPO3 und nicht zur Crawlerextension. Mit ihnen kann der Referenzindex neu aufgebaut werden oder das komplette Backend de-/aktiviert werden.

Der `crawler [3]` macht das Gleiche wie die Besucher: Durch den Aufruf der Seite wird der Index für eben diese erstellt.

Mit `crawler_im [4]` wird eine Liste von Seiten erstellt (queue), die der crawler besuchen und indexieren soll. Zur Information: Ohne eine durch `crawler_im` erstellte Liste, wird der `crawler [3]` nichts indexieren.

crawler_flush löscht die erstellte Liste (queue).

Liste der zu indexierenden Seiten erstellen

In den letzten Jahren wurde `indexed_search` immer weiter verbessert. So gibt es keine Option `proc` mehr und die ganzen TSconfig-Zeilen müssen auch nicht mehr geschrieben werden. Deshalb hier eine Anleitung mit dem Stand Oktober 2010:

Der Crawler benötigt eine Konfiguration, damit er weiß, was er wo auf welcher Seite tun soll. Dieser Konfigurationsdatensatz wird dann auf der gewünschten Seite hinterlegt. Da wir zuallererst einen Seitenindex generieren müssen, erstellen wir auf unserer obersten Seite einen Datensatz vom Typen crawler configuration. Der Name darf keine Leerzeichen enthalten. Tut ihr's doch werden diese nach der Speicherung automatisch entfernt. Vergebt den Namen seitenbaum, setzt den Haken bei: Re-indexing `[tx_indexedsearch_reindex]` und gebt bei `baseUrl` die URL zu Eurem Projekt an (z.B. meineDomain.com/cms/). Danach speichert ihr den Datensatz ab. Gebt ihr die baseUrl NICHT an und versucht die `_cli_dispatch.phpsh` auszuführen, dann kann es zu folgendem Fehler führen:

```
tx_indexedsearch_reindex => Error checking Crawler Result: ...
```

Nur zur Information: Dieser Processing instruction filter ist der Eintrag der damals mithilfe der Option `-proc` angegeben werden musste.

Öffnet nun eine Shell und gebt folgenden Befehl ein:

```shell
sudo -u www-data /var/www/typo3/typo3/cli_dispatch.phpsh crawler_im 1 -d 99 -o queue -conf seitenbaum
```

Direkt hinter `crawler_im` muss die Seiten-UID angegeben werden auf der wir unseren crawler-configuration Datensatz abgelegt haben.

`-d` bestimmt die Ebenentiefe: 0-99

`-o` gibt einen Modus an: `url` = Die URL aller gefundenen Seiten wird in der Konsole ausgegeben. `queue` = Die URLs aller gefundenen Seiten werden in die Tabelle `tx_crawler_queue` geschrieben und können später mit dem Parameter `crawler` indexiert werden. `exec` = Alle URLs werden nicht nur zwischengespeichert, sondern auch direkt indexiert.

`-conf` erwartet den Namen unseres crawler-configurations Datensatzes.

Um euch eine Liste aller Optionen anzeigen zu lassen, gebt ihr nur diese Zeile an:

```shell
sudo -u www-data /var/www/typo3/typo3/cli_dispatch.phpsh crawler_im
```

Mit dieser Zeile stellt ihr den Seitenbaum zusammen und speichert ihn in der Datenbank ab:

```shell
sudo -u www-data /var/www/typo3/typo3/cli_dispatch.phpsh crawler_im 1 -d 99 -o queue -conf seitenbaum
```

Danach könnt ihr selbst entscheiden, wann ihr die Queue abarbeiten wollt:

```shell
sudo -u www-data /var/www/typo3/typo3/cli_dispatch.phpsh crawler
```

Nach der abgearbeiteten Queue könnt ihr nun über Info -> Rootseite --> Selectbox: Indexed search --> Overview - 3 Seiten sehr gut erkennen welche Seiten indexiert wurden und welche nicht. Schaut euch dazu auch das Bild hier oben rechts an. ihr sehr auch, dass die geschützte Seite nicht indexiert wurde.

## Indexierung von geschützten Seiten (restricted pages)

Im letzten Abschnitt habe ich euch gezeigt, dass der Crawler zwar funktioniert, aber geschützte Seiten werden noch immer nicht indexiert geschweige denn über die Suche auch gefunden.

Geht nochmal in den crawler-configuration Datensatz rein und schiebt die gewünschten Benutzergruppen, mit denen der Crawler die Seiten aufrufen soll, in die Linke Spalte. Nach dem Abspeichern führt ihr den Crawler nochmal aus:

```shell
sudo -u www-data /var/www/typo3/typo3/cli_dispatch.phpsh crawler
```

Schaut jetzt nochmal in die indexed_search-Übersicht hinein und ihr werdet feststellen, dass nun auch die geschützten Seiten indexiert wurden.

Ich empfehle nach Änderung der Benutzergruppen im crawler-configurations Datensatz alle index_*-Tabellen in der Datenbank zu leeren (NICHT Löschen!!!). In der indexed_search-Übersicht erscheinen sonst zusätzlich noch die alten Suchergebnisse. Nicht, dass es sonst zu unerwünschten Suchergebnissen kommt.
