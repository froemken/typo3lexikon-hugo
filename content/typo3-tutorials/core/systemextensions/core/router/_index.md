+++
title = "Router"
date = 2024-06-08T12:06:09+01:00
ordersectionsby = "linktitle"
alwaysopen = false
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 9.5{{% /badge %}}

Der Router von TYPO3 kümmert sich darum, aus unleserlichen, vielleicht sogar kryptisch aussehenden URLs eine viel besser lesbare und sogar SEO-optimierte URL zu generieren. Hier ein Beispiel:

**Vorher**

`https://www.typo3lexikon.de/?id=176&L=1&tx_news_pi1[news]=177`

* Scheme: `https`
* Domain: `www.typo3lexikon.de`
* Query: `id=176&L=1&tx_news_pi1[news]=177`

**Nachher**:

`https://www.typo3lexikon.de/aktuelles/news-detail/typo3-13-released.html`

* Scheme: `https`
* Domain: `www.typo3lexikon.de`
* Pfad: `/aktuelles/news-detail/typo3-13-released.html`

Mittels eines Routers kann so der Query-Part einer URL vollständig oder auch nur zu Teilen in einen URL-Pfad umgebaut werden.

## Slug

Bei einem Slug handelt es sich um eine eindeutige und URL sichere Repräsentation eines Element-Titels. Ein "Element" kann im Grunde alles sein: ein Datensatz, eine Rückgabe von einem Web-Request oder auch ein Element aus einer XML-Datei.

Im Folgenden ein Beispiel für einen News-Datensatz mit dem Titel "Wiedereröffnung von Müller&Breuer in Wipperfürth". Die Leerzeichen, die Umlaute und das `&`-Zeichen sind für diesen News-Titel nicht URL sicher und könnte mittels PHP wie folgt aufbereitet werden:

```php
$safeUrl = rawurlencode('Wiedereröffnung von Müller&Breuer in Wipperfürth');
// $safeUrl: Wiederer%C3%B6ffnung%20von%20M%C3%BCller%26Breuer%20in%20Wipperf%C3%BCrth
```

Damit ist die URL zwar URL sicher, aber noch lange nicht lesbar. Eine andere Möglichkeit, die URL aufzubereiten, besteht darin, Leerzeichen und diverse Sonderzeichen durch Bindestriche zu ersetzen, alles kleinzuschreiben und Umlaute von ihrer UTF8-Darstellung in ASCII umzuwandeln. Der Titel der News würde dann folgendermaßen aussehen

`wiedereroeffnung-von-mueller-breuer-in-wipperfuerth`

Dieser Slug kann nun als Segment des URL-Pfades verwendet werden. Dazu später mehr.

`https://www.typo3lexikon.de/aktuelles/news-detail/wiedereroeffnung-von-mueller-breuer-in-wipperfuerth/`

## Slugs und Seiten in TYPO3

Mit TYPO3 Version 9.5 gibt es für Seiten-Datensätze (Tabelle `pages`) ein neues Feld "URL Segment" (Tabellenspalte: `slug`). Mittels Upgrade-Wizard wird dieses Feld für alle Seiten befüllt und sogar alte Slugs von der Community Extension `realurl` mit berücksichtigt. Da Seiten-Slugs eindeutig sein müssen, kann es im Rahmen des Upgrade-Wizards passieren, dass eine Ordner-Seite "Produkte" den Slug "produkte" und die Seite "Produkte" auf der gleichen Ebene den Slug "produkte-1" erhält. In diesem Fall wäre es schöner, wenn die Seite den Slug "produkte" und der Ordner den Slug "produkte-1" erhält. Es obliegt jedoch dem Editor oder Integrator darauf zu achten und dies entsprechend zu ändern.

Verschachtelte Seiten werden pro Ebene im TYPO3 Seitenbaum in der URL mittels Slash getrennt ausgegeben:

```text
Root Page
  - Produkte
    - Handys
      - Samsung
```

Wenn Slugs selbst einen oder mehrere Slashs beinhalten, dann werden diese als "Kombinierte Slugs" bezeichnet. Als Resultat der oberen Struktur lautet der Kombinierte Slug demnach: `produkte/handys/samsung`

{{% notice style="info" %}}
Unter der Haube von TYPO3 werkelt zwar der Router von Symfony, aber für das Auflösen des URL-Pfades zurück zur TYPO3 Seite wird Symfony nicht verwendet. Es wird einfach ein trim-explode auf den Slash des vollständigen URL-Pfades gemacht und von hinten so lange ein Segment entfernt, bis eine Seite mit entsprechendem Slug gefunden wurde. Erst dann bereitet TYPO3 eine Konfiguration für den Symfony Router vor. Dieser wird dann später im Bereich `Aspects` und `RouteEnhancer` nochmal interessant, wenn Slugs aus Extensions die Slugs von TYPO3 Seiten ergänzen.
{{% /notice %}}

