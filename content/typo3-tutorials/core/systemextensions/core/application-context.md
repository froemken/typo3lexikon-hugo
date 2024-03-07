+++
title = "ApplicationContext"
date = 2024-03-08T00:08:34+01:00
slug = "ApplicationContext"
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 6.2{{% /badge %}}

In früheren TYPO3 Versionen wurden auftauchende Fehlermeldungen ungefiltert im Frontend ausgegeben und haben nicht selten auch sicherheitsrelevante Daten wie Datenbankzugänge, E-Mail-Adressen, SMTP-Zugänge und Ähnliches der Öffentlichkeit preisgegeben. Um dies zu unterbinden wurde der sogenannte "ApplicationContext" eingeführt. Diese Kontexte können TYPO3 in gewissermaßen vorkonfigurieren und können auch an diversen Stellen wie der Site-Konfiguration wieder abgefragt werden, um z.B. eine andere `baseVariant` zu provozieren.

TYPO3 wird von Haus aus mit 3 unterschiedlichen Modi ausgeliefert, die sich, wie folgt, auf das Verhalten von TYPO3 auswirken:

- **Production**
  - Nur Fehler werden protokolliert.
  - Es findet keine Protokollierung von veralteten (deprecated) Funktionsaufrufen statt.
  - Im Frontend erscheint im Fehlerfalle nur: `Oops, an error occurred! Code: {code}`.
  - Der Dependency-Injection Cache kann nur über den Flush Cache Button im Installtool geleert werden.
  - Das Aufrufen der Installtool-Menüpunkte erfordern die zusätzliche Eingabe eines Passwortes.
  - Nur Admins mit System-Maintainer-Berechtigung sehen die Menüpunkte des Installtools im TYPO3 Backend.
  - Dieser Modus ist der Performanteste und Sicherste.

- **Development**
  - Fehler und Warnungen werden protokolliert.
  - Veraltete (deprecated) Funktionsaufrufe werden in einer zusätzlichen Log-Datei protokolliert.
  - Im Frontend erscheint der Fehler mit dem Hinweis, wo, in welcher Datei dieser aufgetreten ist.
  - Im Backend werden beim Bearbeiten von Datensätzen hinter jedem Label auch das zugehörige Tabellenfeld in eckigen Klammern angezeigt.
  - Der Clear All Cache Button oben rechts leert auch den Dependency-Injection Cache.
  - Die Menüpunkte im Backend für das Installtool erfordern keine zusätzliche Passwort-Eingabe mehr.
  - Auch Admins ohne System-Maintainer-Berechtigung sehen die Menüpunkte für das Installtool.

- **Testing**
  - In diesem besonderen Modus wird das Cachen für das ClassLoading abgeschaltet, bzw. ist nur für einen Request gültig.

## Standard ApplicationContext

Im `SystemEnvironmentBuilder` liest TYPO3 unterschiedliche Umgebungsvariablen aus, um den gewünschten ApplicationContext zu ermitteln. Dabei gilt folgende Reihenfolge von hohe Priorität bis keine Priorität:

- Umgebungsvariable: `TYPO3_CONTEXT`
- Umgebungsvariable: `REDIRECT_TYPO3_CONTEXT`
- Umgebungsvariable: `HTTP_TYPO3_CONTEXT`
- ApplicationContext wird auf `Production` gesetzt

## ApplicationContext setzen

Der ApplicationContext wird ausgesprochen früh in TYPO3 ausgelesen. Sogar noch vor dem eigentlichen Bootstrap-Prozess von TYPO3. Heißt: Selbst wenn ihr den ApplicationContext in der `additional.php` (ehemals `AdditionalConfiguration.php`) setzt, dann ist es schon zu spät.

Am besten setzt ihr den ApplicationContext als Umgebungsvariable in der Serverkonfiguration. Alternativ braucht ihr eine Lösung die Umgebungsvariable auf PHP Ebene noch vor dem eigentlichen TYPO3-Aufruf zu setzen. Im Folgenden ein paar Ideen dazu.

