+++
title = "AJAX-Beispiele mit der eID-Methode"
linkTitle = "eID Beispiele"
date = 2024-01-06T19:45:25+01:00
slug = "eid-beispiele"
weight = 3
+++

Ich zeige Euch nun ein paar Beispiele, wie Ihr AJAX mithilfe der eID-Methode ans
Laufen bekommt.

### Beispiel für statischen Text

Dieses Beispiel baut im Hintergrund der aktuellen Seite eine Verbindung zu Eurem 
Server auf und ruft dort die PHP-Datei StaticText.php auf. Diese Datei enthält ein
einfaches echo mit fest hinterlegtem Inhalt, den Ihr mit einem Klick abrufen könnt.

Legt dazu folgende Dateien mit entsprechendem Inhalt an:

**ext_localconf.php**

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include']['sfapi_statictext'] = 'EXT:sfapi/Classes/Ajax/StaticText.php';
```

**Classes/Ajax/StaticText.php**

```php
<?php
echo 'Hallo alle miteinander';
```

**HTML**

```html
Klick mich
```

**JavaScript**

```js
$('#ajax').click(function() {
  $.post('index.php?eID=sfapi_statictext', function(data) {
    alert(data);
  });
});
```

### Beispiel mit variablem Text

In diesem Beispiel zeige ich Euch, wie Ihr an Euer Script einen dynamischen Wert übergebt.
Der Inhalt dieser Variable wird dann leicht modifiziert von der VariableText.php
zurückgeliefert und dann in der alert-Box wieder angezeigt.

Legt dazu folgende Dateien mit entsprechendem Inhalt an:

**ext_localconf.php**

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include']['sfapi_variabletext'] = 'EXT:sfapi/Classes/Ajax/VariableText.php';
```

**Classes/Ajax/VariableText.php**

```php
<?php
$value = \TYPO3\CMS\Core\Utility\GeneralUtility::_GP('variable');
echo htmlspecialchars(strip_tags($value));
```

**HTML**

```html
Klick mich
```

**JavaScript**

```js
$('#ajax').click(function() {
  $.post('index.php?eID=sfapi_variabletext', {variable: 'Hallo zusammen'}, function(data) {
    alert(data);
  });
});
```

### Zugriff auf die Datenbank mit Callable

Mit TYPO3 7.4 wurde PSR-7-Support für die eID-Schnittstelle implementiert. Wenn Ihr 
diesen neuen Standard verwenden wollt, müssen Eure eID-Scripte vom ControllerInterface
erben. Ab TYPO3 7.5 ist diese Vererbung jedoch nicht mehr notwendig.

Die Registrierung der eID-Keys schaut in der `ext_localconf.php` nun ein wenig anders aus:

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include']['sfapi_database'] = \StefanFroemken\Sfapi\Ajax\Database::class . '::processRequest';
```

Anstatt einfach nur die PHP-Datei anzugeben müsst Ihr nun ein Callable eintragen. Dabei
handelt es sich um eine Kombination aus Klassennamen und Methodennamen getrennt durch 2 
Doppelpunkte. Die Arraynotation ist hier nicht erlaubt, weil TYPO3 intern mit strpos 
auf das Vorhandensein von zwei Doppelpunkten prüft.

Der aufzurufenden Methode (in diesem Fall "processRequest") werden 2 Parameter übergeben.
Einmal das Request-Objekt, das Daten wie GET und POST beinhaltet und zum anderen ein
leeres Response-Objekt, das eine body-Eigenschaft vom Typ Stream beinhaltet, der mit
php://temp initialisiert wurde. Somit könnt Ihr nicht einfach einen Text an die 
body-Eigenschaft übergeben, sondern müsst diesen mithilfe von write() an den
Stream übertragen. Schaut Euch dazu auch das Beispiel unten an.

In dem Database-Objekt gibt es keine echos mehr. Die komplette Ausgabe wird mithilfe
des Response-Objektes TYPO3-intern nach PRS-7-Standard automatisch realisiert. Hier nun
der Inhalt meiner `Database.php`:

**Database.php**
*Verfügbar seit TYPO3 8.1. Für frühere Versionen müsst Ihr $GLOBALS['TYPO3_DB'] verwenden.*

```php
<?php
declare(strict_types = 1);
namespace StefanFroemken\Sfapi\Ajax;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use TYPO3\CMS\Core\Database\ConnectionPool;
use TYPO3\CMS\Core\Utility\GeneralUtility;

