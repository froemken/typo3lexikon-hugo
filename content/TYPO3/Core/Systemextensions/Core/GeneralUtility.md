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

Hierbei ging es um eine Möglichkeit XXS (Cross Site Scripting) aus Texten zu entfernen. Leider war die Methode nicht zu 100 % sicher, weshalb diese entfernt wurde. Verwendet als Alternative bitte `htmlspecialchars()` im Kontext von HTML oder `GeneralUtility::quoteJSvalue()` im Kontext von JavaScript.

## gif_compress()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 7.1{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 8.0{{% /badge %}}

Hier konntet ihr den Pfad zu einer `*.gif` Datei angeben und unter Einbehaltung des gleichen Namens wurde diese `gif` Datei je nach Konfiguration in `GFX` mittels PHP `gd`-lib oder IM (ImageMagick) komprimiert. Die verwendete Komprimierung lieferte diese Methode als Rückgabewert.

## png_to_gif_by_imagemagick()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 7.1{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 8.0{{% /badge %}}

Übergebt den Pfad zu einer `*.png` Datei und diese wurde in eine `gif` Datei konvertiert, sofern unter `GFX` die Eigenschaft `png_to_gif` aktiv war.

## read_png_gif()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 7.1{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 8.0{{% /badge %}}

Wenn die Eingabedatei ein `gif` und das zweite Argument eine `0` ist oder die Eingabedatei ein `png` und das zweite Argument `1` ist, dann wird die Eingabedatei 1 zu 1 zurückgegeben. In allen anderen Fällen findet je nach zweitem Argument eine Konvertierung von `png` zu `gif` oder `gif` zu `png` statt. Bei einer stattfindenden Konvertierung liegt die Datei dann in `typo3temp/` mit dem Prefix `readPG_`.

## fixed_lgd()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 3.7{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 4.6{{% /badge %}}

Veraltet. Bitte verwende `fixed_lgd_cs()` mit einer positiven Anzahl Zeichen.

## fixed_lgd_pre()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 3.7{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 4.6{{% /badge %}}

Veraltet. Bitte verwende `fixed_lgd_cs()` mit einer negativen Anzahl Zeichen.

## fixed_lgd_cs()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.7{{% /badge %}}

Wenn der übergebene Text länger ist als die angegebene Anzahl Zeichen, dann werden die ersten X Zeichen des Textes übernommen und der Rest des Textes wird standardmäßig mit `...` ersetzt. Wenn die Anzahl Zeichen negativ ist, dann übernimmt diese Methode X Zeichen vom Ende des übergebenen Textes. Beim Abzählen der Zeichen werden Multi-Byte Zeichen unterstützt.

```php
<?php

// Ergebnis: Hallo ...
echo \TYPO3\CMS\Core\Utility\GeneralUtility::fixed_lgd_cs('Hallo zusammen', 6);

// Ergebnis: "...sammen"
echo \TYPO3\CMS\Core\Utility\GeneralUtility::fixed_lgd_cs('Hallo zusammen', -6);

// Ergebnis: Hallo zu###
echo \TYPO3\CMS\Core\Utility\GeneralUtility::fixed_lgd_cs('Hallo zusammen', -8, '###');
```

## breakTextForEmail()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 4.0{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 8.0{{% /badge %}}

Kürzt jede Zeile eines Textes für den Mailversand standardmäßig auf 76 Zeichen und bewegt den Rest der Zeile in die nächste Zeile. Das war damals für einige Mailclients vonnöten.

Veraltet: Bitte verwendet `wordwrap()` oder lasst dies die modernen Mail-Libraries wie Symfony Mailer erledigen.

## breakLinesForEmail()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 6.0{{% /badge %}}

Veraltet. Siehe `breakTextForEmail()`. War dafür zuständig je eine Zeile des Mailtextes ab 76 Zeichen umzubrechen.

## cmpIP()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}

Prüft eine angegebene IPv4 als auch IPv6 Adresse, ob diese den angegebenen Strukturen aus dem zweiten Argument entspricht. Ruft schlussendlich, je nach IPv4 oder IPv6, eine der beiden Methoden `cmpIPv4()` oder `cmpIPv6()` auf.

