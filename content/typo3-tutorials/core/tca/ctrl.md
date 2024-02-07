+++
title = "Ctrl"
date = 2024-01-09T20:41:21+01:00
aliases = ["ctrl.html"]
+++

Dieser Bereich kümmert sich darum wie die angegebene Tabelle im Backend aussieht, welchen Titel sie trägt, ob nur Admins daraus Zugriff haben und und und.

## Die erste Berührung mit der TCA

Öffnet die `extTables.php` im typo3conf-Verzeichnis und fügt ganz unten mal folgende Zeile ein:

```php
$GLOBALS['TCA']['company']['title'] = 'Meine Firmen';
```

Falls Ihr im TYPO3-Installtool das Debugging für das Backend aktiviert und auch fehlgeschlagene SQL-Anweisungen anzeigen lasst, dann könnte es Euch nun passieren, das solche Fehlermeldungen auftauchen, wenn Ihr Euch im Listenmodul mal ein bisschen hin und her bewegt:

```
Table 'typo3.company' doesn't exist
```

```sql
SELECT COUNT(*) FROM company WHERE pid=2
```

Ihr seht: Allein durch die Angabe von "company" in der TCA sucht TYPO3 förmlich nach einer Tabelle mit dem gleichen Namen in der Datenbank und meckert nun, dass es keine Tabelle gibt. Legen wir mal mit Hilfe von t3adminer/phpMyAdmin oder wie hier in der MySQL-Konsole mal eine Tabelle mit diesem Namen an und und erstellen auch gleich mal ein neues Feld für den Firmennamen:

```sql
CREATE TABLE `typo3`.`company` (
`title` VARCHAR (255) NOT NULL
) ENGINE = INNODB;
```

Sofort werden wir wieder mit einer Fehlermeldung erschlagen, mit dem Hinweis, dass es keine Tabellenspalte "pid" gibt. Ich breche das jetzt hier mal ab, weil ich denke, dass Ihr verstanden habt, dass die TCA auch ein gewisses Kontrollorgan mitbringt. Warum sich die Mühe machen, die Tabellen händisch anzulegen, wenn TYPO3 mit Hilfe des kickstarters die Tabellen automatisch erstellen kann.

In den nun folgenden Dokumentationen gehe ich also davon aus, dass Ihr mit dem kickstarter einige Tabellen angelegt habt und nun wissen wollt, wofür die einzelnen Einträge in der TCA eigentlich sind.

## Title

Vergib Deiner Tabelle in TYPO3 einen Titel. Dabei muss es sich nicht um den Tabellennamen in der Datenbank handeln. So heißt die Inhaltstabelle in der TYPO3-Datenbank zum Beispiel tt_content, aber im TYPO3-Backend steht der Titel "Page Content" für die englische Sprache bzw. "Seiteninhalt" für die deutsche Sprache

```php
$TCA['tx_sftca_company'] = array (
    'ctrl' => array (
        'title' => 'Meine Firmen'
    ),
);
```

oder auch mit einer Verbindung zu einer Sprachdatei:

```php
$TCA['tx_sftca_company'] = array (
    'ctrl' => array (
    'title' => 'LLL:EXT:sftca/locallang_db.xml:tx_sftca_company'
    ),
);
```

## label

In dieses Feld muss der Tabellenspaltenname eingetragen werden, der später im Listenmodul zu sehen sein soll. So wird bei Webseitenbenutzern z.B. immer die Spalte username in der ersten Spalte angezeigt. Bei pages die Spalte title und bei Inhaltselementen die Spalte header. Diese Spalte ist nach Setzen dieser Option immer zu sehen und immer in der ersten Spalte. Gerade in Bezug auf spätere Selektboxen wird dieser Wert wichtig, da die uid-Spalte vom kickstarter als Defaultwert genommen wird und Ihr dann nur Zahlen in Eurer Selektbox habt.

Dieses Feld ist für jede Tabelle in der TCA zwingend erforderlich. Ohne Angabe diesen Feldes, werden all Eure Felder nahezu wahllos irgendeiner anderen Tabelle im Listenmodul zugeordnet und der evtl. angegebene Titel überschreibt die Titel anderer Tabellen. Also bitte...immer setzen!!!

```php
$TCA['tx_sftca_company'] = array (
    'ctrl' => array (
        'title' => 'Meine Firmen',
        'label' => 'title'
    ),
);
```

## label_alt

Manchmal kann es vorkommen, dass die Spalte, die wie unter label definiert haben einen leeren Datensatz enthält. In meinem Beispiel habe ich gesagt, dass meine Hauptspalte der title sein soll. Wenn aber einer unserer Datensätze keinen Titel hat, dann erscheint [kein Titel] oder [no title] in unserer Auflistung. Mit label_alt kann man dem nun vorbeugen und eine andere Spalte vorschlagen, die dann in der Hauptspalte angezeigt werden soll, wenn unsere Hauptspalte einen leeren Wert enthält:

```php
$TCA['tx_sftca_company'] = array (
    'ctrl' => array (
        'title' => 'Meine Firmen',
        'label' => 'title',
        'label_alt' => 'zip,city',
    ),
);
```

