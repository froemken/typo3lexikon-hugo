+++
title = "Was macht ein Hook?"
linkTitle = "Hooks"
date = 2024-01-09T20:49:38+01:00
alwaysopen = false
aliases = ["hooks.html"]
+++

Es gibt eine Erklärung auf Wikipedia.org. Aber ich will es mal mit eigenen Worten versuchen:

Ein Hook ist eine "Unterbrechung" an einer vorgegebenen Stelle im aktuellen Programmablauf, um dann einen Code einer völlig anderen Quelle einzubinden.

In Bezug auf TYPO3 bedeutet das, dass überall im PHP-Quellcode Hooks vorhanden sind, die Euch dabei helfen eigenen PHP-Quellcode an genau dieser Stelle zu implementieren bzw. einzufügen.

Ich sag mal ganz salopp dass es zwei Arten von Hooks gibt. Einmal die Hooks mit denen man bestimmte Funktionen um weitere Funktionalität erweitern kann und zum anderen Hooks mit denen Ihr Inhalte verändern könnt. Ersteres könnte z.B. ein weiteres ContentElement (zusätzlich zu Text, Text mit Bild, Formular, ...) sein mit dem Ihr z.B. einen Text erstellen könnt, der immer rot und fett ist. Zweiteres erhält von dem Hook ein Inhaltselement mit seinen Eigenschaften und Ihr könnt dieses Inhaltselement nun zusätzlich überprüfen oder die Ausgabe diesen Inhaltselementes weiter beeinflussen.

Wo macht ein Hook Sinn?
Ein Hook macht meist an solchen Stellen Sinn, an denen der Code erweitert werden soll bzw. ein vorhandener Inhalt nachträglich überarbeitet werden soll. Ich mach mal ein paar Beispiele zum besseren Verständnis:

In der Extension powermail gibt es derzeit 17 dokumentierte Hooks. Einer davon "unterbricht" die laufende Verarbeitung an der Stelle, an der ein Feld (wie Betreff, eMail, Name) HTML-mäßig generiert wird. Wir können nun diesen Hook nutzen und den Inhalt und/oder auch das Aussehen diesen Feldes beeinflussen (roter Rahmen, beliebigen Text über oder rechts vom Feld).

In der Extension tt_news gibt es vorgefertigte Hooks mit denen weitere Marker für das Template erstellt werden können.

Nicht nur Extensions, sondern auch der TYPO3-Code selbst beinhaltet massig Hooks, damit Ihr an vorgegebenen Stellen den PHP-Quellcore erweitern könnt. So gibt es dort Hooks, die die Daten vor der Speicherung eines Datensatzes nochmals überprüfen oder auch einen völlig anderen Inhalt in der Datenbank gespeichern können. Ja! Es gibt auch integrierte Hooks, um das Aussehen einzelner Felder im Backend zu verändern.

Wie finde ich Hooks?
Wenn Ihr Glück habt, dann sind Hooks in der Dokumentation beschrieben. Wenn Ihr Pech habt, dann ist nirgends etwas beschrieben und Ihr müsst Euch selbst auf die Suche machen. Das ist für Anfänger nicht leicht, denn TYPO3 ist kein 2000 Zeilen CMS.

Ich sag mal so: Der Sourcecode von TYPO3 ist manchmal besser dokumentiert als die Dokumentionen, die Ihr im Internet findet. So ist ein Hook sehr oft im Quellcode dokumentiert und Ihr könnt in den Dateien nach dem Wort "hook" suchen. Auf diese Weise kommen alle Hooks Stück für Stück zu Tage. Mit einer Software wie Scriptly von Webocton könnt Ihr auch Dateien eines komplettes Verzeichnisses und deren Unterverzeichnisse nach "hook" durchsuchen lassen. Nahezu alle Kommentare zu den jeweiligen Hooks beschreiben kurz was sie machen. Schaut Euch dazu mal die Datei class.t3lib_tceforms.php im Verzeichnis t3lib an:

Hier ein Hook, der es Euch ermöglicht eine Funktion auszuführen, die VOR der Erstellung eines Feldes im Backend noch etwas ausführt oder überprüft wie z.B. ob der Inhalt diesen Feldes numerisch ist oder einen timestamp in ein gewünschtes Datumformat konvertiert:

// Hook: getSingleField_preProcessforeach ($this->hookObjectsSingleField as $hookObj)    {    if (method_exists($hookObj,'getSingleField_preProcess'))    {        $hookObj->getSingleField_preProcess($table, $field, $row, $altName, $palette, $extra, $pal, $this);    }}
Hooks werden immer in diesen foreach-Konstellationen erstellt. Es wird also irgendein Array (hier $this->hookObjectsSingleField) durchlaufen und für jeden gefundenen Eintrag wird überprüft, ob sich in diesem Objekt "$hookObj" eine Funktion mit dem Namen "getSingleField_preProcess" befindet. Wenn dem so ist, dann wird die Funktion mit den ganzen Parametern, wie im Beispiel zu sehen ist aufgerufen. Ihr seht aber auch, dass diese Funktion nichts zurück gibt. Das bedeutet für uns, dass wir die Daten, die wir ändern wollen per Referenz in unserer Funktion angeben müssen:

function getSingleField_preProcess($table, $field, &$row, $altName, $palette, $extra, $pal, &pObj) { ...}
Hier hab ich noch einen Hook aus der Extension TemplaVoila. In diesem Fall wird nicht eine fest vorgegebene Funktion aufgerufen, sondern ein Objekt geladen:

// First prepare user defined objects (if any) for hooks which extend this function:$hookObjectsArr = array();if (is_array ($TYPO3_CONF_VARS['EXTCONF']['templavoila']['pi1']['renderElementClass'])) {    foreach ($TYPO3_CONF_VARS['EXTCONF']['templavoila']['pi1']['renderElementClass'] as $classRef) {        $hookObjectsArr[] = &t3lib_div::getUserObj($classRef);    }}
Und noch ein Hook aus der Extension TemplaVoila. Diese Form des Hooks ist meineserachtens die wohl üblichste Variante. Auch ist diese Art im Internet sehr verbreitet und auch in dem Buch von Dmitry Dulepov dokumentiert:

// Call hooksif (is_array($GLOBALS['TYPO3_CONF_VARS']['EXTCONF']['templavoila']['newcewizard']['forms'])) {    foreach($GLOBALS['TYPO3_CONF_VARS']['EXTCONF']['templavoila']['newcewizard']['forms'] as $userFunc) {        $params = array(            'pObj' => &$this,            'elements' => &$elements        );        t3lib_div::callUserFunction($userFunc, $params, $this);    }}  
Diese Form des Hooks gibt keine Funktionsnamen mehr vor, sondern Ihr könnt hier mit Euren eigenen Funktionsnamen arbeiten. In diesem Beispiel ist auch sehr schön zu sehen, wie mehrere Parameter an Eure Funktion übergeben werden können.

Durchsucht also mal die einzelnen Dateien und findet selbst heraus wo noch Hooks zu finden sind und welche Aufgabe sie für Euch ausführen.

Wie nutze ich einen Hook?
Egal welchen Hook Ihr gerade verwenden wollt, es gibt immer ein Array, das mit $TYPO3_CONF_VARS['SC_OPTIONS'] oder mit $TYPO3_CONF_VARS['EXTCONF'] anfängt. Die SC_OPTION-Variante findet Ihr in den Core-Dateien von TYPO3 und die EXTCONF-Variante ist für Hooks innerhalb von Extensions bestimmt. Evtl. werdet Ihr sagen, dass in meinem 1ten Beispiel von oben aber gar kein SC_OPTION zu finden ist. Da geb ich Euch grundsätzlich recht, aber sucht doch mal in der gleichen Datei nach dem Array "$this->hookObjectsSingleField" und Ihr werdet folgende Zeilen finden:

$this->hookObjectsSingleField = array();if (is_array ($TYPO3_CONF_VARS['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass']))    {    foreach ($TYPO3_CONF_VARS['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass'] as $classRef)    {        $this->hookObjectsSingleField[] = t3lib_div::getUserObj($classRef);    }}
und siehe da...da ist auch das SC_OPTION. Ihr werdet immer wieder über solche "Fallen" stolpern, von daher will ich Euch möglichst alle Varianten zeigen und Euch auf bestimmte Problemstellen hinweisen. Bei einigen Hooks muss sogar der nachfolgende Code analysiert werden, damit Euer Code auch sauber ausgeführt werden kann.

ext_localconf.php
Um einen Hook verwenden zu können ist es am Einfachsten eine eigene Extension zu erstellen.

<?phpif (!defined('TYPO3_MODE')) { die ('Access denied.');}$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_tceforms.php']['getSingleFieldClass'][] = 'EXT:sfmyext/hook/class.tx_sfmyext_preproc.php:tx_sfmyext_preproc';?>
Alles was links vom Gleichheitszeichen steht habt Ihr weiter oben schon mal gesehen...es ist der gleiche Code (blaue Farbe) mit einem abschließenden [] am Ende. Dieses abschließende [] steht dafür da, um dieses Array mit jedem Aufruf immer weiter füllen zu können. Damit können also auch andere Extensions diesen Hook verwenden und/oder er kann auch innerhalb Eurer Extension mehrfach aufgerufen werden. Vergesst diese [] bitte nicht, ansonsten kann dieser Hook insgesamt nur noch EINMAL verwendet werden. Eine Mehrfachverwendung wäre dann nicht mehr möglich.

Alles was rechts vom Gleichheitszeichen steht ist ein bisschen davon abhängig, wie der Hook aufgebaut ist: Hook mit vorgegebener Funktion, Hook mit Objektaufruf oder Hook mit selbstdefinierter Funktion. Besucht einfach meine Dokumentationen oben im Inhaltsverzeichnis, die Euch detailierte Informationen dazu zeigen.