```php
<?php
// Ergebnis: false
echo GeneralUtility::cmpIP('43.65.72.43', '192.168.100.0/24'));

// Ergebnis: true
echo GeneralUtility::cmpIP(cmpIP('87.12.54.67', '87.12.0.0/16'));

// Ergebnis: true
echo GeneralUtility::cmpIP(cmpIP('32.54.78.21', '32.54.*.*'));

// Ergebnis: false
echo GeneralUtility::cmpIP(cmpIP('54.83.24.78', '192.168.*.*, 32.45.65/24, 32.56.125.*'));

// Ergebnis: true
echo GeneralUtility::cmpIP(cmpIP('54.83.24.78', '192.168.*.*, 54.0.0.0/8, 32.56.125.*'));
```

## cmpIPv4()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.0{{% /badge %}}

Prüft eine angegebene IPv4-Adresse, ob diese den angegebenen Strukturen aus dem zweiten Argument entspricht. Mehrere Strukturen müssen mit Komma `,` getrennt werden. Mit `/` kann die Abfrage auf eine Subnet-Mask erfolgen und mit `*` setzt ihr Platzhalter, um alle Werte einer Oktette zu erlauben.

Siehe `cmpIP()` für Beispiele

## cmpIPv6()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.0{{% /badge %}}

Prüft eine angegebene IPv6-Adresse, ob diese den angegebenen Strukturen aus dem zweiten Argument entspricht. Mehrere Strukturen müssen mit Komma `,` getrennt werden. Mit `/` kann die Abfrage auf die Subnet-Mask `/48` oder `/64` erfolgen. Andere Subnet-Masken resultieren in `false`. Ohne Angabe einer Subnet-Maske wird immer die vollständige IPv6 Adresse geprüft.

## IPv6Hex2Bin()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.0{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 10.4{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 11.0{{% /badge %}}

Veraltet. Bitte verwendet: `PHP:inet_pton($hex)`

## normalizeIPv6()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.0{{% /badge %}}

Konvertiert eine verkürzte IPv6 Adresse wieder in ihre normale Länge.

```php
<?php

// Ergebnis: 2041:0000:140F:0000:0000:0000:875B:131B
echo GeneralUtility::normalizeIPv6('2041:0000:140F::875B:131B');
```

## compressIPv6()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 10.4{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 11.0{{% /badge %}}

Konvertiert eine vollständige IPv6 Adresse in eine verkürzte Fassung.

Veraltet. Bitte verwendet: `PHP:inet_ntop(inet_pton($address))`

```php
<?php

// Ergebnis: 2041:0:140f::875b:131b
echo inet_ntop(inet_pton('2041:0000:140F:0000:0000:0000:875B:131B');
```

## validIP()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.2{{% /badge %}}

Prüft, ob die angegebene IPv4 oder IPv6 Adresse gültig ist.

Ruft eine der beiden Methoden `validIPv4` oder `validIPv6` auf.


## validIPv4()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.2{{% /badge %}}

Prüft, ob die angegebene IPv4-Adresse gültig ist.

## validIPv6()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.0{{% /badge %}}

Prüft, ob die angegebene IPv6-Adresse gültig ist.

## cmpFQDN()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}

Löst eine angegebene IP-Adresse zurück auf ihren Domainnamen auf und prüft das Ergebnis gegen einen oder mehrere kommaseparierte Domainnamen. Für vollständige Teile des Hostnamen kann `*` als Platzhalter verwendet werden. `*` kann nicht als Teil des Hostname verwendet werden: `pf.*seiten.cc`.

```php
<?php

// Ergebnis: true
echo GeneralUtility::cmpFQDN('8.8.8.8', '*.google');
```

## inList()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}

Prüft, ob der Wert aus dem zweiten Argument in der kommaseparierten Liste des ersten Arguments gefunden wurde. 

## rmFromList()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 11.3{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 12.0{{% /badge %}}

Entfernt den Eintrag vom ersten Argument aus der kommaseparierten Liste des zweiten Argumentes.