Wie Ihr seht, kann man auch mehrere alternative Tabellenspalten angeben. Kurz: Ist unsere Spalte title leer, dann wird die Spalte zip angezeigt. Ist zip auch leer, dann wird city angezeigt. Sind alle Spalten leer, dann wird [Kein Titel] angezeigt.

## label_alt_force

Wenn diese Eigenschaft gesetzt wird, dann werden die Inhalte der in label_alt definierten Spaltennamen kommasepariert hinter den Inhalt der Hauptspalte gesetzt. Bei mir schaut's bei drei Datensätze derzeit so aus:

Maier, 51688, Wipperfürth
51515, Nümbrecht
Lüdenscheid

Leere Felder werden einfach weggelassen. Hier die passende TCA dazu:

```php
$TCA['tx_sftca_company'] = array (
    'ctrl' => array (
        'title' => 'Meine Firmen',
        'label' => 'title',
        'label_alt' => 'zip,city',
        'label_alt_force' => TRUE,
    ),
);
```

## label_userFunc

Wenn Ihr Euch einen völlig einzigartigen Inhalt für Eure Hauptspalte wünscht, dann seid Ihr hiermit genau richtig. Erstellt Euch eine UserFunc und gestaltet Euren eigenen individuellen Titel mit den Inhalten des aktuellen Datensatzes.

Ich habe festgestellt, dass Ihr innerhalb der UserFunc immer nur die Felder des aktuellen Datensatz zur Verfügung habt, die zuvor in label und label_alt definiert wurden. Falls Euch also Spalten fehlen, dann macht keine neue Datenbankabfrage innerhalb der UserFunc, sondern fügt die benötigten Spalten einfach bei label_alt noch hinten dran.

So eine UserFunc macht zum Beispiel Sinn, wenn Ihr Datumswerte anzeigen lassen wollt. Per Default werden diese Zeitstempel inkl. Uhrzeit dargestellt. Bei einer Tabelle mit Geburtstagskindern macht es aber evtl. gar keinen Sinn auch eine Uhrzeit mit in der Hauptspalte zu haben. Diese könntet Ihr dann mit einer UserFunc also entfernen.

## type

Die type-Eigenschaft ist ein sehr mächtiges Werkzeug. Es kann die Spalten einer Tabelle kategorisieren. Ihr kennt die type-Eigenschaft, da bin ich mir sicher. Legt mal ein Inhaltselement vom Typ Text an und speichert ab. Nun wechselt Ihr innerhalb des Inhaltselementes von Text auf Bild oder auf irgendeinen anderen Inhaltstypen. Sehr Ihr...da kommt eine Warnmeldung mit dem Hinweis, dass sich nun das Aussehen des Inhaltselementes verändern könnte. Nach dem Bestätigen seht Ihr nun Eingabe felder, die speziell für das Inhaltselement Bild benötigt werden.

Damit TYPO3 weiß, welche Spalten bei welchem gewählten Typen angezeigt werden sollen, gibt es später noch den Abschnitt types. Da ich meine Firmen nicht nach Typen ketegorisieren will gibt's hier ein abgespecktes Beispiel aus der tt_content:

```php
$TCA['tt_content'] = array (
    'ctrl' => array (
        'title' => 'Seiteninhalt',
        'label' => 'header',
        'type' => 'record_type',
    ),
    'types' => array(
        '0' => array('showitem' => 'hidden, record_type, title, some_date '),
        '1' => array('showitem' => 'record_type, title '),
        '2' => array('showitem' => 'title, some_date, hidden, record_type '),
    ),
);
```

Der record_type wird als Selektbox dargestellt und speichert nach dem Speichern einen Zahlenwert in der Datenbank ab. In unserem Beispiel wird es ein Zahlenwert zwischen 0 und 2 sein, weil wir auch nur Typen mit einem Zahlenwert von 0-2 definiert haben. Wird nun z.B. der Inhaltstyp mit der ID 1 gewählt, wird wieder unsere Selektbox und zusätzlich noch die Titelspalte angezeigt.

PS: Die Reihenfolge hier spiegelt auch die Reihenfolge der Felder im Backend wieder.

## hideTable

Das ist mal eine sinnvolle Eigenschaft. Mit Ihr könnt Ihr Tabellen für das Backend unsichtbar machen. Das macht natürlich nicht für jede Tabelle Sinn, aber nerven Euch nicht auch diese 6 oder 7 Tabellen der static_info_tables-Extension auf der Rootseite?

Lasst sie doch einfach verschwinden. Geht in die ext_tables.php den typo3conf-Verzeichnisses und fügt mal diese Zeile ein:

```php
$GLOBALS['TCA']['static_territories']['ctrl']['hideTable'] = TRUE;
```

und schon ist die erste Tabelle nach einem clear Cache futsch.

## requestUpdate

Die Funktionsweise ist dieselbe wie der von der Eigenschaft type. Beide Eigenschaften initializieren einen Reload des aktuellen Formulars. Wofür jetzt aber noch ein zweiter Reload? Ich versuche mal ein Beispiel zu bringen:

Über das Feld type können wir verschiedene Fahrzeugtypen wie LKW, Auto, Schiff und Flugzeug wählen. Individuell für jeden Fahrzeugtyp definieren wir die benötigten Spalten im types Abschnitt (siehe Eigenschaft type).

Im Bereich Auto haben wir nun z.B. ein weiteres Selektfeld "Marke". Je nach Marke können wiederum andere Felder erscheinen oder verschwinden. So gibt es z.B. Kühlerfiguren nur beim Mercedes und Maße für das Reserverad gibt es beim KIA nicht, da hier nur ein Notfallset vorliegt. Egal, ob die Felder innerhalb der Marke gebraucht werden oder nicht, sie alle müssen zumindest für die Kategorie Auto aktiviert werden mit Hilfe der schon oben genannten types-Eigenschaft.

Wie aber blende ich ein Feld ein oder aus, wenn nicht über die types-Eigenschaft? Dies geschieht mit Hilfe der Eigenschaft displayCond, die wir später noch im Abschnitt "columns" näher erläutern werden.

## iconfile

Hier könnt Ihr ein kleines Icon für Eure Tabelle hinterlegen. Dieses wird innerhalb der Tabellen vor jedem Datensatz angezeigt.

Was ich bisher auch nicht wusste ist, dass es 4 verschiedene Möglichkeiten gibt ein Bild einzupflegen

Enthält der Pfad einen Slash (/), dann muss es sich um einen relativen Pfad handeln, der sich relativ zum Verzeichnis /typo3 bewegen muss. Wenn Du also auf ein Icon im fileadmin-Verzeichnis verweisen willst, dann muss der relative Pfad ../fileadmin/ lauten.

Wenn nur ein Dateiname angegeben wird (ohne Pfadangabe), dann muss sich dieses Bild in folgendem Pfad befinden typo3/gfx/i/
Wird kein Pfad angegeben, dann wird versucht das Bild typo3/gfx/i/[Tabellenname].gif zu laden. Da der TYPO3-Quellcode allerdings nicht verändert werden sollte ist bei eigenen Tabellen davon abzuraten seine eigenen Icons in dieses Verzeichnis abzulagern.
Für Icons, die sich innerhalb von Extensions befinden, hat sich folgendes Vorgehen bewährt:

```php
$TCA['tx_sftca_company'] = array (
    'ctrl' => array (
        'title'     => 'Meine Firmen',
        'label' => 'title',
        'iconfile' => t3lib_extMgm::extRelPath($_EXTKEY) . 'icon_tx_sftca_company.gif',
    ),
);
```

## typeicon_column

Üblicherweise wird hier DIE Spalte angegeben, die auch bei der Eigenschaft type angegeben wurde. Aber von der Funktionalität her kann hier auch eine völlig andere Spalte verwendet werden. Nur Sinn machen tut es halt bei der type-Spalte.

Normalerweise erscheint vor jeder Zeile in der Tabelle ein kleines Icon, das wir schon weiter oben mit iconfile deklariert haben. In einem Beispiel mit Automarken wäre es evtl. wünschenswert, wenn ich direkt in der Tabelle an Hand der Icons schon sehen könnte, dass es sich bei bestimmten Datensätzen um Audis, BMWs oder KIAs handelt. Die Spalte, die die Marken differenziert, dieser Spaltenname muss hier in dieser Eigenschaft hinterlegt werden

Welches Bild dann angezeigt werden soll wird mit dernächsten Eigenschaft erledigt.

## typeicon_classes

Diese Eigenschaft hat mich zwei Stunden gekostet. Das Problem war, dass die TYPO3-Doku zur Zeit noch für die 4.5 gilt...ich aber schon mit der 4.6er arbeite. Wärend bei der 4.5er diese Eigenschaft noch typeicons heißt, lautet sie nun typeicon_classes. Muss man erst mal wissen.

Der Hintergedanke dazu ist, vermehrt mit Sprites zu arbeiten. Das ist EIN Bild auf dem mehrere kleinere Bilder zu sehen sind. Nun kann man mit Hilfe von CSS einen kleines Ausschnitt von dem Sprite anzeigen lassen und mit Hilfe von Positionierungen zwischen den vielen kleinen Bildern hin und her wechseln. Hat den Vorteil, dass nur ein Bild geladen werden muss und nicht gefühlte 200. Das entlastet den Traffik und die Webseite ist schneller aufgebaut.

Hier nun also ein Beispiel, wie die TCA ausschauen könnte:

```php
$TCA['tx_sftca_company'] = array (
    'ctrl' => array (
        'title'     => 'LLL:EXT:sftca/locallang_db.xml:tx_sftca_company',    
        'label'     => 'uid',  
        'iconfile'          => t3lib_extMgm::extRelPath($_EXTKEY).'icon_tx_sftca_company.gif',
        'typeicon_column' => 'title',
        'typeicon_classes' => array(
            'maier' => 'mimetypes-x-content-header',
            'mueller' => 'mimetypes-x-content-text-picture',
        ),
    ),
);
```

In diesem Beispiel habe ich mal keine type-Eigenschaft gesetzt und die typeicon_column auf die Spalte title gesetzt. Wenn nun jemand in diese Spalte das Wort "maier" reinschreibt, dann wird dieser eine Datensatz in der Tabelle mit dem Icon der Seiteninhaltsüberschriften versehen. Und nach Eingabe von "mueller" das Icon der Bild-Elemente.

