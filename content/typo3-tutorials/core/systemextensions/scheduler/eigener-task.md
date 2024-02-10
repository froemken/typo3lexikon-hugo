+++
title = "Eigenen Task erstellen"
linkTitle = "Eigener Task"
description = "Erstellung einer eigenen Aufgabe in TYPO3. Mittels Tasks können Extension Entwickler eigene Aufgabe in TYPO3 registrieren."
keywords = "typo3 stefan frömken lindlar oberberg oberbergischer kreis tutorial scheduler eigener task aufgabe cronjob"
date = 2024-02-10T01:13:47+01:00
aliases = ["eigene-tasks", "eigene-tasks.html"]
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

## Vorwort

Aufgaben, sogenannte Tasks, werden in TYPO3 in der Tabelle `tx_scheduler_task` abgelegt. Ganz übel dabei: Die PHP-Klasse, die für die Ausführung der Aufgabe zuständig ist, wird mittels `PHP:serialize()` auch mit in dieser Tabelle abgelegt. Spalte `serialized_task_object`. Dieses Vorgehen führt zu gewaltigen Problemen, wenn der Task später mal aktualisiert wird und weitere Objekte dem Task hinzugefügt werden. Ihr könnt den Task noch sooft erweitern, wie ihr wollt, es wird bei Ausführung der Aufgabe immer das deserialisierte Objekt aus der Tabelle `tx_scheduler_task` verwenden. Eure neu hinzugefügten Services, Referenzen und andere Objekte werden überhaupt nicht berücksichtigt. Die Aufgabe muss zunächst gelöscht und wieder neu angelegt werden.

