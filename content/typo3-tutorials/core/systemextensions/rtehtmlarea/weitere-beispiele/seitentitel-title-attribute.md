+++
title = "Seitentitel im title-Attribut"
date = 2024-01-09T20:34:33+01:00
aliases = ["seitentitel-im-title-attribute.html"]
+++

Vielleicht kennt Ihr das: Ihr erstellt mit dem RTE einen Text, fügt ein paar Links hinzu und auf der Webseite erscheint beim Überfahren der Links mit der Maus: "Opens internal link in current window".

Wäre es nicht schöner, wenn beim Überfahren der Links mit der Maus der Seitentitel der aufzurufenden Seite erscheinen würde? Ich denke schon und da diese Vorgehensweise ein bissl tricky ist stelle ich Euch dieses Tutorial vor.

## TsConfig

Um unser Ziel zu erreichen benötigen wir auf jeden Fall die userFunc-Eigenschaft aus dem Objekt HTMLparser_tags (tags):

```typo3_typoscript
RTE.default.proc.entryHTMLparser_db = 1
RTE.default.proc.entryHTMLparser_db {
  tags {
    a.fixAttrib.href.userFunc = fileadmin/templates/class.user_settitle.php:user_settitle->geturl
    a.fixAttrib.href.always = 1
    a.fixAttrib.title.userFunc = fileadmin/templates/class.user_settitle.php:user_settitle->settitle
    a.fixAttrib.title.always = 1
  }
}
```

## Warum 2 mal userFunc?

Ich hab ne ganze Weile rumgetüftelt und wollte es mit nur einer userFunc schaffen, aber leider werden die anderen Attribute des A-Tags nicht mitgeliefert. Ich konnte von meiner settitle-Funktion also nicht auf das id-Attribut zugreifen, mit dem ich dann den Titel hätte herausfinden können. Weiterhin gibt es hier keine Eigenschaft, um den Inhalt eines bestimmten Attributes auszulesen. Ich musste also eine Lösung finden, den Inhalt des id-Attributes auszulesen und das gelang mir nur mit einer 2ten userFunc (geturl).

## Reihenfolge!

Es ist sehr wichtig, dass zu erst die userFunc geturl aufgerufen wird bevor wir zur userFunc settitle kommen. In der userFunc geturl werden wir nämlich ein paar Settings vornehmen auf die wir mit der settitle userFunc zugreifen wollen.

## Eigenschaft always

Ich habe hier die Eigenschaft always=1 gesetzt, da die Userfunctions auch dann ausgeführt werden sollen, wenn sich im A-Tag gar kein title-Attribut befindet.

## entryHTMLparser_db

Ich hab's auch mit dem exitHTMLparser_db ausprobiert, aber hier kommt der Link nur noch in diesem Format an:

```html
<link 38>stdWrap</link>
```

In diesem Format gibt es aber gar keine Attribute mehr und deshalb kann ich auf mit der tags-Eigenschaft nicht auf die gesuchte ID zugreifen. Es blieb also nur noch entryHTMLparser_db übrig.

## Die beiden Funktionen

Erstellt im fileadmin/templates-Verzeichnis eine Datei mit dem Namen: class.user_settitle.php. Macht das bitte per FTP oder mit der Extension quixplorer, da Ihr in diesem Verzeichnis mit der "Dateiliste" keine PHP-Dateien erstellen/bearbeiten könnt und füllt diese mit folgendem Inhalt:

```php
<?php
/**
 * Class user_settitle
 */
class user_settitle 
{
    /**
     * @var \TYPO3\CMS\Core\Database\DatabaseConnection
     */
    protected $databaseConnection = NULL;

    /**
     * @var \TYPO3\CMS\Frontend\Page\PageRepository
     */
    protected $pageRepository = NULL;

    /**
     * constructor of this class
     */
    public function __construct() 
    {
        $this->databaseConnection = $GLOBALS['TYPO3_DB'];
        $this->pageRepository = \TYPO3\CMS\Core\Utility\GeneralUtility::makeInstance('TYPO3\\CMS\\Frontend\\Page\\PageRepository');
    }

    /**
     * @param string $content
     * @param array $conf
     * @return mixed
     */
    public function setTitle($content, $conf) 
    {
        $urlArr = \TYPO3\CMS\Core\Utility\GeneralUtility::explodeUrl2Array(parse_url($GLOBALS['TSFE']->register['userfunc_rte_url'], PHP_URL_QUERY));
        if (TYPO3\CMS\Core\Utility\MathUtility::canBeInterpretedAsInteger($urlArr['id'])) {
            $row = $this->databaseConnection->exec_SELECTgetSingleRow(
                'title',
                'pages_language_overlay',
                'pid=' . (int)$urlArr['id'] . '
                AND sys_language_uid=' . $this->getSysLanguageFromCurrentRecord(),
                '', '', ''
            );
            if (empty($row)) {
                $row = $this->databaseConnection->exec_SELECTgetSingleRow(
                    'title',
                    'pages',
                    'uid=' . (int)$urlArr['id'],
                    '', '', ''
                );
            }
            return $row['title'];
        } else {
            return $content;
        }
    }

    /**
     * @param string $content
     * @param array $conf
     * @return mixed
     */
    public function getUrl($content, $conf) 
    {
        $GLOBALS['TSFE']->register['userfunc_rte_url'] = $content;
        return $content;
    }

    /**
     * get sys_language_uid
     *
     * return int
     */
    protected function getSysLanguageFromCurrentRecord() {
        $records = $_REQUEST['data']['tt_content'];
        $record = current($records);
        return $record['sys_language_uid'];
    }
}
```

Wie oben schon geschildert wird zu allererst die Funktion geturl aufgerufen. An diese Funktion wird der Inhalt des href-Attributes gesendet. Damit uns dieser Inhalt in unserer nächsten Funktion settitle auch noch erhalten bleibt, speichern wir diesen in einem Register zwischen.

Nun wird Dank dem zweiten Eintrag in der TsConfig die Funktion settitle aufgerufen. Diese greift auf unsere im Register gespeicherte URL zu und diese wird mit ein paar TYPO3-Hilfsmittel in Ihre Einzelteile aufgesplittet bis ich mit einem Array und dem Key 'id' auf die Seiten-ID unserer aufzurufenden Seite zugreifen kann. Diese ID checke ich nochmal kurz gegen, ob diese auch wirklich ein Integer ist und hole mir dann mit einem Datenbank-Query den gewünschten Titel.