Eine Datei oder Pfad kann nicht mehr angegeben werden. Schaut Euch bitte mal die Datei typo3/sysext/t3skin/stylesheets/sprites/t3skin.css an. Dort könnt Ihr sehen wie Sprites eingebunden werden und auch aussehen.

In der neuen Sprite-API gibt es noch 2 weitere Möglichkeiten Spriteklassen einzubinden:

```php
'typeicon_classes' => array(
    'mask' => 'hallo-###TYPE###-zusammen',
),
```

Mit dieser Möglichkeit könnt Ihr nun dynamisch Klassen zusammenbauen lassen. Im Beispiel unserer Autos würde die Klasse nun hallo-audi-zusammen heißen. Damit hier kein Mist passiert, müssen alle verwendeten Klassen global registriert werden. Das könnt Ihr in der ext_tables.php machen:

```php
$GLOBALS['TBE_STYLES']['spritemanager']['spriteIconsAvailable'][] = 'hallo-audi-zusammen';
$GLOBALS['TBE_STYLES']['spritemanager']['spriteIconsAvailable'][] = 'hallo-vw-zusammen';
```

Die zweite Variante funktioniert über eine userFunc:

```php
'typeicon_classes' => array(
    'userFunc' => 'MeinFunktionsname',
),
```

Weitere Informationen dazu findet Ihr in der Datei class.t3lib_iconworks.php.

## thumbnail

Mit thumbnail könnt Ihr eine Spalte angeben, die Bilder abspeichert. Diese Bilder werden dann als kleine Vorschaubilder (thumbs) in der Tabelle pro Datensatz mit angezeigt. Damit das funktioniert müsst Ihr in Euren Benutzereinstellungen die Anzeige von thumbs erlauben.

## selicon_field

Stellen wir uns eine Tabelle Auto und eine Tabelle Marken vor. In der Tabelle Marken haben wir ein Feld für den Markennamen und ein Feld, um ein Bild/Logo für diese Marke zu hinterlegen. Mit der Eigenschaft thumbnail kann ich dieses Logo nur im Listenmodul anzeigen lassen. Mit selicon_field kann ich dieses Bild in Originalgröße überall dort anzeigen lassen, wo auf diese Markentabelle zugegriffen wird. So z.B. in der Autotabelle. Hier gibt es z.B. eine Selektbox in der ich die Marken auswählen kann. Unter der Selektbox erscheinen nun alle Bilder aus der Markentabelle.

## selicon_field_path

Siehe auch selicon_field. Gebt hier den gleichen Pfad an, den Ihr auch bei selicon_field für die Eigenschaft upload_pfad angegeben habt.

## sortby

Wenn Ihr die Daten Eurer Tabelle sortieren wollt, dann erstellt dafür ein weiteres Feld in Eurer Tabelle vom Typ Integer und gebt dieses Feld hier an. Danach erhaltet Ihr im Listenmodul nach Aktivierung der erweiterten Ansicht für jeden Datensatz zwei Pfeile mit denen Ihr Euren Datensatz entweder einen Schritt nach oben oder nach unten bewegen könnt. Drag and Drop ist nicht implementiert.

## default_sortby

Ich hatte mich auch gewundert. Warum gibt es sortby und dann auch noch default_sortby? default_sortby hat keine Auswirkung, wenn die Eigenschaft sortby gesetzt ist. Der Unterschied ist, dass Ihr bei default_sortby kompletten Zugriff auf die MySQL ORDER BY Klause habt. So könnt Ihr zum Beispiel erst nach Wohnort, dann nach Nachname und dann nach Vorname sortieren:

```php
'default_sortby' => 'ORDER BY city, last_name, first_name',
```

## mainpalette

Mit dieser Eigenschaft können noch nicht gerenderte Paletten an das Ende des Eingabeformulars gesetzt werden. Normalerweise erscheinen Paletten nur, wenn man auf das kleine Paletten-Icon klickt oder ganz unten die Checkbox "2te Optionspalette anzeigen" markiert. Ich habe lange versucht auf bereits gerenderte Paletten zuzugreifen, aber leider ohne Erfolg. Erst mit Durchwühlen des Quellcodes stellte sich heraus, dass die angegebene Palette noch nicht gerendert sein darf. Soll heißen: Ihr müsst im Paletten-Abschnitt eine neue Palette erzeugen auf die noch kein Feld verweist (3ter Abschnitt in der semikolongetrennten Felddefinition).

Diese Paletten werden immer ans Ende des Eingabeformulars gerendert, was bei einseitigen Formularen vielleicht noch Sinn machen könnte, aber spätestens bei Tab gesteuerten Formularen wird es für den User unübersichtlich, da sich diese Palette auf Grund von installierten Extensions nie im selben Tab befinden wird.

## canNotCollapse

