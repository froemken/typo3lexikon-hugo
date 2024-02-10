+++
title = "scheduler"
description = "Die System-Extension scheduler listet alle im System registrierten Tasks auf und kann diese entsprechend starten, bearbeiten, löschen und den aktuellen Status des jeweiligen Tasks wiedergeben."
keywords = "typo3 stefan frömken lindlar oberberg oberbergischer kreis tutorial scheduler planer cronjob"
date = 2024-02-09T22:40:39+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["scheduler.html"]
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Bei der System-Extension `scheduler` handelt es sich um ein TYPO3 Backend Modul, um Aufgaben zeitgesteuert anzulegen, zu konfigurieren und bei Bedarf auch manuell auszuführen. Auf diese Weise könnt ihr z.B. alle 6 Stunden die Versionsliste von Extensions aktualisieren lassen, Log-Einträge älter als 180 Tage löschen oder auch einen Status-Report von eurem TYPO3 System erstellen und euch per E-Mail zuschicken lassen.

## Installation

Prüft nach der Installation bitte im Backend unter `Wartung` >> `Analyze database`, ob die `scheduler` eigenen Tabellen angelegt wurden.

### Standalone

Seit TYPO3 4.3 ist `scheduler` bereits enthalten und muss über den Extension-Manager nur noch aktiviert werden.

### Composer

{{% badge style="green" icon="angle-double-up" %}}TYPO3 8.7.7{{% /badge %}}

Installiert den `scheduler` mit folgendem Kommando:

```shell
composer req typo3/cms-scheduler
```

## Konfiguration