/**
 * Class Database
 */
class Database
{
    /**
     * Get titles from database
     *
     * @param ServerRequestInterface $request
     * @param ResponseInterface $response
     * @return ResponseInterface
     */
    public function processRequest(ServerRequestInterface $request, ResponseInterface $response): ResponseInterface
    {
        $content = [];
        foreach ($this->getTitlesOfTheFirstThreePages() as $row) {
            $content[] = 'Pagetitle: ' . $row['title'];
        }
        $response->getBody()->write(implode('', $content));

        return $response;
    }

    /**
     * Returns the titles of the first three pages
     *
     * @return array
     */
    protected function getTitlesOfTheFirstThreePages(): array
    {
        $queryBuilder = $this->getConnectionPool()->getQueryBuilderForTable('pages');
        $rows = $queryBuilder
            ->select('title')
            ->from('pages')
            ->setMaxResults(3)
            ->execute()
            ->fetchAll();
        if (!$rows) {
            $rows = [];
        }
        return $rows;
    }

    protected function getConnectionPool(): ConnectionPool
    {
        return GeneralUtility::makeInstance(ConnectionPool::class);
    }
}
```

### INSERTs in die Datenbank führen zu Cacheproblemen

Neben einfachen SQL-Abfragen könnt Ihr natürlich auch Dateneingaben machen. Je nach dem
WO in welche Tabelle Ihr diese Daten einpflegt, kann es durchaus passieren, dass diese 
Daten bei der Ausgabe jedoch nicht oder noch nicht angezeigt werden.

Sofern möglich cached TYPO3 jede Seite. Änderungen, die direkt über die Datenbank 
realisiert worden sind, bekommt TYPO3 überhaupt nicht mit und liefert somit den 
"alten" Stand aus den Cachetabellen aus.

Mit folgendem Script ladet Ihr Euch den CacheManager und könnt dann mit flushCachesInGroup
den kompletten Seitencache leeren oder aber Ihr nehmt flushCachesInGroupByTag() und könnt
auf diese Weise den Cache für eine individuelle Seite leeren.

**Classes/Ajax/ClearCache.php**

```php
<?php
/** @var \TYPO3\CMS\Core\Cache\CacheManager $cacheManager */
$cacheManager = \TYPO3\CMS\Core\Utility\GeneralUtility::makeInstance(
    \TYPO3\CMS\Core\Cache\CacheManager::class
);
// clear cache of all pages
$cacheManager->flushCachesInGroup('pages');
// clear cache of page with UID 34
$pageId = 34;
$cacheManager->flushCachesInGroupByTag('pages', 'pageId_' . $pageId);
```

### Mit eID auf den aktuell angemeldeten Benutzer zugreifen

Die angemeldeten Frontendbenutzer werden über das TypoScriptFrontendController-Objekt 
verwaltet. Zunächst einmal die Registrierung des eID-Keys in der `ext_localconf.php`:

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include']['sfapi_frontenduser'] = 'EXT:sfapi/Classes/Ajax/FrontEndUser.php';
```

In der FrontEndUser.php erzeugt Ihr das Objekt TypoScriptFrontendController und gebt ihm
noch die Parameter aus TYPO3_CONF_VARS, die Seiten ID und den PageType mit, der
üblicherweise 0 ist. Mit initFEuser werden dann die Session- und Cookie-Daten ausgelesen
und bei Erfolg der Frontendbenutzer-Datensatz in fe_user->user abgelegt. Hier ein einfaches
Beispiel:

```php
<?php
/** @var \TYPO3\CMS\Frontend\Controller\TypoScriptFrontendController $typoScriptFrontendController */
$typoScriptFrontendController = \TYPO3\CMS\Core\Utility\GeneralUtility::makeInstance(
    \TYPO3\CMS\Frontend\Controller\TypoScriptFrontendController::class,
    $GLOBALS['TYPO3_CONF_VARS'],
    1, // page ID
    0 // pageType.
);
$typoScriptFrontendController->initFEuser();
if ($typoScriptFrontendController->fe_user->user['username'] == '') {
    echo 'Sie sind nicht angemeldet';
} else {
    echo 'Benutzername: ' . $typoScriptFrontendController->fe_user->user['username'];
}
```
