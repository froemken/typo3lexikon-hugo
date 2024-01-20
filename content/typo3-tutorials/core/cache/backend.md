+++
title = "Caching Framework: Backend"
linkTitle = "Backend"
date = 2024-01-06T22:14:05+01:00
slug = "backend"
aliases = ["backend.html"]
+++

## Caching Framework: Backend

Mit den Cache Backends kann pro Cache angegeben werden WO die Daten gespeichert werden sollen. Bedenkt bitte, dass jeder Server andere Software, PHP-Module und Hardware installiert haben kann. TYPO3 kommt derzeit nur mit einer alternativen Konfiguration für den Extbase Object Cache daher, sofern das APC- oder APCu-Modul geladen ist und
über 100 MB zur Verfügung stellt. Wenn Ihr keine andere Konfiguration vorgebt, dann verwendet TYPO3 das Typo3DatabaseBackend. Wenn Ihr einen Cache auf ein anderes Backend abändern wollt, dann müsst Ihr die Konfiguration selbst vornehmen. Das gilt nicht nur für Eure oder die mitgelieferten TYPO3 Core Caches, sondern auch für Caches, die durch Extensions angelegt wurden.

## \TYPO3\CMS\Core\Cache\Backend\ApcBackend

Mit Einführung von OpCache in PHP 5.5 schwand die Daseinsberechtigung von APC. Nach vielen Jahren hat auch TYPO3 das erkannt und diesen Cache mit Version 10.0 entfernt.

Als Alternative empfehle ich auf das ApcuBackend zu setzen.

## \TYPO3\CMS\Core\Cache\Backend\ApcuBackend

Im Gegensatz zum ApcBackend benötigt dieser Cache nur den APC UserCache. Dieser kommt standardmäßig mit 32 MB daher. Zu wenig, um dieses Backend für das Cachen der Extbase Objekte zu verwenden. Dieser erfordert mindestens 100 MB. Ich empfehle die Größe mittels der PHP Einstellung apc.shm_size auf 128 MB zu setzen.

Die Daten sind nur innerhalb einer PHP Instanz gültig. Wenn der Hoster mehrere PHP Instanzen startet (FCGI), dann wird nur eine dieser Instanzen mit den Cachedaten gefüllt. Wenn die Webseite dann erneut geladen wird, dann kann es passieren, dass eine andere PHP Instanz verwendet wird, die noch nicht mit den Cachedaten gefüllt wurde. Es kann also 
etwas dauern bis alle PHP Instanzen gefüllt sind, und Deine Seite in den Genuss der Geschwindigkeit kommt.

Wenn ihr TYPO3 über den scheduler oder die Shell verwaltet, dann muss die Eigenschaft `apc.enable_cli` aktiviet werden.

Bei einigen Hostern gilt für die CLI eine andere php.ini wie für den Webserver. Es kann also sein, dass APC trotz Aktivierung nicht auf der CLI zur Verfügung steht. In diesem Fall sucht Euch den php.ini Pfad mithilfe von `phpinfo()` raus und gebt diesen Pfad auf der Shell zusätzlich mit an. Im Folgenden ein Beispiel. Die Pfade müssen an Euer System angepasst werden.

```shell
#! /bin/bash
env -i /usr/local/bin/php7.0.13-cli -c /etc/php.ini/7/330366/2382053/php.ini -f /www/123456_12345/rp-hosting/1234/5678/typo3cms/typo3lexikon/typo3/cli_dispatch.phpsh extbase [command]
```

## \TYPO3\CMS\Core\Cache\Backend\FileBackend

Mit diesem Backend werden die Daten in Dateien ausgelagert. Es handelt sich hierbei um den großen Bruder des SimpleFileBackends und kommt mit ein paar Spezialfeatures daher.

Bei dem FileBackend kann jede Cache-Datei mit einem Tag oder mehreren Tags versehen werden. Eine Art der Zugehörigkeit, wie z.B. Kategorien. Auf diese Weise könnt Ihr alle Caches-Einträge eines oder mehreren Tags löschen. Da ein Dateiname in seiner Länge beschränkt ist, werden die Tags ans Ende einer jeden Cache-Datei eingebunden. Auch,
wenn die Entwickler hier sehr performanceorientiert programmiert haben, so muss bei Verwendung von Tags jede Datei geöffnet werden, was an sich schon erhebliche Geschwindigkeitseinbußen beinhaltet.

Je größer dieser Cache ist, je mehr Tags ihr verwendet, desto langsamer wird dieser Cache. Ihr solltet Euch genau überlegen, ob ihr diesen Cache wirklich verwenden wollt oder lieber auf einen schnelleren Cache wechseln wollt.