Weitere Beispiele, wie man den ApplicationContext in TYPO3 setzen kann, könnt ihr der [TYPO3 Dokumentation](https://docs.typo3.org/m/typo3/reference-coreapi/main/en-us/ApiOverview/RequestLifeCycle/Bootstrapping.html#application-context) entnehmen.

### Apache

In der vhost Konfiguration als auch in der `.htaccess` kann der ApplicationContext wie folgt gesetzt werden:

```shell
SetEnv TYPO3_CONTEXT Development
```

{{% notice style="info" title="Wichtig" icon="exclamation" %}}
Bei einigen Hostern kann es sein, dass das Setzen von Umgebungsvariablen mittels der `.htaccess` unterbunden wurde. In diesem Falle müsst ihr euch anderweitig behelfen. Schaut mal bei den noch folgenden Beispielen vorbei.
{{% /notice %}}

### Nginx

```shell
fastcgi_param TYPO3_CONTEXT Development;
```

### .env (nur Composer)

Es gibt die Möglichkeit `.env` Dateien im Root-Verzeichnis eures Projektes einlesen zu lassen. Alle enthaltenen Werte werden dann als Umgebungsvariable zur Verfügung gestellt. Die Basis dafür bildet der [Symfony .env loader](https://packagist.org/packages/symfony/dotenv). Jedoch benötigt dieses Paket ein paar Methodenaufrufe, für die Initialisierung. Entweder ihr baut das selbst oder aber ihr verwendet den [HelHum .env connector](https://github.com/helhum/dotenv-connector). Dieser übernimmt für euch die Initialisierung des Symfony Packages.

#### Installation

```shell
composer req helhum/dotenv-connector
```

#### .env

Bitte achtet darauf keine Leerzeichen vor und nach dem `=` einzufügen

```shell
TYPO3_CONTEXT=Development/Dev1
```

### AutoLoader (nur Composer)

Falls euer TYPO3 mittels Composer aufgesetzt wurde, könnt ihr die [Composer files](https://getcomposer.org/doc/04-schema.md#files) Eigenschaft dazu missbrauchen, um mit jedem Request eine bestimmte Datei vor allen anderen Dateien zu laden.

{{< tabs >}}
{{% tab title="composer.json" %}}
```json
{
    "autoload": {
        "files": ["Source/Scripts/ApplicationContext.php"]
    }
}
```
{{% /tab %}}
{{% tab title="ApplicationContext.php" %}}
```php
<?php
putenv('TYPO3_CONTEXT=Development');
```
{{% /tab %}}
{{< /tabs >}}

### php.ini

In der `php.ini` gibt es die Möglichkeit pro Request eine bestimmte Datei immer zuerst zu laden. Die Eigenschaft lautet `auto_prepend_file`. Gebt hier den absoluten Pfad zu einer php-Datei mit folgendem Inhalt in eurem Hosting-Paket an.

```php
<?php
putenv('TYPO3_CONTEXT=Development');
```

{{% notice style="info" title="Wichtig" icon="exclamation" %}}
Einige Hoster bieten in ihren Konfigurationsmenüs keine Möglichkeit an diesen Wert zu setzen. Falls der Hoster Plesk verwendet könnt ihr versuchen diese Einstellung in einer `user.ini` im Document Root vorzunehmen. Alternativ auch mal `.user.ini`, `php.ini` und auch `.php.ini` ausprobieren. Im Zweifel den Hoster fragen, welche alternativen Möglichkeiten zur Verfügung stehen.
{{% /notice %}}

### index.php

Diese Fassung ist bitte der allerletzte Ausweg, wenn wirklich keine andere der vorherigen Fassungen funktioniert hat, oder euer Hoster euch enorm eingeschränkt hat. Denn diese Lösung klappt nur für das Frontend. Somit kann der angezeigt ApplicationContext im TYPO3 Backend von dem tatsächlich genutzten ApplicationContext im Frontend abweichen.

Erstellt eine eigene `index.php` im Document Root Verzeichnis und ladet dann von dort aus die eigentliche `index.php`.

```php
<?php
putenv('TYPO3_CONTEXT=Development');
require_once('typo3_src/index.php')
```

## Sub ApplicationContext

Der ApplicationContext kann mittels `/` weitere Male unterteilt werden. Hier ein paar Beispiele:

- Development/Dev1
- Development/Local/Ddev
- Testing/UnitTest
- Production/1und1

Ich nutze diese Unterteilung gerne, um für Kunden unterschiedliche Abnahme-Domains zu realisieren. Mittels der beschriebenen Möglichkeit der [Composer files](#composer) habe ich eine Datei angelegt, um je nach `HTTP_HOST` den ApplicationContext individuell zu setzen. In der Site-Konfiguration frage ich den gesetzten ApplicationContext wieder ab und setze damit eine andere base URI mithilfe der `baseVariants`.

- Development/Dev1 -> dev1.example.com
- Development/Dev2 -> dev2.example.com
- Development/Dev3 -> dev3.example.com
- Development/Dev4 -> dev4.example.com
- Development/Dev5 -> dev5.example.com

### Root ApplicationContext

Völlig egal, ob ihr mit nur einem ApplicationContext arbeitet oder mit einem per Slash mehrfach unterteiltem ApplicationContext: Der erste Teil ist immer der Root ApplicationContext und muss immer entweder `Production`, `Development` oder `Testing` sein, ansonsten funktionieren die Methoden `isProduction`, `isDevelopment` und `isTesting` nicht.

### Parent ApplicationContext

Dieser Abschnitt trifft nur zu, wenn ihr den ApplicationContext mittels Slashes in mehrere Abschnitte unterteilt habt. Der komplette restliche Wert hinter dem ersten Slash wird verwendet, um einen neuen ApplicationContext zu instanziieren. Den sogenannten Parent ApplicationContext. Hier seht ihr, wie sich der Context verschachtelt:

- ApplicationContext: `Development/Local/Ddev/Dev2`
  - Root ApplicationContext: `Development`
  - Parent ApplicationContext: `Local/Ddev/Dev2`
    - Root ApplicationContext: `Local`
    - Parent ApplicationContext: `Ddev/Dev2`
      - Root ApplicationContext: `Ddev`
      - Parent ApplicationContext: `Dev2`

Ich habe oben geschrieben, dass der Root ApplicationContext immer einer der 3 Werte sein muss: `Production`, `Development` oder `Testing`. Spätestens mit der 2ten Verschachtelung ist der Root ApplicationContext hier nun `Local` und mit der 3ten Verschachtelung `Ddev`. Es gibt in der PHP Klasse `ApplicationContext` keine Möglichkeit diesen Root ApplicationContext abzufragen! Ihr habt nur die Möglichkeit mittels `getParent()` auf den nächsten Parent ApplicationContext zuzugreifen und mittels `(string)getParent()` den bis dahin vollständigen ApplicationContext als String wiederzugeben. Also auf Ebene 2 kommt dann `Local/Ddev/Dev2` und auf Ebene 3 `Ddev/Dev2` zurück.

## ApplicationContext auslesen

TYPO3 selbst fragt den ApplicationContext bereits an den unterschiedlichsten Stellen ab, aber auch ihr könnt an diversen Stellen auf den ApplicationContext reagieren.

### PHP

{{% badge style="green" icon="angle-double-up" %}}TYPO3 6.2{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 10.2{{% /badge %}}
{{% badge style="red" icon="skull-crossbones" %}}TYPO3 11.0{{% /badge %}}

Früher holte man sich den ApplicationContext mit `GeneralUtility::getApplicationContext()`. Diese Methode wurde mittlerweile entfernt. Bitte verwendet die `Environment` Klasse. 

{{% badge style="green" icon="angle-double-up" %}}TYPO3 9.2{{% /badge %}}

Hier ein paar Beispiele, wie ihr mit der `Environment` Klasse auf den ApplicationContext zugreift:

{{< tabs >}}
{{% tab title="Root abfragen" %}}
```php
<?php

use TYPO3\CMS\Core\Core\Environment;

class SunnyProducts
{
    public function getDiscount(): int
    {
        if (Environment::getContext()->isDevelopment()) {
            return 20;
        }
        
        return 4;
    }
}
```
{{% /tab %}}
{{% tab title="Parent abfragen" %}}
```php
<?php

use TYPO3\CMS\Core\Core\Environment;

class SunnyProducts
{
    public function getDiscount(): int
    {
        if ((string)(Environment::getContext()->getParent() ?? '') === 'Local/Ddev') {
            return 20;
        }
        
        return 4;
    }
}
```
{{% /tab %}}
{{< /tabs >}}

### Site Config

{{% badge style="green" icon="angle-double-up" %}}TYPO3 9.2{{% /badge %}}

```yaml
baseVariants:
-
  base: 'https://dev-1.typo3lexikon.de/'
  condition: 'applicationContext == "Development/Dev1"'
```

### TypoScript

{{% badge style="green" icon="angle-double-up" %}}TYPO3 13.0{{% /badge %}}

```typo3_typoscript
if {
   value.data = applicationcontext
   equals = Development/Dev1
}
```

{{% badge style="green" icon="angle-double-up" %}}TYPO3 9.4{{% /badge %}}

```typo3_typoscript
[applicationContext == "Development/Dev1"]
page.10.wrap = <div style="border: 3px red solid;">|</div>
[END]
```

## Presets

{{% badge style="green" icon="angle-double-up" %}}TYPO3 6.2{{% /badge %}}

Wie anfangs erwähnt, wirkt sich der ApplicationContext auf bestimmte TYPO3 Einstellungen aus. Schauen wir uns dazu die Presets aus der EXT:install `Classes/Configuration/Context/*` etwas genauer an:

- `LivePreset` mit Priorität 50
- `DebugPreset` mit Priorität 50
- `CustomPreset` mit Priorität 10

Wie ihr seht, hat das `LivePreset` und das `DebugPreset` die gleiche Priorität. Welches also gewinnt? Es kommt auf den gesetzten ApplicationContext an. Wenn der `Production` ApplicationContext gesetzt ist, dann bekommt das `LivePreset` nochmals 20 Punkte mehr Priorität. Wenn der `Development` ApplicationContext gesetzt ist, dann bekommt das `DebugPreset` 20 weitere Punkte Priorität.

In jeder der 3 Dateien seht ihr welche TYPO3 Konfiguration zur Laufzeit wie zu überschreiben ist. Nochmal zur Verdeutlichung: Das Setzen des ApplicationContext verändert die TYPO3 Konfiguration zur Laufzeit. Er ändert nicht aktiv die `settings.php` oder `additional.php`!

{{% notice style="info" title="Wichtig" icon="exclamation" %}}
Im Installtool könnt ihr die Presets manuell setzen. Dies hat jedoch keine Auswirkung auf den gesetzten ApplicationContext. Setzt ihr das Preset auf `Debug`, dann werden die Konfigurationen aus der `DebugPreset.php` in die `settings.php` (ehemals `LocalConfiguration.php`) geschrieben. In den Systeminformationen von TYPO3 steht jedoch weiterhin der ApplicationContext auf `Production` (sofern mittels Umgebungsvariable nicht anders gesetzt).
{{% /notice %}}