Paletten werden im Normalfall immer versteckt. Erst durch Klicken auf das Paletten Icon öffnet sich die entsprechende Palette zur weiteren Bearbeitung. Je nach Euren Rechten gibt es unterhalb der Datenfelder eine Checkbox mit dem Namen "Zweite Optionspalette anzeigen". Wenn Ihr diese Checkbox markiert, dann werden Euch ALLE Paletten standardmäßig eingeblendet OHNE erst auf das Paletten Icon drüben zu müssen. Mit Hilfe von canNotCollapse = true werden die Paletten Icons überflüssig, da nun immer alle Paletten angezeigt werden, auch wenn "Zweite Optionspalette anzeigen" demarkiert wurde.

## tstamp

Gebt hier den Spaltennamen an, der mit jedem Bearbeiten eines Datensatzes mit dem aktuellen Zeitstempel gefüllt werden soll. Im Idealfall ist dies die Spalte tstamp.

## crdate

Tragt hier den Spaltennamen ein, der mit jedem neu erstellten Datensatz mit dem Zeitstempel des Erstellungsdatums gefüllt werden soll. Im Idealfall ist dies die Spalte crdate. Der hier eingetragene Wert wird nie wieder geändert.

## cruser_id

Tragt hier einen Spaltennamen ein, der mit jeden neu erstellten Datensatz mit der UID des Backendbenutzers gefüllt werden soll. Im Idealfall ist dies die Spalte cr_userid.

## rootLevel

In dieser Eigenschaft sind nur drei Werte erlaubt:

0: Die Datensätze können nur auf Seiten erstellt werden, die eine pid von > 0 aufweisen.
1: Die Datensätze können nur auf der Rootseite erstellt werden. Hier können nur Admins Daten erstellen.
-1: Die Datensätze können sowohl auf Seiten als auch auf der Rootseite erstellt werden.

## readOnly

Die Daten in diesen Tabellen können nur gelesen aber nicht editiert werden.

## adminOnly

Die Daten in der Tabelle können nur von Administratoren geändert werden. Administratoren sind BE-User, die das "ist Admin"-Flag gesetzt haben.

## editlock

Dieser Option muss ein Spaltenname übergeben werden, mit Hilfe dessen entschieden werden kann, ob ein Datensatz von anderen Redakteuren weiter bearbeitet werden kann oder nicht.

Am meisten Sinn macht es dieses Feld als Checkbox zu realisieren. Durch das Markieren eines Datensatzes mit Hilfe dieser Checkbox wird dieser eine Datensatz für alle anderen "nicht Admins", also Redakteuren gesperrt. Kann also nicht mehr bearbeitet werden. Erst ein Admin-User kann diesen Datensatz öffnen und durch Demarkieren der Checkbox den Datensatz für die Redakteure wieder freigeben zur weiteren Bearbeitung.

Hier die Meldung, wenn ein Redakteur versucht diesen Datensatz trotz Sperre zu bearbeiten:

Sie haben nicht die nötigen Rechte, um diese Änderung durchzuführen.
Reason: ERROR: Record was locked for editing. Only admin users can change this state.

## origUid

Wenn Ihr mit dem Versionierungsfeature von TYPO3 arbeitet (Systemextension: version), dann wird mit jeder neu erstellten Version ein Kopie des gewählten Datensatzes erstellt. Damit diese neue Kopie weiß, wo sich der original Datensatz befindet, könnt Ihr hier einen Spaltennamen angeben, der die UID des Originaldatensatzes aufbewahren kann. Üblicherweise wird hier die Spalte "t3_origuid" verwendet.

## delete

Normalerweise ist ein Datensatz nach dem Löschen futsch. Um das zu unterbinden könnt Ihr hier einen Spaltennamen angeben, der den Datensatz als gelöscht markieren kann...ihn aber nicht wirklich unwiederbringlich aus der Datenbank rauslöscht.

## enablecolumns

Hier könnt Ihr Spaltennamen angeben, die für das Anzeigen eines Datensatzes sind. Dazu gehören die Spalten hidden, deleted, start und endtime als auch fe_group. All diese Spalten entscheiden darüber, ob ein Datensatz für einen bestimmten Benutzer zu einer angegeben Zeit angezeigt werden darf oder nicht.

Für Programmierer ist die Angabe dieser Eigenschaft sehr wichtig, da diese dem Programmierer sehr viel Arbeit abnehmen kann. Das cObj bietet dem Programmierer zum Beispiel die Methode enableFields an. Diese kümmert sich völlig automatisch darum dem Programmierer mitzuteilen, ob ein Datensatz in Abhängigkeit zur altuellen Zeit und evtl. eingeloggtem User angezeigt werden darf oder nicht.

## searchFields

Diese Option ist erst seit TYPO3 4.6 mit an Board und hilft der Backendsuche dabei nur Felder zu durchsuchen, die mit dieser Option als durchsuchbar gelten. Wollt Ihr also, dass auch Eure Datensätze mit Hilfe der Backendsuche gefunden werden können, dann gebt die zu durchsuchenden Spaltennamen kommasepariert an.

## groupName

Diese Option erwartet einen Extensionkey einer anderen Extension. Dadurch bildet Eure Extension keine eigene Gruppe mehr innerhalb des Assisstenten für neue Datensätze, sondern bindet die Links zum Erstellen Eurer Datensätze innerhalb der Gruppe an, die Ihr hier angegeben habt.

