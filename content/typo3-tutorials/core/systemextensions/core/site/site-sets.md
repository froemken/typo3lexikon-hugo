+++
title = "Site Sets"
date = 2024-05-12T20:12:26+01:00
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 13.1{{% /badge %}}

Mit Site Sets kann nun jede Extension eine oder mehrere vorkonfigurierte Site-Set-Definitionen zur Verfügung stellen. Diese Site-Set-Definitionen können Site-Settings, TypoScript aber auch PageTSConfig beinhalten und stehen im Sites-Modul vom TYPO3 Backend einer oder mehrerer Seiten als Auswahl zur Verfügung.

## Site Set Definition

Eine Site-Set-Definition beinhaltet die Konfiguration für Site-Settings, TypoScript und PageTSConfig und kann über das Site-Modul einer oder mehreren Sites zugewiesen werden. Site-Set-Definitionen werden im Verzeichnis `Configuration/Sets/` angelegt und durch einen Unterordner mit beliebigen Namen voneinander getrennt. Auf diese Weise ist es auch möglich pro Extension mehrere Site-Set-Definitionen anzulegen. Jeder dieser Unterordner muss eine `config.yaml` besitzen, die der Site-Set-Definition mindestens einen eindeutigen Namen und bevorzugt auch ein eindeutiges Label zuweist.

`[MyExt]/Configuration/Sets/Typo3LexikonBootstrap/config.yaml`

```yaml
# Site Set Name
# Ähnlich dem Package Namen von Composer: [Vendor]/[Package]
# Wird benötigt, um das Site Set eindeutlig zu identifizieren
# und Abhängigkeiten zu anderen Site Sets aufzulösen.
# Dieser Name spiegelt KEINE Extension wieder, sondern durch den Vendornamen nur den Anbieter einer Extension.
# Es gibt aus dem Namen hier KEINE Rückschlüsse darauf, aus welcher Extension die Site-Set-Definition zur Verfügung gestellt wurde.
name: stefanfroemken/bootstrap-set

# Dieser Label wird in der neuen Selectbox des Site-Moduls verwendet. Sollte möglichst eindeutig sein, um Dopplungen im Site-Modul zu vermeiden
label: Typo3lexikon Bootstrap Set

# Settings für die Webseite definieren
# Niemals Settings mit Punkt verschachteln!!! z.B. website.background.color
# Sonst klappen später die neuen Settings Definitionen nicht.
# Wenn ein Setting Sonderzeichen oder Leerzeichen beinhaltet, empfiehlt es sich
# den Wert in Anführungszeichen einzuwickeln.
settings:
  website:
    background:
      color: '#386492'

# Lade setup.typoscript, constants.typoscript, page.tsconfig und config.yaml von den Site Set Definitionen dieser oder auch anderer Extensions.
# Diese Abhängigkeiten werden vor dem eigenen Site Set geladen. 
dependencies:
  # Anhängigkeit zu einer Site-Set-Definition einer meiner eigenen Extensions
  - stefanfroemken/classic
  # Anhängigkeit zu einer Site-Set-Definition eines anderen Anbieters (Vendor)
  - in2code/powermail-default
```

## Site mit Site-Set verknüpfen

Im Site-Modul gibt es eine neue Selectbox, um eine oder mehrere Site-Set-Definitionen als Vorkonfiguration der Site auszuwählen. Diese Auswahl wird auch in der YAML Datei der Site-Konfiguration hinterlegt:

`config/sites/[site-identifier]/config.yaml`

```yaml
base: 'https://www.typo3lexikon.de/'
rootPageId: 1
dependencies:
  - stefanfroemken/bootstrap-set
```

Diese Abhängigkeit könnt ihr in dieser Datei natürlich auch manuell setzen.

## Settings Definitionen

Das ganz große Problem von TypoScript und TSConfig ist, dass jeder angegebene Wert immer nur ein String sein kann. Es obliegt allein dem Entwickler, diese Werte auszulesen und in den gewünschten Datentyp wie Integer umzuwandeln.

Mit Settings Definitionen möchte TYPO3 hier Abhilfe schaffen und stellt nun eine API zur Verfügung, mit der ihr zusätzliche beschreibende Definitionen für jedes einzelne Site-Setting hinzufügen könnt.

`EXT:my_ext/Configuration/Sets/[SetName]/settings.definitions.yaml`