| Parameter | Standard | Erklärung |
|-----------|----------|-----------|
| Enable sample tasks | 1 | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.4{{% /badge %}} {{% badge style="red" icon="angle-double-up" %}}TYPO3 12.0{{% /badge %}}<br>Für die Zeit während der Einrichtungs- oder auch Testphase können hiermit 2 zusätzliche Test-Aufgaben "Sleep" und "Test" dem `scheduler` hinzugefügt werden, von denen ihr im Planer Modul auswählen könnt. Wenn ihr diese Option deaktiviert, dann achtet bitte darauf, dass ihr zuvor alle Test-Aufgaben im Planer Modul entfernt habt. |
| Maximum lifetime | 1440 | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}<br>Tragt hier die max. Lebensdauer einer Aufgabe in Minuten ein. Wenn ein Task länger dauert als diese Angabe, dann wird der Task nicht mehr als aktiv laufend in der Aufgaben-Übersicht angezeigt. Die Aufgabe wird nach erreichen dieser Angabe nicht gestoppt! |
| Enable logging | 1 | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}} {{% badge style="red" icon="angle-double-up" %}}TYPO3 9.2{{% /badge %}}<br>Wenn aktiviert, dann wird der Start und das Ende einer jeden Aufgabe in BE Protokoll festgehalten. |
| Use `at` daemon | 0 | {{% badge style="green" icon="angle-double-up" %}}TYPO3 6.0{{% /badge %}} {{% badge style="red" icon="angle-double-up" %}}TYPO3 9.0{{% /badge %}}<br>Ermöglicht das sofortige Ausführen der nächsten Aufgabe, sobald eine vorherige Aufgabe beendet wurde. Auf diese Weise muss nicht erst gewartet werden, bis der CronJob den nächsten Impuls gegeben hat. Die Nutzung diesen Features erfordert jedoch die Installation des [at](https://wiki.ubuntuusers.de/at/) Daemon |
| Show description as hover | 1 | {{% badge style="green" icon="angle-double-up" %}}TYPO3 6.0{{% /badge %}} {{% badge style="red" icon="angle-double-up" %}}TYPO3 7.0{{% /badge %}}<br>Wenn aktiv, dann wird die Aufgabenbeschreibung angezeigt, wenn der Titel mit der Maus für ein paar Sekunden berührt wird. |

## CronJob

Die `scheduler` Extension selbst ist kein CronJob und kann somit keine Aufgaben von alleine zeitgesteuert ausführen. Dies bedarf immer eines zugrundeliegenden CronJobs, den ihr selber anlegen müsst. Viele Webseiten-Hoster bieten bereits einen oder mehrere solcher CronJobs an, die ihr oftmals über die Konfigurationsoberfläche eures Hosters anlegen und verwalten könnt.

Die Häufigkeit der Ausführung könnt ihr meist auf stündlich, wöchentlich oder monatlich stellen. Das liegt bei euch. Wenn ihr Aufgaben anlegen wollt, die mittels `scheduler` alle 6 Stunden ausgeführt werden sollen, dann empfehle ich hier den CronJob auf stündlich einzustellen. Die Ausführung der Aufgaben im `scheduler` unterliegen immer dem Interval des zugrundeliegenden CronJobs. Konfiguriert ihr den CronJob auf stündlich und legt im Planer-Modul Aufgaben an, die alle 30 Minuten auszuführen sind, dann wird diese Aufgabe dennoch nur einmal in der Stunde ausgeführt.

Viele Hoster bieten oftmals keine minütlichen Intervalle an, jedoch erlauben einige Hoster die Angabe des Intervalls in cron-Syntax wie `*/5 * * * MON-FRI` für eine Ausführung alle 5 Minuten von Montag bis Freitag. Die Webseite [crontab guru](https://crontab.guru/) kann bei der Nutzung der cron-Syntax sehr hilfreich sein.

Wählt im TYPO3 Backend das "Planer" Modul und wechselt oben links auf `Konfiguration prüfen`. Im Bereich `CLI-Skript` findet ihr den Pfad, den ihr im CronJob verwenden müsst.

### Standalone

```shell
[PfadZuEurerWebseite]/public/typo3/sysext/core/bin/typo3 scheduler:run
```

### Composer

```shell
[PfadZuEurerWebseite]/vendor/bin/typo3 scheduler:run
```

### Sonderfälle

#### Keine Argumente

In seltenen Fällen lässt der Hoster keine zusätzlichen Argumente wie `scheduler:run` zu. In diesem Fall müsst ihr euch mit einem Trick behelfen. Legt eine ausführbare Datei `scheduler.sh` mit folgendem Inhalt an:

```shell
#! /bin/bash
env -i [PfadZuPhp] -f [PfadZuEurerWebseite]/vendor/bin/typo3 scheduler:run
```

und markiert diese Datei als ausführbar

```shell
chmod +x scheduler.sh
```

Den Pfad zu PHP erhaltet ihr mit

```shell
which php
```

Konfiguriert den CronJob nun so, dass er die `scheduler.sh` Datei ausführt.

#### Fehlende Konfigurationsoberfläche

In seltenen Fällen bieten Webseiten-Hoster keine Möglichkeit an, den Intervall über eine Konfigurationsoberfläche zu setzen. In diesem Fall muss das Intervall über SSH konfiguriert werden

```shell
crontab -e
```

Im darauf folgenden Editor dann folgende Zeile hinzufügen, um den `scheduler` alle 15 Minuten auszuführen

```shell
*/15 * * * * /usr/bin/php [PfadZuEurerWebseite]/vendor/bin/typo3 scheduler:run
```

## CLI Benutzer anlegen

{{% badge style="green" icon="angle-double-up" %}}TYPO3 8.6{{% /badge %}}

Mit dem ersten Ausführen von `scheduler:run` wird automatisch der `_cli_` Benutzer in TYPO3 angelegt. Dieser hat unter anderem spezifische Berechtigungen, um Datensätze mittels DataHandler zu verarbeiten.

{{% notice style="info" title="Frühere TYPO3 Versionen" icon="info" %}}
Früher musste manuell ein `_cli_scheduler` Benutzer im TYPO3 Backend angelegt und mit entsprechenden Berechtigungen versehen werden. Ab TYPO3 8.6 und aktueller: Bitte löscht alle `_cli_*` Benutzer. 
{{% /notice %}}

## Installation überprüfen

Im Planer Modul wechselt ihr oben links auf `Konfiguration prüfen`. Hier seht ihr, wann der zugrundeliegende CronJob zum letzten Mal ausgeführt wurde. Ist die `Letzte Ausführung` orange hinterlegt, wurde das `scheduler:run` Kommando noch nie ausgeführt. Bitte prüft, ob der CronJob korrekt konfiguriert wurde oder startet das Kommando manuell.

## Der erste Test

Wechselt im "Planer" Modul zurück auf `Geplante Tasks`. Wählt oben links das `+` Icon. Im folgenden Formular setzt ihr folgende Optionen:

| Eigenschaft | Wert |Erklärung |
|-------------|------|----------|
| Deaktivieren | deaktiviert lassen | Wenn aktiv, wird die Aufgabe bei jedem CronJob Intervall nicht ausgeführt |
| Klasse | Erweiterungsliste aktualisieren | Hier findet ihr alle zur Verfügung stehenden Aufgaben, die unter anderem auch von anderen Extensions bereitgestellt werden können. Besonderes Augenmerk bitte auf `Konsolenbefehle ausführen`. Damit können Konsolenbefehle, die als `schedulable` deklariert wurden ausgeführt werden. |
| Typ | wiederkehrend | Soll die Aufgabe nur einmal oder wiederkehrend ausgeführt werden? |
| Taskgruppe | leer lassen | Um die Übersicht beizubehalten können Aufgaben Gruppen zugeordnet werden. Sogenannte `Planertaskgruppen` werden über das Listen-Modul auf der Root-Seite (Die Seite mit dem TYPO3-Logo) angelegt. |
| Start | 04:00 DatumVonMorgen | Wann soll die Aufgabe erstmalig ausgeführt werden? |
| Ende | leer lassen | Wann soll die Aufgabe das letzte Mal ausgeführt werden? Leer lassen, um die Aufgabe für alle Zeiten immer wieder auszuführen |
| Häufigkeit | 86400 | Angabe in Sekunden (86400 = 1 Tag) oder cron-Syntax. [crontab guru](https://crontab.guru/) |
| Parallele Ausführung erlauben | nein | Darf eine Aufgabe parallel zu einer anderen bereits laufenden Aufgabe ausgeführt werden? Ich empfehle hier `nein`. Falls ihr `ja` wählen solltet, bedenkt, dass eine Aufgabe an den CronJob Prozess gebunden ist. Heißt: Solange ihr nur einen CronJob für `scheduler:run` konfiguriert habt, kann auch immer nur eine Aufgabe ausgeführt werden. Ihr müsstet also schon mehrere CronJobs auf `scheduler:run` setzen, damit ein `ja` hier überhaupt eine Auswirkung hat. |
| Beschreibung | Aktualisieren der Erweiterungsliste, um über sicherheitsrelevante Updates informiert werden zu können | Gebt hier eine kurze Beschreibung an, die in der Aufgaben-Übersicht eine Aufgabe kurz erklären |

Nach `Speichern & Schließen` wird die Aufgabe in der Übersicht angezeigt. Ihr könnt nun warten bis der CronJob diese Aufgabe nächsten Morgen ausführt, oder aber ihr führt die Ausgabe direkt von der Übersicht aus.

{{% notice style="info" title="Hinweis" icon="info" %}}
Wenn ihr eine Aufgabe manuell über das "Planer" Modul anstößt und diese sehr lange dauert, dann wird diese Aufgabe zunächst im Rahmen der PHP Beschränkungen wie `max_execution_time` ausgeführt. Wenn dieses Maximum erreicht ist, dann erhaltet ihr im Modul eine weiße Seite und der Error wegen Überschreitung der `max_execution_time` wird im PHP Protokoll hinterlegt (sofern konfiguriert). Die Aufgabe läuft im Hintergrund jedoch weiter. Wenn ihr wisst, dass eine Aufgabe länger dauert, dann ladet einfach das "Planer" Modul neu. Manche Aufgaben bringen einen Prozess-Balken mit wie `EXT:events2` oder auch `EXT:solr` an dem ihr sehen könnt, wie weit die Aufgabe fortgeschritten ist.
{{% /notice %}}

## Weiterführende Links

Tim Lochmüller hat zu der Entstehungsgeschichte vom `scheduler` einen interessanten Blog-Artikel auf [typo3blogger](https://typo3blogger.de/vom-standalone-cli-zum-scheduler/) geschrieben.

Englische original [Dokumentation](https://docs.typo3.org/c/typo3/cms-scheduler/main/en-us/Index.html) zum TYPO3 `scheduler`