Alle Link-Generierungen über die TYPO3 API erzeugen nun eine URL auf Basis des Slugs der aufgerufenen TYPO3 Seite. URLs mit enthaltener Seiten-UID `?id=126` werden nicht mehr generiert! Dieser `id` Parameter sollte somit nicht mehr verwendet werden, da er in zukünftigen TYPO3 Versionen rausfliegen wird. Gerade im Bereich JavaScript sollte darauf geachtet werden von jetzt an eine TYPO3 Seite über deren Slug aufzurufen.

## Slug-Felder im TCA konfigurieren

In TYPO3 wurde ein neuer TCA Typ `slug` eingeführt, mit dem automatisch oder aber auch manuell ein Slug für einen Datensatz erstellt werden kann.

### appearance

Mit dieser Konfiguration kann auf das Aussehen des Slug-Feldes im TYPO3 Backend eingewirkt werden.

| Parameter | Erklärung | 
|-----------|-----------|
| prefix | Hierbei handelt es sich um den Bereich vor dem Slug-Eingabefeld. Üblicherweise steht dort die Domain drin. Dieser Bereich kann mittels einer UserFunc individuell gestaltet werden. Im Beispiel unten muss die Methode `getPrefix` einen String zurückgeben. |

#### Beispiel

```php
[
    'columns' => [
        'path_segment' => [
            'label' => 'Pfad Segment (Slug)',
            'config' => [
                'type' => 'slug',
                'size' => 50,
                'generatorOptions' => [
                    'fields' => ['title'],
                    'fieldSeparator' => '-',
                ],
                'appearance' => [
                    'prefix' => StefanFroemken\SitePackage\UserFunction\SlugUserFunction::class . '->getPrefix',
                ],
                'fallbackCharacter' => '-',
                'eval' => 'unique',
                'default' => '',
            ],
        ],
    ],
]
```

### eval

Mit `eval` wird geprüft wo, in welchem Rahmen ein Slug eindeutig sein muss.

**unique**

Der Slug muss auf innerhalb der kompletten TYPO3 Instanz eindeutig sein. Dies ist die beste Option, wenn von einer Root-Seite auf einen Datensatz einer anderen Root-Seite verwiesen werden soll.

**uniqueInSite**

Der Slug muss nur innerhalb einer Root-Seite eindeutig sein. Verlinkungen auf Slugs anderer Root-Seiten ist damit nicht mehr möglich.

**uniqueInPid**

Damit kann der gleiche Slug sogar innerhalb einer Root-Seite mehrfach vorkommen. Der Slug ist nur noch pro Seiten UID eindeutig. Nicht zu empfehlen, da es entsprechendes Know-How in der späteren Konfiguration erfordert.

#### Beispiel

```php
[
    'columns' => [
        'path_segment' => [
            'label' => 'Pfad Segment (Slug)',
            'config' => [
                'type' => 'slug',
                'size' => 50,
                'generatorOptions' => [
                    'fields' => [
                        'title',
                    ],
                    'fieldSeparator' => '-',
                ],
                'fallbackCharacter' => '-',
                'eval' => 'unique',
                'default' => '',
            ],
        ],
    ],
]
```

### fallbackCharacter

Innerhalb `generatorOptions` gibt es die Möglichkeit mittels `replacements` Zeichen oder auch ganze Wörter zu ersetzen. Alle Zeichen, die nicht durch diese `replacements` abgedeckt sind und nicht sicher für die URL sind (Leerzeichen, `&`, ...), werden mit diesem Standardzeichen ersetzt.

### generatorOptions

Mit diesen Optionen wird angegeben welche Felder, welcher Separator und welche Zeichen-Ersetzungen an den Slug Generator (PHP Klasse: `SlugHelper`) zu übergeben sind.

| Parameter | Erklärung | 
|-----------|-----------|
| fields | Angabe eines oder mehrerer Tabellenspalten, die für die Slug-Generierung verwendet werden sollen. Die Inhalte dieser Spalten werden dann mittels der Angabe aus `fieldSeparator` verknüpft. Durch Angabe eines Spaltennamens als Array `['subtitle']`, kann dieser Spaltenname als optional gekennzeichnet werden. Wenn nicht befüllt, dann wird der nächste angegebene Spaltenname für die Generierung herangezogen. Bitte verwendet nicht die Spalte `uid`, da diese gerade bei neuen Datensätzen noch nicht befüllt ist und somit `new[HashWert]` in der URL erscheinen würde. |
| fieldSeparator | Wenn mehrere Spaltennamen aus `fields` für die Slug-Generierung verwendet werden sollen, kann hiermit ein Trenner-Zeichen für die Spalten-Inhalte angegeben werden. |
| prefixParentPageSlug | Wenn `true`, dann wird der Slug der Eltern-Seite dem Slug vorangestellt. Nur gültig für Datensätze der Tabelle `pages`. |
| replacements | Hiermit können einzelne Zeichen als auch ganze Wörter vor der eigentlichen Slug-Generierung noch manuell ausgetauscht werden. |
| postModifiers | Sollten die bisherigen Optionen für die gewünschte Slug-Generation nicht ausreichen, kann hier mittels Hooks nachjustiert werden. |