Dieses Problem ist so omnipräsent, dass der TYPO3 Core in seiner Dokumentation [Creating a new task](https://docs.typo3.org/c/typo3/cms-scheduler/main/en-us/DevelopersGuide/CreatingTasks/Index.html) die Verwendung von CLI Kommandos bevorzugt, die als ausführbare Aufgabe deklariert wurden. In Bezug auf `scheduler` Aufgaben wird nur noch mitgeteilt, dass jegliche Logik in ausgelagerte Services zu überführen ist, um Referenzen in der Aufgabe auf ein absolutes Minimum zu reduzieren.

{{% notice style="info" title="Persönliche Anmerkung" icon="info" %}}
Ich habe noch nichts Offizielles dazu gelesen, aber ich glaube, dass die `scheduler` Extension in der aktuellen Fassung ausgedient hat und eine gewaltige Umstrukturierung dieser Extension in Planung ist. Es gibt schon [alternative Ansätze](https://github.com/helhum/typo3-crontab) von Helmut Hummel die `scheduler` Extension zu ersetzen. Daher rate auch ich dazu möglichst auf schedulable CLI Kommandos umzusteigen.
{{% /notice %}}

## Eigenen Task registrieren

Im Folgenden ein Beispiel zur Erstellung einer Aufgabe, um einen Server anzupingen.

Seit TYPO3 4.3 werden Aufgaben für die `scheduler` Extension in der `ext_localconf.php` registriert. Folgendes Beispiel funktioniert ab {{% badge style="green" icon="angle-double-up" %}}TYPO3 11.5{{% /badge %}}.

```php
<?php

if (!defined('TYPO3')) {
    die('Access denied.');
}

use StefanFroemken\SitePackage\Task\PingerTask;

$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['scheduler']['tasks'][PingerTask::class] = [
    'extension' => 'site_package',
    'title' => 'Ping Server',
    'description' => 'Hinterlegt eine IP-Adresse, die dieser Task mit jedem Intervall anpingen soll.',
];
```

Der Task wird immer mit seinem vollständigen Klassennamen registriert (siehe `::class` oben). Diese Klasse muss nun angelegt werden. Ich empfehle Aufgaben (Tasks) im Extension-Verzeichnis unter `Classes/Task/*` abzulegen. Erstellt dort die Datei `PingerTask.php` mit folgendem Inhalt:

```php
<?php

declare(strict_types=1);

/*
 * This file is part of the package stefanfroemken/site-package.
 *
 * For the full copyright and license information, please read the
 * LICENSE file that was distributed with this source code.
 */

namespace StefanFroemken\SitePackage\Task;

use TYPO3\CMS\Scheduler\Task\AbstractTask;

/**
 * Scheduler task to ping a server
 */
class PingerTask extends AbstractTask
{
    public function execute(): bool
    {
        $time = $this->ping('typo3lexikon.de', 15);

        return $time !== 0.0;
    }

    /**
     * @return float Return time until answer in seconds
     */
    private function ping(string $ip, int $timeout = 10): float
    {
        $start = microtime(true);
        $file  = @fsockopen($ip, 80, $errno, $errstr, $timeout);
        $stop  = microtime(true);

        if (!$file) {
            // Site is offline
            return 0.0;
        }

        fclose($file);

        return round(($stop - $start) * 1000);
    }
}
```

Jeder Task muss von der abstrakten Klasse `AbstractTask` erben. Ohne diese Klasse kann die benötigte `execute()` Methode nicht aufgerufen werden. Die `execute()` Methode erlaubt einen booleschen Wert als Rückgabe. Gebt `false` zurück und der Task wird in der Aufgabenübersicht entsprechend als fehlerhaft markiert.

Das @-Zeichen vor `PHP:fsockopen()` ist dafür da, dass ihr nicht die PHP-Warnungen seht, wenn ihr den Task per Hand ausführt und der Server nicht erreichbar sein sollte.

Wechselt nun ins Installtool und wählt unter `Wartung` den Punkt `Flush Caches`. Der Task kann nun über das Planer Modul hinzugefügt, konfiguriert und auch manuell ausgeführt werden.

## Dynamische Werte

Anstatt mit fest vorgegebenen Werten im `PingerTask` zu arbeiten, sollten diese Werte besser konfigurierbar gemacht werden. Dazu bietet TYPO3 bei der Registrierung des Tasks in der `ext_localconf.php` eine weitere Option `additionalFields` an, um eine weitere PHP Klasse für die Erstellung und Verwaltung von zusätzlichen Feldern zu hinterlegen:

```php
<?php

if (!defined('TYPO3')) {
    die('Access denied.');
}

use StefanFroemken\SitePackage\Task\PingerTask;
use StefanFroemken\SitePackage\Task\PingerTaskAdditionalFieldProvider;

$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['scheduler']['tasks'][PingerTask::class] = [
    'extension' => 'site_package',
    'title' => 'Ping Server',
    'description' => 'Hinterlegt eine IP-Adresse, die dieser Task mit jedem Intervall anpingen soll.',
    'additionalFields' => PingerTaskAdditionalFieldProvider::class,
];
```

Diese weitere PHP Klasse `PingerTaskAdditionalFieldProvider` muss alle Methoden aus dem `AdditionalFieldProviderInterface` implementieren, um die Felder für die Benutzereingaben zu erstellen, zu überprüfen und die Werte an unseren Task zu überführen. In unserem Fall erben wir jedoch von der Klasse `AbstractAdditionalFieldProvider`, da diese zusätzlich eine Methode zur Ausgabe von Fehlermeldungen mitbringt.

```php
<?php

declare(strict_types=1);

/*
 * This file is part of the package stefanfroemken/site-package.
 *
 * For the full copyright and license information, please read the
 * LICENSE file that was distributed with this source code.
 */

namespace StefanFroemken\SitePackage\Task;

use TYPO3\CMS\Core\Type\ContextualFeedbackSeverity;
use TYPO3\CMS\Core\Utility\GeneralUtility;
use TYPO3\CMS\Core\Utility\MathUtility;
use TYPO3\CMS\Scheduler\AbstractAdditionalFieldProvider;
use TYPO3\CMS\Scheduler\Controller\SchedulerModuleController;
use TYPO3\CMS\Scheduler\Task\AbstractTask;
use TYPO3\CMS\Scheduler\Task\Enumeration\Action;

/**
 * Scheduler task to ping a server
 */
class PingerTaskAdditionalFieldProvider extends AbstractAdditionalFieldProvider
{
    /**
     * @param PingerTask|null $task
     */
    public function getAdditionalFields(array &$taskInfo, $task, SchedulerModuleController $schedulerModule): array
    {
        $additionalFields = [];
        $fieldHtml = '<input class="form-control form-control-clearable t3js-clearable" type="text" name="tx_scheduler[%s]" id="%s" value="%s" size="30" />';
        $currentSchedulerModuleAction = $schedulerModule->getCurrentAction();

        if (!isset($taskInfo['scheduler_pinger_ip'])) {
            $taskInfo['scheduler_pinger_ip'] = '';
            if ($currentSchedulerModuleAction->equals(Action::EDIT)) {
                $taskInfo['scheduler_pinger_ip'] = $task->ip;
            }
        }

        if (!isset($taskInfo['scheduler_pinger_port'])) {
            $taskInfo['scheduler_pinger_port'] = 80;
            if ($currentSchedulerModuleAction->equals(Action::EDIT)) {
                $taskInfo['scheduler_pinger_port'] = $task->port;
            }
        }

        // Write the code for the field "ip"
        $fieldId = 'task_ip';
        $additionalFields[$fieldId] = [
            'code'     => sprintf($fieldHtml, 'scheduler_pinger_ip', $fieldId, $taskInfo['scheduler_pinger_ip']),
            'label'    => 'IP-Adresse/Webseite'
        ];

        // Write the code for the field "port"
        $fieldId = 'task_port';
        $additionalFields[$fieldId] = [
            'code'     => sprintf($fieldHtml, 'scheduler_pinger_port', $fieldId, $taskInfo['scheduler_pinger_port']),
            'label'    => 'Port'
        ];

        return $additionalFields;
    }

    public function validateAdditionalFields(array &$submittedData, SchedulerModuleController $schedulerModule): bool
    {
        $result = true;

        if (!GeneralUtility::validIP($submittedData['scheduler_pinger_ip'])) {
            $result = false;
            $this->addMessage('Please enter a valid IP address', ContextualFeedbackSeverity::ERROR);
        }

        if (!MathUtility::canBeInterpretedAsInteger($submittedData['scheduler_pinger_port'])) {
            $result = false;
            $this->addMessage('Port must be integer', ContextualFeedbackSeverity::ERROR);
        }

        return $result;
    }

    /**
     * @param PingerTask|AbstractTask $task
     */
    public function saveAdditionalFields(array $submittedData, AbstractTask $task): void
    {
        $task->ip = (string)$submittedData['scheduler_pinger_ip'];
        $task->port = (int)$submittedData['scheduler_pinger_port'];
    }
}

```

### getAdditionalFields

Im oberen Bereich setzen wir Standardwerte für unsere neuen Optionen. Wenn wir uns im Bearbeiten-Modus befinden werden unsere Optionen mit den Werten aus der Tabelle `tx_scheduler_task` wieder befüllt.
Im unteren Bereich wird das HTML Grundgerüst für die Optionen gebaut. Hier gibt es kein Fluid oder andere Vorgaben. Ihr seid hier auch euch allein gestellt. Ich empfehle euch die TYPO3 eigenen AdditionalFieldProvider für Beispiele anzuschauen.

### validateAdditionalFields

Bei Bedarf könnt ihr hier die vom Benutzer eingetragenen Werte überprüfen lassen. Sofern ihr vom `AbstractAdditionalFieldProvider` erbt, könnt ihr bei Fehlern direkt eine Ausgabe für die Aufgabe im Planer Modul erstellen lassen.

### saveAdditionalFields

Hier werden die Benutzereingaben an den Task übergeben und somit auch in der Tabelle `tx_scheduler_task` in serialisierter Form abgelegt. Evtl. Typ-Anpassungen (cast) sollten hier vorgenommen werden

## PingerTask anpassen

Die beiden neuen Optionen müssen in unserem `PingerTask` hinzugefügt werden

```php
<?php

declare(strict_types=1);

/*
 * This file is part of the package stefanfroemken/site-package.
 *
 * For the full copyright and license information, please read the
 * LICENSE file that was distributed with this source code.
 */

namespace StefanFroemken\SitePackage\Task;

use TYPO3\CMS\Scheduler\Task\AbstractTask;

/**
 * Scheduler task to ping a server
 */
class PingerTask extends AbstractTask
{
    public string $ip = '';

    public int $port = 80;

    public function execute(): bool
    {
        $time = $this->ping($this->ip, $this->port);

        return $time !== 0.0;
    }

    /**
     * @return float Return time until answer in seconds
     */
    private function ping(string $ip, int $port): float
    {
        $start = microtime(true);
        $file  = @fsockopen($ip, $port, $errno, $errstr, 10);
        $stop  = microtime(true);

        if (!$file) {
            // Site is offline
            return 0.0;
        }

        fclose($file);

        return round(($stop - $start) * 1000);
    }
}
```

Nach diesem Update kann kein Hostname mehr für den Pinger verwendet werden. Es muss nun eine gültige IPv4 oder IPv6 Adresse angegeben werden. Den Timeout habe ich auf 10 Sekunden festgesetzt, dafür ist nun der Port "anpingbar". Ja, sorry, eigentlich haben wir hier jetzt eher einen Port-Checker gebaut ;-)