Wird TYPO3 intern seit TYPO3 10 nicht mehr verwendet. Der Extension Scanner zeigt Euch an, wo diese Methoden noch aufgerufen wird. Es gibt keinen Ersatz für diese Methode. Bitte versucht mit Arrays zu arbeiten oder kopiert Euch die Methode in Euren Quellcode.

## intInRange()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 6.0{{% /badge %}}

"Quetscht" eine angegebene Zahl in einen vorgegebenen Zahlenbereich.

Ab TYPO3 4.6 ist diese Methode veraltet und wurde in das `t3lib_utility_Math` Objekt überführt, welches dann wiederrum mit TYPO3 6.0 in das `MathUtility` überführt wurde.

Veraltet. Bitte verwendet: `MathUtility::forceIntegerInRange()`.

## intval_positive()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 6.0{{% /badge %}}

Konvertiert negative Zahlenwerte ins Positive. Auf `-6` wird `6`.

Ab TYPO3 4.6 ist diese Methode veraltet und wurde in das `t3lib_utility_Math` Objekt überführt, welches dann wiederrum mit TYPO3 6.0 in das `MathUtility` überführt wurde.

Veraltet. Bitte verwendet: `MathUtility::convertToPositiveInteger()`.

## int_from_ver()

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}
{{% badge style="red" icon="times-circle" %}}TYPO3 7.0{{% /badge %}}

Konvertiert eine Versionsnummer mit 3 Teilen in eine Zahl. Aus `4.12.3` wird `4012003`.

Ab TYPO3 4.6 ist diese Methode veraltet und wurde in das `t3lib_utility_VersionNumber` Objekt überführt, welches dann wiederrum mit TYPO3 6.0 in das `VersionNumberUtility` überführt wurde.

Veraltet. Bitte verwendet: `VersionNumberUtility::convertVersionNumberToInteger()`.

## md5int()
## uniqueList()
## split_fileref()
## dirname()
## modifyHTMLColor()
## modifyHTMLColorAll()
## rm_endcomma()
## danish_strtoupper()
## convUmlauts()
## shortMD5()
## testInt()
## isFirstPartOfStr()
## formatSize()
## convertMicrotime()
## splitCalc()
## calcPriority()
## calcParenthesis()
## htmlspecialchars_decode()
## deHSCentities()
## slashJS()
## rawUrlEncodeJS()
## rawUrlEncodeFP()
## validEmail()
## formatForTextarea()
## inArray()
## intExplode()
## revExplode()
## trimExplode()
## uniqueArray()
## removeArrayEntryByValue()
## implodeArrayForUrl()
## compileSelectedGetVarsFromArray()
## addSlashesOnArray()
## stripSlashesOnArray()
## slashArray()
## array_merge_recursive_overrule()
## array_merge()
## csvValues()
## get_tag_attributes()
## split_tag_attributes()
## implodeParams()
## wrapJS()
## xml2tree()
## array2xml()
## xml2array()
## xmlRecompileFromStructValArray()
## xmlGetHeaderAttribs()
## getURL()
## writeFile()
## mkdir()
## get_dirs()
## getFilesInDir()
## getAllFilesAndFoldersInPath()
## removePrefixPathFromList()
## fixWindowsFilePath()
## resolveBackPath()
## locationHeaderUrl()
## debug_ordvalue()
## view_array()
## print_array()
## debug()
## getThisUrl()
## linkThisScript()
## linkThisUrl()
## getIndpEnv()
## milliseconds()
## clientInfo()
## getFileAbsFileName()
## validPathStr()
## isAbsPath()
## isAllowedAbsPath()
## verifyFilenameAgainstDenyPattern()
## upload_copy_move()
## upload_to_tempfile()
## unlink_tempfile()
## tempnam()
## stdAuthCode()
## loadTCA()
## resolveSheetDefInDS()
## resolveAllSheetsInDS()
## callUserFunction()
## getUserObj()
## makeInstance()
## makeInstanceClassName()
## makeInstanceService()
## plainMailEncoded()
## quoted_printable()
## substUrlsInPlainText()
## makeRedirectUrl()
## freetypeDpiComp()
## devLog()
