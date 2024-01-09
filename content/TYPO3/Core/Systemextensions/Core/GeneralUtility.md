+++
title = "GeneralUtility"
date = 2024-01-09T20:17:12+01:00
slug = "generalutility"
+++

Die `GeneralUtility` Klasse bringt diverse statische Methoden mit, um z.B. temporäre Dateien zuschreiben, um $_GET Parameter auszulesen, XML Strukturen in ein Array umzuwandeln, Pfade auf Gültigkeit zu prüfen, `EXT:` aufzulösen und vieles mehr.

## _GP()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 12.3{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 13.0{{% /badge %}}

Liest den angeforderten Parameter aus dem globalen Array `$_GET` aus. Falls der geforderte Parameter auch in dem globalen Array `$_POST` vorhanden ist, so wird der Parameter von `$_POST` zurückgeliefert.

Vorzugsweise sollte die Methode nur für einfache Parameter wie `&id=3` verwendet werden. Nicht jedoch für Arrays wie `tx_ext[prod]=5`. Ja, es geht auch mit Arrays, aber dafür sollte `GPmerged()` verwendet werden.

**URL**: `example.com/produkte/waschgel.html?size=XXL`

Veraltet. Bitte verwendet den TYPO3 Request:

```php
<?php

declare(strict_types=1);

namespace StefanFroemken\Sfproducts\UserFunc;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use TYPO3\CMS\Core\Http\HtmlResponse;
use TYPO3\CMS\Core\Utility\ArrayUtility;
use TYPO3\CMS\Core\Utility\GeneralUtility;


class TestGeneralUtility
{
    public function test(ServerRequestInterface $request): ResponseInterface
    {
        $value = $request->getParsedBody()['tx_scheduler'] ?? $request->getQueryParams()['tx_scheduler'] ?? null;
        
        return new HtmlResponse($value);
    }
}
```

**Ausgabe**: `XXL`

{{% notice style="info" title="Hinweis" icon="skull-crossbones" %}}
Wenn Ihr schon wisst in welchem Array sich Eure Variable befindet, dann verwendet bitte bevorzugt direkt die Methoden `_GET()` oder `_POST()` aus `GeneralUtility`.
{{% /notice %}}

## _GPmerged()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 12.2{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 13.0{{% /badge %}}

Liest den angeforderten Parameter aus dem globalen Array `$_GET` aus. Falls der geforderte Parameter auch in dem globalen Array `$_POST` vorhanden ist, so wird der Parameter von `$_POST` zurückgeliefert. Bitte beachtet, dass es sich bei dem angeforderten Parameter um ein Array handeln muss.

**URL**: `example.com/produkte/waschgel.html?tx_sfproducts_show[product]=16`

Veraltet. Bitte verwendet den TYPO3 Request:

```php
<?php

declare(strict_types=1);

namespace StefanFroemken\Sfproducts\UserFunc;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use TYPO3\CMS\Core\Http\JsonResponse;
use TYPO3\CMS\Core\Utility\ArrayUtility;
use TYPO3\CMS\Core\Utility\GeneralUtility;

class TestGeneralUtility
{
    public function test(ServerRequestInterface $request): ResponseInterface
    {
        $getMergedWithPost = $request->getQueryParams()['tx_sfproducts_show'];
        ArrayUtility::mergeRecursiveWithOverrule($getMergedWithPost, $request->getParsedBody()['tx_sfproducts_show']);
        
        return new JsonResponse($getMergedWithPost);
    }
}
```

**Ausgabe**:

```json
{"product":13}
```

## _GET()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 12.4{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 13.0{{% /badge %}}

Ohne mitgelieferten Argument liefert `_GET()` alle Parameter von dem globalen Array `$_GET` zurück. Mit übergebenen Argument wird nur der gewünschte Parameter aus dem `$_GET` Array zurückgegeben. Parameter, die kein Array sind, werden sicherheitshalber in Strings konvertiert. In früheren TYPO3 Versionen beinhaltete `_GET()` noch Logik für Magic Quotes, welche mit PHP 5.5 obsolet wurden.

Veraltet. Bitte verwendet den TYPO3 Request:

```php
<?php

declare(strict_types=1);

namespace StefanFroemken\Sfproducts\UserFunc;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use TYPO3\CMS\Core\Http\HtmlResponse;
use TYPO3\CMS\Core\Utility\ArrayUtility;
use TYPO3\CMS\Core\Utility\GeneralUtility;


class TestGeneralUtility
{
    public function test(ServerRequestInterface $request): ResponseInterface
    {
        $value = $request->getQueryParams()['tx_scheduler']) ?? null;
        
        return new HtmlResponse($value);
    }
}
```

## _POST()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 12.2{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 13.0{{% /badge %}}

Ohne mitgelieferten Argument liefert `_POST()` alle Parameter von dem globalen Array `$_POST` zurück. Mit übergebenen Argument wird nur der gewünschte Parameter aus dem `$_POST` Array zurückgegeben. Parameter, die kein Array sind, werden sicherheitshalber in Strings konvertiert. 

Veraltet. Bitte verwendet den TYPO3 Request:

```php
<?php

declare(strict_types=1);

namespace StefanFroemken\Sfproducts\UserFunc;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use TYPO3\CMS\Core\Http\HtmlResponse;
use TYPO3\CMS\Core\Utility\ArrayUtility;
use TYPO3\CMS\Core\Utility\GeneralUtility;

class TestGeneralUtility
{
    public function test(ServerRequestInterface $request): ResponseInterface
    {
        $value = $request->getParsedBody()['tx_scheduler']);
        
        return new HtmlResponse($value);
    }
}
```

## _GETset()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 9.5{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 10.0{{% /badge %}}

Veraltet, bitte erstellt eine Middleware, um dem TYPO3 Request mittels `$request = $request->withQueryParams([...]);` weitere Parameter hinzuzufügen.

Mit dieser Methode konntet ihr das globals Array `$_GET` modifizieren. Als erstes Argument habt ihr einen String oder ein Array angegeben. Wenn das zweite Argument leer blieb, dann hat das Array aus dem ersten Argument das komplette globale Array überschrieben. Wenn das zweite Argument jedoch ein String war, wurde nur der Teil von `$_GET` überschrieben, der dem 2ten Argument entsprach. Um tief verschachtelte Elemente zu überschreiben, konnte man die Pipe `|` als Separator verwenden:

```php
GeneralUtility::_GETset('Schokolade', 'produkte|kaltspeisen|eis');
```

**Ergebnis**:

```php
[
    "produkte" => [
        "kaltspeisen" => [
            "eis" => "Schokolade",
        ],
    ],
]
```

## GPvar()

{{% badge style="orange" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 4.6{{% /badge %}}

Veraltet. Verwendet bitte `_GP()`.

## GParrayMerged()

{{% badge style="orange" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 4.6{{% /badge %}}

Veraltet. Verwendet bitte `_GPmerged()`.

## removeXSS()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.2{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 8.2{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 9.0{{% /badge %}}

Veraltet. Hierbei ging es um eine Möglichkeit XXS (Cross Site Scripting) aus Texten zu entfernen. Leider war die Methode nicht zu 100 % sicher, weshalb diese entfernt wurde. Verwendet als Alternative bitte `htmlspecialchars()` im Kontext von HTML oder `GeneralUtility::quoteJSvalue()` im Kontext von JavaScript.
