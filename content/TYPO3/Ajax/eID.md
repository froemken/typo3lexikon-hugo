+++
title = "AJAX-Request mittels eID"
linkTitle = "eID"
date = 2024-01-06T19:45:25+01:00
slug = "eid"
+++

Ajax-Requests auf Basis von `eID` werden vorzugsweise für das TYPO3 Frontend verwendet und werden innerhalb von TYPO3 sehr früh geladen. An dieser frühen Position hat noch keine Autorisierung stattgefunden, die Sprachen bzw. das Übersetzungssystem ist noch nicht gestartet, der TypoScriptFrontendController alias `TSFE` ist noch nicht geladen und selbst der Site- als auch der PageResolver wurden noch nicht ausgeführt. Somit stehen Euch im Rahmen von `eID` Requests nur ein paar wenige TYPO3 APIs und die Datenbank zur Verfügung.

Bis TYPO3 9.5 gab es die `EidUtility` Klasse, die Methoden enthielt, um nachträglich die Frontend Autorisation, die Sprachen und das TCA zu laden. Da die Methoden der `EidUtility` Klasse nach Belieben der Entwickler aufgerufen werden konnten, konnten die Entwickler selbst entscheiden, welcher TYPO3 Prozess in welcher Reihenfolge ausgeführt werden sollte. Da dies oft zu unerwünschten Nebeneffekten führte, wurde die `EidUtility` Klasse mit TYPO3 9.4 zunächst als `deprecated` deklariert und schlussendlich mit TYPO3 10.0 entfernt.

Durch den sehr frühen Aufruf von eigenem Quellcode mittels `eID` fühlen sich viele Entwickler aufgrund der Geschwindigkeitsvorteile leider noch immer dazu verleitet sämtliche ihrer Requests mit diesem System umzusetzen. Durch den zusätzlichen Wegfall der `EidUtility` entstehen nun noch schlimmere Konstellationen, wo Entwickler teilweise den kompletten TYPO3 Bootstraps Prozess selbst in die Hand nehmen.

Ohne geladene Seiten-Konfiguration, ohne verarbeitete Seiten-UID und mit nur einem rudimentärem TSFE sich um das Rendering von Inhaltselementen und insbesondere Extbase-Plugins in einem `eID` Request zu kümmern, führt oftmals zu fehlerhaften Cache-Einträgen, die sich sogar auf das Rendering der üblichen Webseitenausgabe auswirken können.

Beispiele für `eID` Ajax-Requests:
- Text verarbeiten: z.B. Deutsche Umlaute in `ae`, `oe`, `ue` umwandeln
- Request zu anderen Anbietern: z.B. Temperatur bei Wetter API Anbieter abfragen
- Daten von Tabellen ohne Workspaces/Übersetzung abfragen

Mein persönliches Empfinden und auch Gespräche im TYPO3 Kreis sagen mir, dass `eID` Request in Zukunft durch Middlewares vollständig abgelöst werden. Wusstet ihr, dass `eID` Requests seit TYPO3 9.4 bereits als Middleware umgesetzt werden?

## eID registrieren

`eID` Ajax-Requests werden mit dem URL-Parameter `&eID=[key]` eingeleitet, wobei der zu verwendende `key` zunächst in der `ext_localconf.php` registriert werden muss:

**ext_localconf.php** {{% badge style="green" icon="angle-double-up" %}}TYPO3 9.4{{% /badge %}}

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include']['sfapi_statictext'] = \StefanFroemken\Sfapi\Ajax\Request::class . '::staticText';
```

**ext_localconf.php** {{% badge style="red" icon="angle-double-down" %}}TYPO3 9.4{{% /badge %}}

[Seit TYPO3 9.4 veraltet](https://docs.typo3.org/c/typo3/cms-core/main/en-us/Changelog/9.4/Deprecation-85646-DeprecateEIDImplementedAsScript.html)

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include']['sfapi_statictext'] = 'EXT:sfapi/Classes/Ajax/StaticText.php';
```

## Beispiel: Statischer Text

In diesem Beispiel wird durch einen Klick auf "Klick mich" mittels JavaScript ein Ajax-Request zu der `Request` Klasse aufgebaut und die Methode `staticText` aufgerufen. Diese setzt den Inhalt von `HtmlResponse` auf "Hallo alle miteinander". Dieser Response wird im JavaScript mittels `then()` ausgelesen und in der Browser Konsole protokolliert. 

**Classes/Ajax/Request.php**

```php
<?php

declare(strict_types=1);

namespace StefanFroemken\Sfapi\Ajax;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use TYPO3\CMS\Core\Http\HtmlResponse;

class Request
{
    public function staticText(ServerRequestInterface $request): ResponseInterface
    {
        return new HtmlResponse('Hallo alle miteinander');
    }
}
```

**HTML**

```html
<html>
<head>
    <title>Ajax-Requests returns static text</title>
</head>
<body>
    <div id="ajax">Klick mich</div>
</body>
</html>
```

**JavaScript**