## hideAtCopy

Wenn Ihr bei der Eigenschaft enablecolumns für das Feld "disabled" einen Wert eingetragen habt, dann werden Kopien eines Datensatzes automatisch als versteckt markiert und müssen von einem BE-User erst aktiviert werden, damit diese im Frontend angezeigt werden können.

## prependAtCopy

Wenn Ihr einen Datensatz innerhalb der gleichen Seite kopiert, dann könnt Ihr hier einen Text angeben, der dem Titel des neuen Datensatzes hinten angefügt wird. Der Standard ist, dass dem Titel der Kopie "(Kopie 1)" angefügt wird. Bei weiteren Kopien inkrementiert sich der Zähler automatisch: Kopie 2, Kopie 3 und so weiter.

## copyAfterDuplFields

Diese Option ist von Namen her schon ein bisschen wiedersprüchlich, denn mit Ihr kann angegeben werden, welche Felder bei einem Kopiervorgang mit kopiert werden sollen. Natürlich macht diese Option nur in einer ganz besonderen Konstellation Sinn:

Um diese Option nutzen zu können, muss die Tabelle über die manuelle Sortierung verfügen (sortby => 'sorting'). Jetzt ist es überhaupt erst möglich einen kopierten Datensatz nicht nur an das Ende vorhandener Datensätze einzufügen, sondern auch nach einem ganz bestimmten Datensatz. Das Einfüge Icon erscheint nicht mehr NUR oberhalb der Tabelle, sondern PRO Datensatz.

Wenn Ihr nun auf das Einfüge-Icon klickt, das sich in einer Zeile eines bereits vorhandenen Datensatzes befindet, dann werden alle Felder des einzufügenden Datensatzes, die Ihr mit dieser Option definiert habt, mit den Daten diesen bereits bestehenden Datensatzes überschrieben.

Diese Option macht gerade für die Tabelle tt_content Sinn. Wär ja doof, wenn ich einen Inhalt aus Spalte "rechts" hinter ein Inhaltselement aus der Spalte "links" kopiere, aber die Spaltendefinition immer noch auf "rechts" steht und ich erst den Datensatz bearbeiten muss, um die Spalte nachträglich auf "links" umzustellen.

## setToDefaultOnCopy

Hier könnt Ihr eine Liste von Spaltennamen angeben, die nach dem Kopieren wieder Ihren Standardwert erhalten sollen. Dazu müsst Ihr natürlich zuvor auch einen Defaultwert für Euer Feld definiert haben (default => 'Initialer Wert')

## useColumnsForDefaultValues

Um diese Option nutzen zu können muss in der ext_localconf.php folgende Zeile eingebunden werden:

```php
t3lib_extMgm::addUserTSConfig('options.saveDocNew.tx_sftca_company=1');
```

Ich glaub ab TYPO3 Version 4.4 ist diese Zeile allerdings nicht mehr nötig, da man sich dazu entschieden hat, diese Zeile für jede Tabelle als Standard zu setzen. Naja...auf jeden Fall erscheint Dank dieser Zeile oder Dank diesem Standard noch ein 4ter Speicherbutton mit dem Ihr einen Datensatz abspeichern könnt und danach sofort einen weiteren Datensatz anlegen könnt.

Mit dieser Option könnt Ihr nun entscheiden welche Felder des vorherigen Datensatzes als Standardwert für den neuen Datensatz verwendet werden soll.

Interessant wird die Option auch, wenn Ihr das manuelle Sortieren aktiviert habe (sortby => 'sorting'). Denn dann erscheinen pro Datensatz im Listview kleine Icons, um einen Datensatz nach einem bereits vorhandenen Datensatz zu erstellen. Auch hier wird diese Option aktiv und kopiert die definierten Felder des vorhandenen Datensatzes als Standardwerte in den neu zu erstellenden Datensatz.

## shadowColumnsForNewPlaceholders

Wenn innerhalb einer Arbeitsumgebung, die nicht die LIVE-Umgebung wiederspiegelt, ein neuer Datensatz angelegt wird, dann wird parallel dazu auch ein weiterer Datensatz für die LIVE-Version erstellt. Diese Kopie für die LIVE-Umgebung trägt in der Datenbank den Titel: "INITIAL PLACEHOLDER".

Mit dieser Option könnt Ihr nun angeben, welche Felder des Datensatzes aus der Arbeitsumgebung mit in diesen Platzhalterdatensatz für die LIVE-Umgebung mit kopiert werden sollen.

## is_static

Mit dieser Option wird eine Tabelle als "statisch" deklariert. Das heißt, dass diese Daten sich im Normalfall nie oder nur sehr selten ändern. Somit sind statische Tabellen bestens für Ländercodes, Währungen und Sprachen geeignet. Die enthaltenen Daten sollten am Besten nur von zentraler Stelle aus administriert werden, um eine eindeutige Zuordnung von Datensatz zu der UID beizubehalten und das systemübergreifend.

Leider wird nicht genau dokumentiert, was sich nach Setzen dieser Option innerhalb von TYPO3 verändert. Hier meine eigenen Nachforschungen:

Innerhalb des Assisstenten für neue Datensätze verschwindet unsere Tabelle aus der Auswahl. Wenn man sich jedoch im Listenmodul befindet, kann man über das Icon "Neuer Datensatz" innerhalb der Tabelle dennoch Datensätze hinzufügen.

Wenn ich den Quellcode richtig interpretiere, dann werden beim Export in t3d-Dateien statische Tabellen immer mit Ihren fest zugeordneten UIDs exportiert.

Empfehlung: Wenn Ihr schon is_static verwenden wollt, dann fügt wenigstens noch diese beiden Option Eurer Tabelle hinzu:

```php
'readOnly' => TRUE,
'rootLevel' => 1,
```

## fe_cruser_id

Wenn Ihr Frontendediting auch für eingeloggte Webseitenbenutzer aktiviert habt, macht es durchaus Sinn, die ID diesen Benutzers mit in den gerade erstellten Datensatz zu packen. Wählt hier eine Spalte, in die die UID des Webseitenbenutzers abgelegt werden soll. Der Standard ist: "fe_cruser_id".

## fe_crgroup_id

Wenn Ihr Frontendediting auch für eingeloggte Webseitenbenutzer aktiviert habt, macht es durchaus Sinn, die Gruppen-UIDs diesen Benutzers mit in den gerade erstellten Datensatz zu packen. Wählt hier eine Spalte, in die die UIDs der Gruppen abgelegt werden soll. Der Standard ist: "fe_crgroup_id".

## fe_admin_lock

Diese Option muss auf einen Spaltennamen verweisen, der als Checkbox erstellt wurde. Im Normalfall können Webseitenbenutzer Datensätze, die sie im Frontend erstellt haben auch wieder bearbeiten (fe_user-UID == fe_cruser_id). Wenn allerdings die Checkbox der hier angegebenen Spalte aktiviert wurde, dann klappt die Bearbeitung durch den Frontendbenutzer nicht mehr.

## languageField

Gebt hier die Spalte an, die die Sprach-UID enthält.

Die Sprach-UID ist eine Referenz zu der Tabelle sys_language.

Folgende Sprach-UIDs sind als Wert in dieser Spalte erlaubt:

-1: Der Datensatz spiegelt keine Sprache wieder. Er wird immer angezeigt. Egal welche Sprache ausgewählt wurde.

0: Die Standardsprache

Werte > 0: Spiegeln die Sprache wieder, wie sie innerhalb der sys_language Tabelle definiert wurde

Es wird empfohlen dieses Feld als Selectbox zu erstellen mit "maxitems <= 1".

## transOrigPointerField

Gebt hier den Spaltennamen an, der auf die UID des Datensatzes zeigt, der die Standardsprache wiederspiegelt. Im Normalfall wird hier l18n_parent verwendet. Die Spalte selbst muss auch in der TCA angelegt worden sein. Empfohlen wird der Typ "passthrough".

## transForeignTable

In den meisten Fällen werden die Übersetzungen eines Datensatzes innerhalb der gleichen Tabelle abgespeichert. Wie Ihr aber am Beispiel von der Tabelle "pages" sehen könnt gibt es in diesem Fall eine eigene Übersetzungstabelle mit dem Namen "pages_language_overlay". Um solche Konstrukte zu erzeugen benötigt Ihr diese Option und müsst ihr den Tabellennamen angeben, der für die Übersetzungen zuständig sein soll.

## transOrigPointerTable

Diese Option ist das Gegenteil von transForeignTable. Hier muss nun angegeben werden, welche Tabelle die Standardsprache beinhaltet. Im Falle der pages_language_overlay würde man hier demnach die "pages" Tabelle angeben.

In der englischen Originaldokumentation wird noch eine Warnung ausgesprochen, dass dieser Weg der Übersetzung mit 2 Tabellen zu Inkonsistenzen führen kann und somit nicht unbedingt verwendet werden sollte.

## transOrigDiffSourceField

In die hier angegebene Spalte werden die Werte der Standardübersetzung geschrieben. Wird nun etwas an der Originalsprache verändert, kann der Redakteur im Backend sehen welche Felder sich verändert haben, da diese sich nun in der Übersetzung rot hervorheben. Dadurch weiß er welche Texte er bei Bedarf anpassen muss.

## versioningWS

Wenn für Eure Tabelle das Versioning Feature aktiviert werden soll, dann muss dieser Wert entweder auf 1 oder auf 2 gesetzt werden. Diese Zahlen spiegeln die Versionsnummer der Versionsfeatures wieder. Das einzige was ich gefunden habe ist, das mit Version 2 das Verschieben von Elementen eingeführt wurde. Auch innerhalb der TCA von tt_content wird die Zahl 2 verwendet, weshalb ich die 2 auch für Eure Tabelle empfehlen würde.

Um Versioning verwenden zu können müssen ein paar zusätzliche Spalten in Eure Tabelle eingebunden werden:

## t3ver_oid

Datensätze, die sich in einer Arbeitsumgebung befinden verweisen mit dieser Spalte auf den Datensatz der LIVE-Umgebung. Die LIVE-Umgebung selbst hat hier eine 0 stehen.

## t3ver_id