```yaml
settings:
  website.background.color:
    # Keine Ahnung, wo das aktuell verwendet wird.
    label: 'Background color'
    # Keine Ahnung, wo das aktuell verwendet wird.
    description: 'This will validate the given color string'
    # Welche Validierung/Konvertierung soll durchgeführt werden?
    # [sysext]/core/Classes/Settings/Type/*
    type: color
    # Ein Standardwert als Fallback, falls in config.yaml kein Wert angegeben wurde
    default: '#129845'
```

### Definitionstypen

#### int

Prüft, ob der Wert bereits eine Ganzzahl ist oder als Ganzzahl interpretiert werden kann. Wenn ja, dann wird der String in eine Ganzzahl umgewandelt.

#### number

Prüft, ob der Wert bereits eine Ganzzahl oder Float ist oder der String als Ganzzahl oder Float interpretiert werden kann. Wenn ja, dann wird der String in eine Ganzzahl oder Float umgewandelt.

#### bool

Wenn der Wert bereits boolean ist, wird dieser direkt 1 zu 1 zurückgegeben.

Wenn der Wert eine Ganzzahl ist, dann wird für 0 `false` und für 1 `true` zurückgeliefert. 

Wenn der Wert ein String ist, dann wird für `true`, `false`, `yes`, `no`, `on`, `off`, `0`, und `1` der entsprechende boolesche Wert zurückgegeben.

#### string

Konvertiert nahezu alle Datentypen in einen String. Wenn ein Objekt angegeben wurde, dann muss dieses `Stringable` sein, ansonsten findet keine Konvertierung statt. Boolesche Werte werden in `true` und `false` konvertiert.

#### text

Exakt das Gleiche wir der `string` Typ. Verwendet es als Alias, falls jemand mit `String` nichts anfangen kann.

#### stringlist

Bei dem Wert muss es sich um ein Array handeln, dessen Array Keys bei 0 beginnen und sich pro Element um 1 erhöhen. Das `list` in diesem Typen leitet sich von der internen PHP Methode `array_is_list` ab und hat nichts damit zu tun, dass hier auch Komma separierte Listen konvertiert werden können.

Pro Array Eintrag wird der `string` Typ ausgeführt.

#### color

Prüft, ob der angegebene String als Farbcode interpretiert werden kann. Angaben, die mit `rgb`, `rgba` und `#` starten sind hier erlaubt. Für `#` Farbcodes wird z.B. geprüft, ob diese 3-, 6- oder 8-stellig sind.

## Site Settings

Anstatt Site Settings für Site-Set-Definitionen direkt in der `config.yaml` zu definieren, sollten diese zwecks besserer Struktur in der `settings.yaml`, die parallel zu der `config.yaml` anzulegen ist, abgelegt werden.

`[MyExt]/Configuration/Sets/Typo3LexikonBootstrap/settings.yaml`

```yaml
settings:
  website:
    background:
      color: '#386492'
```

Bitte setzt hier keine Standardwerte. Dafür sind die Settings Definitionen da.

## TypoScript

In dem Verzeichnis für die Site-Set-Definition, parallel zur `config.yaml`, können die TypoScript Dateien `setup.typoscript` und `constants.typoscript` angelegt werden. Diese werden dann automatisch in die TypoScript Struktur für die entsprechende Site eingebunden.

Großer Vorteil hier ist, dass diese TypoScript Dateien, wie durch die Abhängigkeiten (`dependencies`) definiert, entsprechend in die TypoScript Ladereihenfolge einordnen.

Innerhalb dieser beiden Seiten könnt ihr auch weiterhin mittels `@include` auf andere TypoScript Dateien verweisen:

`EXT:my_ext/Configuration/Sets/Typo3LexikonBootstrap/setup.typoscript`

```typo3_typoscript
@import 'EXT:my_ext/Configuration/TypoScript/Setup/*.typoscript'
```

## PageTSConfig

In dem Verzeichnis für die Site-Set-Definition, parallel zur `config.yaml`, kann mittels einer `page.tsconfig` Datei Standard PageTSConfig für eine oder mehrere Sites geladen werden.

Großer Vorteil hier ist, dass dieses PageTSConfig, wie durch die Abhängigkeiten (`dependencies`) definiert, entsprechend in die Ladereihenfolge für PageTSConfig einordnen. Eine manuelle Einordnung mittels `@include` ist nicht mehr Vonnöten.

## Konsole: Verfügbare Site-Sets

Die zur Verfügung stehenden Site-Set-Definitionen können über einen Shell Befehl ausgelesen werden: 