```js
document.querySelector('#ajax').addEventListener('click', event => {
    fetch('index.php?eID=sfapi_statictext', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'}
    }).then(data => {
        console.log('Request complete! Response: ', data);
    });
});
```

## Beispiel: Variabler Text

In diesem Beispiel wird durch einen Klick auf "Klick mich" mittels JavaScript ein Ajax-Request zu der `Request` Klasse aufgebaut und die Methode `variableText` aufgerufen. Diese nutzt die übergebene Variable aus dem Request Objekt, entfernt alle HTML-Tags und setzt dann den Inhalt von `HtmlResponse`. Dieser Response wird im JavaScript mittels `then()` ausgelesen und in der Browser Konsole protokolliert.

**Classes/Ajax/Request.php**

```php
<?php

declare(strict_types=1);

namespace StefanFroemken\Sfapi\Ajax;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use TYPO3\CMS\Core\Http\HtmlResponse;

class Request
{
    public function variableText(ServerRequestInterface $request): ResponseInterface
    {
        $postParameters = $request->getBody();
        $variable = $postParameters['variable'] ?? 'No Variable given';

        return new HtmlResponse(strip_tags($variable));
    }
}
```

**HTML**

```html
<html>
<head>
	<title>Ajax-Requests returns variable text</title>
</head>
<body>
<div id="ajax">Klick mich</div>
</body>
</html>
```

**JavaScript**

```js
document.querySelector('#ajax').addEventListener('click', event => {
    fetch('index.php?eID=sfapi_statictext', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: {
            variable: 'Hallo zusammen'
        }
    }).then(data => {
        console.log('Request complete! Response: ', data);
    });
});
```

## Zugriff auf die Datenbank mit Callable

Mit TYPO3 7.4 wurde PSR-7-Support für die eID-Schnittstelle implementiert. Wenn Ihr diesen neuen Standard verwenden wollt, müssen Eure eID-Scripte vom ControllerInterface erben. Ab TYPO3 7.5 ist diese Vererbung jedoch nicht mehr notwendig.

Die Registrierung der eID-Keys schaut in der `ext_localconf.php` nun ein wenig anders aus:

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include']['sfapi_database'] = \StefanFroemken\Sfapi\Ajax\Database::class . '::processRequest';
```

Anstatt einfach nur die PHP-Datei anzugeben, müsst Ihr nun ein `callable` eintragen. Dabei handelt es sich um eine Kombination aus Klassennamen und Methodennamen getrennt durch 2 Doppelpunkte. Eine Array-Angabe wie `[$class, $method]` ist hier nicht erlaubt, weil TYPO3 intern mit `strpos()` auf das Vorhandensein von zwei Doppelpunkten `::` prüft, um zwischen Klassenname und Methodenname zu unterscheiden.

Der aufzurufenden Methode (in diesem Fall "processRequest") werden 2 Parameter übergeben. Einmal das Request-Objekt, das Daten wie GET und POST beinhaltet und zum anderen ein leeres Response-Objekt, das eine body-Eigenschaft vom Typ Stream beinhaltet, der mit php://temp initialisiert wurde. Somit könnt Ihr nicht einfach einen Text an die 
body-Eigenschaft übergeben, sondern müsst diesen mithilfe von write() an den Stream übertragen. Schaut Euch dazu auch das Beispiel unten an.

In dem Database-Objekt gibt es keine echos mehr. Die komplette Ausgabe wird mithilfe des Response-Objektes TYPO3-intern nach PRS-7-Standard automatisch realisiert. Hier nun der Inhalt meiner `Database.php`:

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

Neben einfachen SQL-Abfragen könnt Ihr natürlich auch Dateneingaben machen. Je nachdem WO in welche Tabelle Ihr diese Daten einpflegt, kann es durchaus passieren, dass diese Daten bei der Ausgabe jedoch nicht oder noch nicht angezeigt werden.

Sofern möglich cached TYPO3 jede Seite. Änderungen, die direkt über die Datenbank realisiert worden sind, bekommt TYPO3 überhaupt nicht mit und liefert somit den "alten" Stand aus den Cachetabellen aus.

Mit folgendem Script ladet Ihr Euch den CacheManager und könnt dann mit flushCachesInGroup den kompletten Seitencache leeren oder aber Ihr nehmt flushCachesInGroupByTag() und könnt auf diese Weise den Cache für eine individuelle Seite leeren.

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

Die angemeldeten Frontendbenutzer werden über das TypoScriptFrontendController-Objekt verwaltet. Zunächst einmal die Registrierung des eID-Keys in der `ext_localconf.php`:

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['eID_include']['sfapi_frontenduser'] = 'EXT:sfapi/Classes/Ajax/FrontEndUser.php';
```

In der FrontEndUser.php erzeugt Ihr das Objekt TypoScriptFrontendController und gebt ihm noch die Parameter aus TYPO3_CONF_VARS, die Seiten ID und den PageType mit, der üblicherweise 0 ist. Mit initFEuser werden dann die Session- und Cookie-Daten ausgelesen und bei Erfolg der Frontendbenutzer-Datensatz in fe_user->user abgelegt. Hier ein einfaches Beispiel:

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
