+++
title = "TYPO3 Console. Das neue CLI"
linkTitle = "Console"
date = 2024-01-06T23:16:58+01:00
slug = "console"
weight = 3
+++

TYPO3 ist nicht nur über das Frontend oder dem Backend erreichbar, nein, es bietet auch
die Möglichkeit bestimmte Aufgaben mithilfe der Konsole, auch Command Line Interface 
kurz CLI genannt, zu ermöglichen. Auf diese Weise können bestimmte Aufgaben automatisiert
werden, ohne sich erst im Backend anmelden zu müssen. Zum Beispiel könnte auf diese Weise
ein Export initiiert werden oder das Backend gesperrt werden. Wenn Euch ein Cronjob zur
Verfügung steht, könntet Ihr auch jeden Tag oder alle paar Wochen mal den ReferenzIndex
von TYPO3 aktualisieren.

Vor TYPO3 8.0 mussten diese Commands über `typo3/cli_dispatch.phpsh` gestartet werden.
Jedoch ist die zugrunde liegende Programmierung etwas in die Jahre gekommen, weshalb man
sich für die Implementation der Symfony Console entschieden hat. Durch die Verwendung
bereits bestehender etablierter OpenSource Software wie Symfony, müssen die TYPO3 Entwickler
das Rad nicht neu erfinden und können sich wieder wichtigeren Themen widmen.

Die Schnittstelle zwischen TYPO3 und der Symfony Console besteht aus gerade mal 4 Dateien
und mit keiner dieser Dateien kommt Ihr in Berührung. Die wohl wichtigste Datei ist der
`CommandRequestHandler.php`. Dieser durchsucht alle installierten Extension nach folgender
Datei

`typo3conf/ext/[ExtKey]/Configuration/Commands.php`

Wenn diese Datei existiert werden alle enthaltenen PHP Klassen als Kommandos registriert
und können über die CLI ausgeführt werden.

## Eine erste kleine Ausgabe

Die `Commands.php` liefert ein oder mehrere Array-Elemente zurück bei denen der Array Key
den zu verwendenden Shellbefehl widerspiegelt, in diesem Falle `print:something`, und der
class Part die PHP Klasse, die für die Ausführung des Befehls zuständig sein soll.

```php
<?php
return [
    'print:something' => [
        'class' => \SF\Sfcategory\Command\PrintCommand::class
    ]
];
```

Erstellt nun die dazugehörige `PrintCommand.php` Datei im `Classes/Command` Verzeichnis.
Alle Commands müssen eine execute Methode aufweisen. Mit dem ersten Parameter $input 
könnt Ihr auf Eingaben aus der Shell reagieren. Mit dem zweiten Parameter $output könnt 
Ihr Ausgaben auf der Shell produzieren.

```php
<?php
namespace SF\Sfcategory\Command;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

class PrintCommand extends Command
{
    /**
     * Configure the command by defining the name, options and arguments
     */
    public function configure()
    {
        $this->setDescription('A description while listing all commands');
        $this->setHelp('A detailed description, if your command was prefixed with "help"');
    }

    /**
     * Executes the current command.
     *
     * @param InputInterface  $input  An InputInterface instance
     * @param OutputInterface $output An OutputInterface instance
     *
     * @return null|int null or 0 if everything went fine, or an error code
     */
    protected function execute(InputInterface $input, OutputInterface $output)
    {
        $words = array('Hello', 'How are you?', 'Nice to meet you');
        $output->writeln($words[array_rand($words, 1)]);
        
        return 0; // everything fine
    }
}
```

Als Einstiegspunkt bietet TYPO3 8.0 in der composer-basierten Version einen Symlink auf die
neue typo3 Datei im bin Verzeichnis an. Bei nicht Composer basierten Versionen müsst Ihr
das Original aufrufen:

```shell
php typo3/sysext/core/bin/typo3 list
```

Mit diesem Kommando könnt Ihr Euch alle zur Verfügung stehenden Befehle anzeigen lassen.
Nach einem Clear System Cache, sollte auch der obere `print:something` Command dabei sein.
Mit `help` könnt Ihr Euch alle Argumente für einen spezifischen Command anzeigen lassen:

```shell
php typo3/sysext/core/bin/typo3 help print:something
```

Führt nun den `print` Command aus:

```shell
php typo3/sysext/core/bin/typo3 print:something
```

Die Ausgabe zeigt nun einen der drei Werte der PrintCommand Datei an.

## Spaß mit Tabellen auf der Console

```php
<?php
namespace SF\Sfcategory\Command;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Helper\Table;
use Symfony\Component\Console\Helper\TableSeparator;
use Symfony\Component\Console\Helper\TableStyle;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

class PrintCommand extends Command
{
    protected function execute(InputInterface $input, OutputInterface $output)
    {
        $style = new TableStyle();
        $style->setHorizontalBorderChar('<fg=blue>-</>');
        $style->setVerticalBorderChar('<bg=yellow>|</>');
        
        $table = new Table($output);
        // set special style
        $table->setStyle($style);
        // add table header
        $table->setHeaders(array('uid', 'title', 'price'));
        // add some rows
        $table->addRows(array(
            array(1, 'Lego', '23.56'),
            array(2, 'table', '135.00'),
            new TableSeparator(),
            array(3, 'chair', '35.62')
        ));
        // add one row
        $table->addRow(array(4, 'biro', '5.12'));
        // set fixed width of each column
        $table->setColumnWidths(array(3, 15, 6));
        $table->render();
    }
}
```

## Jetzt wird Dir der Prozess gemacht

```php
<?php
namespace SF\Sfcategory\Command;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Helper\ProgressBar;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

class PrintCommand extends Command
{
    protected function execute(InputInterface $input, OutputInterface $output)
    {
        // create a new progress bar
        $progress = new ProgressBar($output, 10);
    
        // start and displays the progress bar
        $progress->start();
    
        for ($i = 0; $i < 10; $i++) {
            // do something which needs some time
            sleep(1);
        
            // move progressbar one step
            $progress->advance();
        }
    
        // set progressbar to 100%
        $progress->finish();
    }
}
```

## Eingaben als Argument verarbeiten

```php
<?php
namespace SF\Sfcategory\Command;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

class PrintCommand extends Command
{
    protected function configure()
    {
        $this->addArgument('first-name', InputArgument::REQUIRED, 'What is your first name?');
        $this->addArgument('last-name', InputArgument::REQUIRED, 'Tell me your last name');
    }
    
    protected function execute(InputInterface $input, OutputInterface $output)
    {
        $output->writeln(sprintf(
            'Nice to see you: %s %s',
            $input->getArgument('first-name'),
            $input->getArgument('last-name')
        ));
    }
}
```

## Frag die Konsole

```php
<?php
namespace SF\Sfcategory\Command;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Helper\QuestionHelper;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;
use Symfony\Component\Console\Question\Question;
use TYPO3\CMS\Core\Utility\MathUtility;

class PrintCommand extends Command
{
    protected function execute(InputInterface $input, OutputInterface $output)
    {
        /** @var QuestionHelper $helper */
        $helper = $this->getHelper('question');
        $question = new Question(
            'What can I calculate for you? ',
            '23 * 12'
        );
        $output->writeln(
            MathUtility::calculateWithParentheses(
                $helper->ask($input, $output, $question)
            )
        );
    }
}
```