```shell
bin/typo3 site:sets:list
```

## Weitere Informationen

### Site

Die Site Settings können über das Site-Objekt ausgelesen werden:

```php
<?php

namespace StefanFroemken\SfCars\Service

use TYPO3\CMS\Core\Site\SiteFinder;

class WebsiteService
{
    public function __construct(
        protected readonly SiteFinder $siteFinder
    ) {}

    public function getBackgroundColor(): string
    {
        return $site->getSettings()->get('website.background.color');
    }
    
    protected function getSite(): Site
    {
        return $this->siteFinder->getSiteByPageId(123);
    }
}
```

Falls zu diesem Setting eine Settings-Definition existiert, wurde der zurückgelieferte Wert bereits validiert, konvertiert und, falls nicht gesetzt, der Standardwert verwendet.

### SetRegistry

Die `SetRegistry` ruft die gefundenen Site-Sets in geordneter Reihenfolge ab, so wie durch `dependencies` in `config.yaml` definiert. Bitte verwendet bevorzugt das `Site` Objekt, um an die benötigten Daten dranzukommen. Wenn ihr jedoch eine oder mehrere Site-Set-Definitionen in geordneter Reihenfolge, wie durch `dependencies` definiert abfragen müsst, dann ist `SetRegistry` die richtige Anlaufstelle. Um alle Site-Set-Definitionen auszulesen, verwendet bitte `SetCollector`.

#### getSets

Liest eine oder mehrere Site-Set-Definitionen inkl. deren Abhängigkeiten aus.

```php
<?php

namespace StefanFroemken\SfCars\Service

use TYPO3\CMS\Core\Site\Set\SetDefinition;
use TYPO3\CMS\Core\Site\Set\SetRegistry;

class WebsiteService
{
    public function __construct(
        protected readonly SetRegistry $setRegistry
    ) {}

    /**
     * @return list<SetDefinition>
     */
    public function getMyNeededSets(): array
    {
        return $this->setRegistry->getSets(
            'stefanfroemken/bootstrap', 
            'in2code/powermail'
        );
    }
}
```

#### hasSet

Prüft, ob eine Site-Set-Definition verfügbar ist.

```php
<?php

namespace StefanFroemken\SfCars\Service

use TYPO3\CMS\Core\Site\Set\SetRegistry;

class WebsiteService
{
    public function __construct(
        protected readonly SetRegistry $setRegistry
    ) {}

    public function hasBootstrapSet(): bool
    {
        return $this->setRegistry->hasSet('stefanfroemken/bootstrap');
    }
}
```

#### getSet

Liest eine Site-Set-Definition OHNE Abhängigkeiten aus.

```php
<?php

namespace StefanFroemken\SfCars\Service

use TYPO3\CMS\Core\Site\Set\SetDefinition;
use TYPO3\CMS\Core\Site\Set\SetRegistry;

class WebsiteService
{
    public function __construct(
        protected readonly SetRegistry $setRegistry
    ) {}

    public function getJustBootstrapSet(): SetDefinition
    {
        return $this->setRegistry->getSet('stefanfroemken/bootstrap');
    }
}
```

### SetCollector

TYPO3 kommt mit einem neuen `ServiceProvider`, der mit dem ersten Instanziieren des `SetCollector` alle Extensions durchgeht und alle gefundenen Site-Set-Definitionen ausliest.

```php
<?php

namespace StefanFroemken\SfCars\Service

use TYPO3\CMS\Core\Site\Set\SetDefinition;
use TYPO3\CMS\Core\Site\Set\SetCollector;

class WebsiteService
{
    public function __construct(
        protected readonly SetCollector $setCollector
    ) {}

    /**
     * @return array<string, SetDefinition>
     */
    public function getEverythingYouHave(): array
    {
        return $this->setCollector->getSetDefinitions();
    }
}
```

Das ist jedoch nicht der offizielle Weg, um an die Site-Set-Definitionen und deren Abhängigkeiten dranzukommen. Bitte greift über das `Site` Objekt auf die Konfiguration zu. Alternativ könnt ihr auch die `SetRegistry` verwenden, da nur diese die Site-Sets in der Reihenfolge verwaltet, wie durch die `dependency` Angabe deklariert.

Verwendet den `SetCollector` nur, wenn ihr alle Site-Set-Definitionen auslesen müsst. Abhängigkeiten werden hier nicht berücksichtigt.