Diese Spalte beinhaltet einen aufsteigenden Zähler, der mit jeder neu erstellten Version um 1 hochzählt.

## t3ver_label

Hierbei handelt es sich einfach um einen Namen dieser Version. Vergebt eine Versionsnummer oder vergebt einen Namen wie "Ostern".

## t3ver_wsid

Über das Listenmodul könnt Ihr nahezu beliebig viele Arbeitsumgebungen erzeugen. Je nach dem in welcher Arbeitsumgebung sich dieser Datensatz derzeit befindet, spiegelt diese Spalte die UID der Arbeitsumgebung wieder. Eine 0 bedeutet, dass sich dieser Datensatz in der LIVE-Umgebung befindet.

## t3ver_state

Hier muss erstmal unterschieden werden, ob es sich um einen Datensatz innnerhalb einer Arbeitsumgebung handelt oder um die LIVE-Umgebung:

LIVE-Umgebung:

1 oder 2 bedeutet, dass es sich bei diesem Datensatz um einen temporären Platzhalter handelt. Das passiert, wenn ein Redakteur innerhalb einer Arbeitsumgebung einen neuen Datensatz erzeugt. Parallel dazu wird völlig automatisch dieser für's Frontend unsichtbare Platzhalter erzeugt.

3 bedeutet, dass es sich bei diesem Datensatz um einen "Verschiebe nach"-Platzhalter handelt. In diesem Fall wird auch die Spalte t3ver_move_id mit der UID des LIVE-Datensatzes befüllt (die selbst auch wieder eine Arbeitsgruppenversion beinhalten kann), die an eine andere Position verschoben werden soll. Anders als im Falle von 1 und 2 gibt es bei diesem Platzhalter Typ keinen Arbeitsumgebungsdatensatz.

Wenn diese Spalte einen Wert größer 0 hat, dann darf dieser Datensatz niemals in der LIVE-Umgebung erscheinen.

Arbeitsumgebung:

1 oder 2 bedeutet, dass wenn dieser Datensatz veröffentlicht wird, dass dann die Arbeitsumgebung gelöscht werden muss.

-1 bedeutet nur, dass die derzeitige LIVE-Version als t3ver_state eine 1 stehen hat.

4 bedeutet, dass es sich hierbei um eine Verschiebe Position für die LIVE-Version handelt und dass es einen Verschiebe-nach Datensatz innerhalb der LIVE-Umgebung gibt.

## t3ver_stage

Innerhalb einer Arbeitsumgebung können Stationen definiert werden, die so ein Datensatz durchlaufen muss. In dieser Spalte wird die UID dieser Station eingefügt. 0 bezieht sich auf's ebearbeiten, während -10 für "Fertig für die Veröffentlichung" steht.

## t3ver_count

Verstehe ich noch nicht.

## t3ver_tstamp

Zeitstempel des letzten Wechsels zwischen einer Version in der Arbeitsumgebung und der LIVE-Umgebung.

## t3ver_move_id

Muss ich noch ausprobieren. Anscheinend wird hiermit angegeben wohin die Onlineversion verschoben werden soll, wenn die Arbeitsumgebung veröffentlicht und somit LIVE geht.

## versioningWS_alwaysAllowLiveEdit

Wenn diese Option true ist, dann kann der Datensatz innerhalb der Tabelle immer bearbeitet werden. Egal ob sich dieser gerade in der LIVE- oder in der Arbeitsumgebung befindet. Und er kann sogar bearbeitet werden, wenn das LIVE-Bearbeiten in der Arbeitsumgebung verboten wurde.

## versioning_followPages

Wenn die Option gesetzt ist, wird mit jeder neu angelegten Version für die Tabelle "pages" auch der Inhalt Eurer Tabelle kopiert. Damit nicht alle Daten kopiert werden ist es wichtig, dass Ihr die Option origUid setzt.

PS: Ich habe diese Option noch nicht getestet und weiß ehrlich gesagt noch nicht welchen Vorteil die hat.

## dividers2tabs

Normalerweise werden alle Tabellenfelder untereinander dargestellt. Es gibt aber die Möglichkeit einem Spaltennamen einen bestimmten Prefix zu verpassen, um damit TYPO3 mitzuteilen, dass diese Spalte in einem neuen Tab dargestellt werden soll. Damit das funktioniert muss diese Option gesetzt sein.

Beispiel folgt

## dynamicConfigFile

Diese Eigenschaft ist nur für's Aufräumen zuständig. Denn normalerweise könnt Ihr alle TCA-Einstellungen direkt in der ext_tables.php erledigen. Mit dieser Option könnt Ihr z.B. die Spaltendefinitionen in eine andere Datei auslagern.

```php
'dynamicConfigFile' => t3lib_extMgm::extPath($_EXTKEY) . 'tca.php',
```

## EXT[Extension-Key]

Hier habt Ihr einen Bereich um der TCA eigene Eigenschaften hinzuzufügen. Versucht Euch bitte an diese Konvention zu halten, da sich sonst mehrere Extensions mit der selben Idee überschneiden könnten.

```php
['ctrl']['EXT']['meineExtensionKey'] = (Dein Freiraum)
```