```php
[
    'columns' => [
        'path_segment' => [
            'label' => 'Pfad Segment (Slug)',
            'config' => [
                'type' => 'slug',
                'size' => 50,
                'generatorOptions' => [
                    'fields' => [
                        // Wenn subtitle befüllt, dann verwende "subtitle" Inhalt.
                        // Wenn subtitle leer, dann nutze "title" Inhalt
                        ['subtitel', 'title'],
                        'date'
                    ],
                    // Slug-Format: subtitleOderTitle-date
                    'fieldSeparator' => '-',
                    // Ersetze alle Vorkommen von "foo" durch "bar"
                    // Ersetze alle Slashs durch Unterstriche
                    'replacements' => [
                        'foo' => 'bar'
                        '/' => '_'
                    ],
                ],
                'fallbackCharacter' => '-',
                'eval' => 'unique',
                'default' => '',
            ],
        ],
    ],
]
```

### prependSlash

Standard: `false`

Wenn aktiviert, dann wird dem Slug ein Slash vorangestellt. Nur sinnvoll, wenn man verschachtelte Elemente in der URL nachstellen will. Zum Beispiel verschachtelte Kategorien.

Für die Tabelle `pages` Spalte `slug` ist diese Option `true` und kann nicht deaktiviert werden.

## Performance

Die URL Generierung mittels `typolink` ist sehr umfangreich und dadurch verhältnismäßig langsam. Dank des neuen Routers können URLs nun auch direkt gebaut werden. Diese Möglichkeit hier braucht kein TypoScript und somit auch kein TSFE (PHP Klasse `TypoScriptFrontendController`). Dadurch verliert man jedoch alles an Kompatibilität. Weder `t3://page` noch `t3://file` kann hier verwendet werden. Auch sämtliches an Hooks und open-window JavaScript kann nicht verwendet werden. Verwendet diese Möglichkeit somit bitte mit Bedacht.

```php
<?php
declare(strict_types=1);

namespace StefanFroemken\SitePackage\Helper;

use TYPO3\CMS\Core\Site\Entity\Site;
use TYPO3\CMS\Core\Site\SiteFinder;
use TYPO3\CMS\Core\Utility\GeneralUtility;

class SiteHelper
{
    public function __construct(protected readonly SiteFinder $siteFinder)
    {}

    public function buildUrl(Site $site, int $targetPageUid, array $arguments = []): string
    {
        return (string)$site
            ->getRouter()
            ->generateUri($targetPageUid, $arguments);
    }

    public function getSiteByPageUid(int $pageUid): Site
    {
        return $this->siteFinder->getSiteByPageId($pageUid);
    }
}

$siteHelper = GeneralUtility::makeInstance(SiteHelper::class);
$site = $siteHelper->getSiteByPageUid(123);
var_dump($siteHelper->buildUrl(
    $site,
    43782,
    [
        'tx_news_pi1' => [
            'news' => 2934,
        ]   
    ] 
));
```

## Routen für Extensions

Wie vorhin erklärt, bildet nur der vordere Teil des URL-Pfades den Slug für TYPO3 Seiten ab. Der hintere Teil der URL, sofern vorhanden und konfiguriert, bildet Pfade für Extensions ab. Die Konfiguration dazu befindet sich in der `config.yaml` im `sites/[site-identifier]/` Ordner auf dem Webserver und kann aktuell noch nicht über das Sites-Modul im TYPO3 Backend konfiguriert werden. Diese Datei muss manuell bearbeitet werden. Die Routen für Extensions werden in der `config.yaml` im Abschnitt `routeEnhancers` konfiguriert. Siehe dazu die folgenden Abschnitte `Route Enhancer` und `Aspect`.

### Route Enhancer

Mit einem [Route Enhancer](/typo3-tutorials/core/systemextensions/core/router/enhancer/) wird eine Route (oder auch mehrere) definiert, wo auf welcher Seite diese Gültigkeit und welche Bedingungen ein URL-Parameter zu erfüllen hat.

### Aspect

Mit einem [Aspect](/typo3-tutorials/core/systemextensions/core/router/aspect/) kann ein Mapping des URL-Parameters auf einen anderen Wert oder ein anderes Format erfolgen.