Ein weiteres Feature ist der Freezemodus. Richtig gehört: Ihr könnt den Cache einfrieren lassen. Eine Art read-only Modus. In diesem besonderen Modus kann kein Cacheeintrag mehr gelöscht oder hinzugefügt werden. Beim Einfrieren, werden Duplikate aller Cachedateien erzeugt, die dann keine Metadaten wie Ablaufdatum und Tags beinhalten.
Die neuen Dateinamen werden serialisiert und in eine eigene Cachedatei geschrieben. Beim Laden des FileBackends wird diese Datei geladen und die Dateipfade stehen wieder zur Verfügung. Mittels get() könnt ihr nun nur noch auf die gefreezten Dateien zugreifen.

Nur mit `flush()` kann der Freezemode aufgehoben werden. Die enthaltenen Cachedateien werden dabei komplett gelöscht.

Der Core verwendet diesen Cache von Haus aus nicht.

## \TYPO3\CMS\Core\Cache\Backend\MemcachedBackend

Wenn euch das PHP Modul Memcached zur Verfügung steht und mindestens eine RAM-Datenbank angelegt habt, kann TYPO3 diese verwenden. Solltet Ihr die Größe der Datenbank im Arbeitsspeicher zu klein gewählt haben, dann werden bei einem überfüllten Speicher die neu zu speichernden Daten zwar gespeichert, aber dafür werden andere Cachedaten wieder rausgeschmissen. Im Bereich von Shared Hosting Paketen ist dieser Cache nur sehr selten zu finden.

## \TYPO3\CMS\Core\Cache\Backend\NullBackend

Dieses Backend ist einfach zu erklären. Alle Daten, die ihr an dieses Backend sendet, werden direkt verworfen. Das kann zum Beispiel während der Entwicklung von Vorteil sein. Das ständige Clear Cache entfällt.

## \TYPO3\CMS\Core\Cache\Backend\PdoBackend

Dank PHP:PDO könnt Ihr eine Verbindung zu einem anderen oder auch dem gleichen Server mit einer anderen Datenbank aufbauen, der sich dann um die Aufnahme der Cachedaten kümmern.

## \TYPO3\CMS\Core\Cache\Backend\RedisBackend

phpRedis ist ein PHP Modul und muss über die php.ini auf Eurem Server zur Verfügung stehen. Es arbeitet ähnlich dem System von Memcache. Vorteil: Er ist extremst schnell und es ist völlig egal, ob die Datenbank 10 oder 10 Millionen Einträge enthält. Die Datenbank ist immer gleich schnell. Nachteil: Dieses Modul ist extrem Speicher hungrig.

Zusätzlich kann Redis die Daten auch auf der Festplatte ablagern und hat somit nicht die Probleme, wie bei Memcache.

## \TYPO3\CMS\Core\Cache\Backend\SimpleFileBackend

Der kleine Bruder des FileBackend. Kein Tagging, kein Freezemode. Jeder Aufruf von `set()` erzeugt eine neue Datei im Cacheverzeichnis. Jedoch wird bei jedem Zugriff geprüft, ob die Cachingdauer abgelaufen ist, was etwas Performanceeinbußen mitbringt. Nichtsdestotrotz gehört es neben dem Typo3DatabaseBackend zu den wichtigsten Backends.

## \TYPO3\CMS\Core\Cache\Backend\TransientMemoryBackend

Transient - vergänglich. Alles, was ihr hier reinschreibt, gilt nur für den aktuellen Request. Danach sind die Daten weg und müssen im nächsten Request erneut erzeugt werden. Ich verwende dieses Backend gerne im Bereich von FAL. So habe ich zum Beispiel den Dropbox Driver programmiert und festgestellt, dass zu ein und demselben Verzeichnis die Dropbox API immer und immer wieder angefragt wird. Da diese jedoch sehr langsam ist cache ich diese Information im TransientMemoryBackend.

## \TYPO3\CMS\Core\Cache\Backend\Typo3DatabaseBackend

Wenn kein Backend angegeben wird, dann wird standardmäßig das Typo3DatabaseBackend verwendet. Die Cachetabellen werden in der gleichen Datenbank angelegt, in der sich auch das TYPO3-System befindet.

## \TYPO3\CMS\Core\Cache\Backend\WincacheBackend

Dieses Backend ist dem ApcBackend sehr ähnlich und bringt ebenfalls einen PHP Precompiler und einen UserCache mit. Unterschied: Dieses Backend gilt nur für Microsoft basierte Webserver.
