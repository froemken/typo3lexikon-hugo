+++
title = "Passwort generieren"
date = 2024-01-09T20:49:47+01:00
aliases = ["passwort-generieren.html"]
+++

Im Folgenden zeige ich euch meine Vorgehensweise, um im TYPO3-Backend einen Button zur Passwort-Generierung für Backend-Benutzer einzubinden. Der Button soll rechts vom Passwortfeld erscheinen. Bei jedem Klick wird per AJAX ein neues Passwort erzeugt, in das Eingabefeld eingesetzt und zusätzlich im Klartext in einem Panel unterhalb des Feldes angezeigt.

Lösungen für das Frontend gibt es im Netz wie Sand am Meer. Im Backend läuft die FormEngine jedoch nach ganz eigenen Regeln. Mir war wichtig, die Umsetzung möglichst sauber und Core-konform umzusetzen.

## Einen Wizard einfügen

Zunächst müsst ihr für das Passwortfeld der Tabelle `be_users` einen zusätzlichen Wizard hinzufügen. Erstellt in eurer Extension bzw. eurem Sitepackage die Datei `Configuration/TCA/Overrides/be_users.php` mit folgendem Inhalt:

```php
<?php
defined('TYPO3') || die('Access denied.');

$GLOBALS['TCA']['be_users']['columns']['password']['config']['wizards'] = [
    'generatePassword' => [
        'type' => 'userFunc',
        'userFunc' => \Vendor\MyExt\Hooks\GeneratePassword::class . '->render',
    ],
];
```

Mit dem TCA-Typ `userFunc` und der Angabe einer PHP-Klasse könnt ihr euch komplett selbst um das Rendering des Wizards kümmern. Erstellt nun die entsprechende Datei `Classes/Hooks/GeneratePassword.php`:

```php
<?php

namespace Vendor\MyExt\Hooks;

use TYPO3\CMS\Backend\Form\Element\AbstractFormElement;
use TYPO3\CMS\Core\Imaging\IconFactory;
use TYPO3\CMS\Core\Utility\GeneralUtility;
use TYPO3\CMS\Extbase\Utility\LocalizationUtility;

class GeneratePassword
{
    /**
     * Render a wizard to generate a password
     *
     * @param array $parentArray
     * @param AbstractFormElement $formElement
     * @return string
     */
    public function render(array $parentArray, AbstractFormElement $formElement): string
    {
        // Structure of the panel to show the password after AJAX request
        $parentArray['item'] .= sprintf(
            '<div class="panel panel-default myExtGeneratedPassword" style="margin-top: 5px;">
                <div class="panel-heading">%s</div>
                <div class="panel-body"><code></code></div>
            </div>',
            LocalizationUtility::translate('generatedPassword', 'myExt')
        );

        // Render the button
        /** @var IconFactory $iconFactory */
        $iconFactory = GeneralUtility::makeInstance(IconFactory::class);
        return sprintf(
            '<a href="#" class="btn btn-default myExtGeneratePassword" data-itemuid="%s" data-itemname="%s" title="Generate password">%s</a>',
            $parentArray['uid'],
            $parentArray['itemName'],
            $iconFactory->getIcon('actions-document-synchronize')
        );
    }
}
```

Was man dem Code nicht direkt ansieht: TYPO3 ruft die Methode `render()` intern mittels `GeneralUtility::callUserFunction()` auf. Das Besondere dabei: Alle Parameter werden als Referenz übergeben. Das eröffnet zwei Möglichkeiten gleichzeitig:

Im Array `$parentArray` befindet sich unter dem Schlüssel `item` bereits das vollständige HTML des Passwortfeldes. In meinem Beispiel hänge ich dort ein leeres Bootstrap-Panel mit einer übersetzten Überschrift an. In den Panel-Body wird später per JavaScript das generierte Passwort im Klartext eingesetzt.

Den eigentlichen Wizard-Button liefert ihr über den Return-Wert der Funktion zurück. Ich erstelle hier einen Button mit dem Synchronisations-Icon aus der `IconFactory`. Wichtig sind die HTML5-Data-Attribute `data-itemuid` und `data-itemname`. Darüber kann JavaScript das Zielfeld im Formular eindeutig identifizieren. Das ist besonders dann essenziell, wenn mehrere Backend-Benutzer im gleichen Formular auf einmal bearbeitet werden (beispielsweise bei `inline`-Verknüpfungen). Ohne diese Zuordnung würdet ihr mit einem Klick versehentlich alle Passwortfelder auf der Seite überschreiben.

Nach dem Speichern solltet ihr den Cache im Backend leeren. Der Wizard-Button sollte danach am Passwortfeld zu sehen sein.

## Vorbereitungen für das RequireJS-Modul

Dieser Teil hat bei der Entwicklung ordentlich Zeit gekostet. Es gibt zwar verschiedene Hooks, über die man JavaScript mithilfe des `PageRenderer` einbinden könnte, jedoch zeigt sich in der Praxis ein Detailproblem:

Es ist ein riesiger Unterschied, ob ein Datensatz direkt im Web-Modul *Liste* bearbeitet wird oder als verschachtelter Kind-Datensatz über den TCA-Typ `inline` geladen wird. Bei `inline`-Feldern wird das HTML nachträglich per AJAX nachgeladen. Wenn das JavaScript global eingebunden ist, kennt es die neu nachgeladenen DOM-Elemente zunächst nicht. Binde ich das JavaScript wiederum direkt im `be_users`-Datensatz ein, funktioniert es zwar beim direkten Bearbeiten, aber nicht beim Nachladen über `inline`. Das JavaScript wird im AJAX-Response zwar geliefert, aber vom Browser nicht ausgeführt.

Bei der Analyse der AJAX-Responses fiel mir im JSON-Objekt das Attribut `scriptCall` auf.

Im `FormInlineAjaxController` wird dieser Wert erstellt und über `mergeChildResultIntoJsonResult()` je nach Eingabefeld befüllt. Jedes Eingabefeld in TYPO3 kann über den Array-Schlüssel `requireJsModules` RequireJS-Module an diesen `scriptCall` übermitteln. Die FormEngine von TYPO3 bietet jedoch keinen Hook an, um dieses Array nachträglich zu erweitern. In Abstimmung mit dem Core bleibt hier der saubere Weg: Wir überschreiben das Rendering des Passwortfeldes über einen eigenen `renderType`.

Fügt dazu in eurer `ext_localconf.php` folgende Zeilen ein:

```php
<?php
defined('TYPO3') || die('Access denied.');

// Add custom form elements to inject requireJSmodule for password generation
$GLOBALS['TYPO3_CONF_VARS']['SYS']['formEngine']['nodeRegistry'][1454580921] = [
    'nodeName' => 'myExtPassword',
    'priority' => 70,
    'class' => \Vendor\MyExt\Form\Element\InputTextElement::class,
];

$GLOBALS['TYPO3_CONF_VARS']['SYS']['formEngine']['nodeRegistry'][1454581671] = [
    'nodeName' => 'myExtRsaPassword',
    'priority' => 70,
    'class' => \Vendor\MyExt\Form\Element\RsaInputElement::class,
];
```

Damit werden zwei neue Node-Namen in der `nodeRegistry` registriert. Zwei deshalb, weil wir je nach Status der Extension `rsaauth` eine unterschiedliche Render-Klasse benötigen. Die Weiche setzen wir in der Datei `Configuration/TCA/Overrides/be_users.php`:

```php
<?php
defined('TYPO3') || die('Access denied.');

if (\TYPO3\CMS\Core\Utility\ExtensionManagementUtility::isLoaded('rsaauth')) {
    $GLOBALS['TCA']['be_users']['columns']['password']['config']['renderType'] = 'myExtRsaPassword';
} else {
    $GLOBALS['TCA']['be_users']['columns']['password']['config']['renderType'] = 'myExtPassword';
}
```

Durch diese Angabe übernimmt eure eigene Klasse das Rendering des Passwortfeldes.

Kopiert dazu die beiden Original-Klassen aus dem Core in eure Extension:

- `sysext/backend/Classes/Form/Element/InputTextElement.php`
- `sysext/rsaauth/Classes/Form/Element/RsaInputElement.php`

In der Extension liegt die Datei beispielsweise unter `Classes/Form/Element/InputTextElement.php`.

Passt den Namespace an eure Extension an und lasst eure Klasse von der jeweiligen Core-Klasse erben:

```php
<?php

namespace Vendor\MyExt\Form\Element;

use TYPO3\CMS\Core\Imaging\IconFactory;
use TYPO3\CMS\Core\Utility\GeneralUtility;

/**
 * Generation of TCEform elements of the type "input type=text"
 */
class InputTextElement extends \TYPO3\CMS\Backend\Form\Element\InputTextElement
{
    /**
     * Render single-line input form field with RequireJS module injection
     *
     * @return array As defined in initializeResultArray() of AbstractNode
     */
    public function render(): array
    {
        $resultArray = parent::render();
        $resultArray['requireJsModules'][] = 'TYPO3/CMS/MyExt/GeneratePassword';
        return $resultArray;
    }
}
```

Dasselbe Prinzip wenden wir bei der Klasse `RsaInputElement` an:

```php
<?php

namespace Vendor\MyExt\Form\Element;

/**
 * Generation of RSA password input elements with RequireJS module injection
 */
class RsaInputElement extends \TYPO3\CMS\Rsaauth\Form\Element\RsaInputElement
{
    /**
     * Render RSA password field with RequireJS module injection
     *
     * @return array
     */
    public function render(): array
    {
        $resultArray = parent::render();
        $resultArray['requireJsModules'][] = 'TYPO3/CMS/MyExt/GeneratePassword';
        return $resultArray;
    }
}
```

Indem wir `parent::render()` aufrufen und lediglich das RequireJS-Modul an `$resultArray['requireJsModules']` anhängen, vermeiden wir redundanten Code und halten die Lösung wartbar.

## Das JavaScript als RequireJS-Modul

TYPO3 bringt RequireJS mit und sucht bei Modulen mit dem Prefix `TYPO3/CMS/` in den Verzeichnissen der installierten Extensions. Legt die Datei `Resources/Public/JavaScript/GeneratePassword.js` in eurer Extension an:

```javascript
/**
 * Module: TYPO3/CMS/MyExt/GeneratePassword
 */
define("TYPO3/CMS/MyExt/GeneratePassword", ["jquery"], function($) {
    $(function() {
        // Hide all password panels initially
        $("div.myExtGeneratedPassword").hide();

        // Trigger AJAX call on button click
        $(document).on("click", "a.myExtGeneratePassword", function(event) {
            event.preventDefault();
            var itemName = $(this).data("itemname");
            var itemUid = $(this).data("itemuid");

            $.ajax({
                url: TYPO3.settings.ajaxUrls['myExtGeneratePassword'],
                dataType: 'text',
                cache: false,
                success: function(response) {
                    // Update form field and cleartext panel
                    $("[data-formengine-input-name='" + itemName + "']").val(response);
                    $("input[name='" + itemName + "']")
                        .siblings("div.myExtGeneratedPassword")
                        .show()
                        .find("div.panel-body code")
                        .text(response);

                    TBE_EDITOR.fieldChanged('be_users', itemUid, 'password', itemName);
                }
            });
        });
    });
});
```

Über die `data`-Attribute des Wizards steuern wir exakt das passende Passwortfeld an und aktualisieren dessen Wert. Zusätzlich fügen wir das generierte Passwort in den Body des Nachrichtenselements ein und signalisieren TYPO3 über `TBE_EDITOR.fieldChanged()`, dass sich der Feldinhalt geändert hat.

## Das AJAX-Script im Backend

Backend-AJAX-Routen werden in der `ext_localconf.php` registriert:

```php
<?php
defined('TYPO3') || die('Access denied.');

\TYPO3\CMS\Core\Utility\ExtensionManagementUtility::registerAjaxHandler(
    'myExtGeneratePassword',
    \Vendor\MyExt\Ajax\GeneratePassword::class . '->generate',
    true
);
```

Über die Kennung `myExtGeneratePassword` stellt TYPO3 in der JavaScript-Umgebung automatisch die passende URL unter `TYPO3.settings.ajaxUrls['myExtGeneratePassword']` inklusive Sicherheits-Tokens bereit.

Erstellt nun die AJAX-Controller-Klasse `Classes/Ajax/GeneratePassword.php`:

```php
<?php

namespace Vendor\MyExt\Ajax;

use Vendor\MyExt\Configuration\ExtConf;
use TYPO3\CMS\Core\Http\AjaxRequestHandler;
use TYPO3\CMS\Core\Utility\GeneralUtility;

class GeneratePassword
{
    /**
     * Generate password AJAX endpoint
     *
     * @param array $ajaxParameters
     * @param AjaxRequestHandler $ajaxRequestHandler
     * @return void
     */
    public function generate(array $ajaxParameters, AjaxRequestHandler $ajaxRequestHandler): void
    {
        /** @var ExtConf $extConf */
        $extConf = GeneralUtility::makeInstance(ExtConf::class);
        $ajaxRequestHandler->setContent([
            $this->generateStrongPassword(
                $extConf->getPasswordLength(),
                $extConf->getAllowedPasswordChars()
            )
        ]);
    }

    /**
     * Generate a strong password with configurable character sets
     *
     * @param int $length
     * @param string $availableSets
     * @return string
     */
    public function generateStrongPassword(int $length = 12, string $availableSets = 'luds'): string
    {
        $sets = [];
        if (str_contains($availableSets, 'l')) {
            $sets[] = 'abcdefghjkmnpqrstuvwxyz';
        }
        if (str_contains($availableSets, 'u')) {
            $sets[] = 'ABCDEFGHJKMNPQRSTUVWXYZ';
        }
        if (str_contains($availableSets, 'd')) {
            $sets[] = '23456789';
        }
        if (str_contains($availableSets, 's')) {
            $sets[] = '!@#$%&*?';
        }

        $all = '';
        $password = '';
        foreach ($sets as $set) {
            $password .= $set[array_rand(str_split($set))];
            $all .= $set;
        }

        $allArray = str_split($all);
        $remainingLength = $length - count($sets);
        for ($i = 0; $i < $remainingLength; $i++) {
            $password .= $allArray[array_rand($allArray)];
        }

        return str_shuffle($password);
    }
}
```

## Das Passwort konfigurierbar machen

Über die Datei `ext_conf_template.txt` im Root-Verzeichnis eurer Extension stellt ihr Konfigurationsoptionen für den Extension-Manager bereit:

```typo3_typoscript
# cat=password; type=int+; label = LLL:EXT:myExt/Resources/Private/Language/ExtConf.xlf:passwordLength
passwordLength = 12
# cat=password; type=boolean; label = LLL:EXT:myExt/Resources/Private/Language/ExtConf.xlf:passwordUseUpperCase
passwordUseUpperCase = 1
# cat=password; type=boolean; label = LLL:EXT:myExt/Resources/Private/Language/ExtConf.xlf:passwordUseLowerCase
passwordUseLowerCase = 1
# cat=password; type=boolean; label = LLL:EXT:myExt/Resources/Private/Language/ExtConf.xlf:passwordUseDigits
passwordUseDigits = 1
# cat=password; type=boolean; label = LLL:EXT:myExt/Resources/Private/Language/ExtConf.xlf:passwordUseSpecialChars
passwordUseSpecialChars = 1
```

### Die Übersetzung in `ExtConf.xlf`

Erstellt die Sprachdatei `Resources/Private/Language/ExtConf.xlf`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xliff version="1.0" xmlns:t3="http://typo3.org/schemas/xliff">
    <file t3:id="1454342620" source-language="en" datatype="plaintext" original="messages" date="2016-02-01T17:03:45Z" product-name="myExt">
        <body>
            <trans-unit id="passwordLength">
                <source>Length of password</source>
            </trans-unit>
            <trans-unit id="passwordUseLowerCase">
                <source>Use lowercase letters</source>
            </trans-unit>
            <trans-unit id="passwordUseUpperCase">
                <source>Use uppercase letters</source>
            </trans-unit>
            <trans-unit id="passwordUseDigits">
                <source>Use digits</source>
            </trans-unit>
            <trans-unit id="passwordUseSpecialChars">
                <source>Use special chars</source>
            </trans-unit>
        </body>
    </file>
</xliff>
```

### Typisierter Zugriff auf die Konfiguration mit `ExtConf`

Für einen sauberen und objektorientierten Zugriff auf die Einstellungen erstellen wir die Service-Klasse `Classes/Configuration/ExtConf.php`:

```php
<?php

namespace Vendor\MyExt\Configuration;

use TYPO3\CMS\Core\SingletonInterface;

class ExtConf implements SingletonInterface
{
    protected int $passwordLength = 12;
    protected bool $passwordUseLowerCase = true;
    protected bool $passwordUseUpperCase = true;
    protected bool $passwordUseDigits = true;
    protected bool $passwordUseSpecialChars = true;

    public function __construct()
    {
        $extConf = unserialize($GLOBALS['TYPO3_CONF_VARS']['EXT']['extConf']['my_ext'] ?? '', ['allowed_classes' => false]);
        if (is_array($extConf)) {
            foreach ($extConf as $key => $value) {
                $methodName = 'set' . ucfirst($key);
                if (method_exists($this, $methodName)) {
                    $this->$methodName($value);
                }
            }
        }
    }

    public function getPasswordLength(): int
    {
        return $this->passwordLength > 0 ? $this->passwordLength : 12;
    }

    public function setPasswordLength(int|string $passwordLength): void
    {
        $this->passwordLength = (int)$passwordLength;
    }

    public function getPasswordUseLowerCase(): bool
    {
        return $this->passwordUseLowerCase;
    }

    public function setPasswordUseLowerCase(bool|string $passwordUseLowerCase): void
    {
        $this->passwordUseLowerCase = (bool)$passwordUseLowerCase;
    }

    public function getPasswordUseUpperCase(): bool
    {
        return $this->passwordUseUpperCase;
    }

    public function setPasswordUseUpperCase(bool|string $passwordUseUpperCase): void
    {
        $this->passwordUseUpperCase = (bool)$passwordUseUpperCase;
    }

    public function getPasswordUseDigits(): bool
    {
        return $this->passwordUseDigits;
    }

    public function setPasswordUseDigits(bool|string $passwordUseDigits): void
    {
        $this->passwordUseDigits = (bool)$passwordUseDigits;
    }

    public function getPasswordUseSpecialChars(): bool
    {
        return $this->passwordUseSpecialChars;
    }

    public function setPasswordUseSpecialChars(bool|string $passwordUseSpecialChars): void
    {
        $this->passwordUseSpecialChars = (bool)$passwordUseSpecialChars;
    }

    public function getAllowedPasswordChars(): string
    {
        $allowedPasswordChars = [];
        if ($this->getPasswordUseLowerCase()) {
            $allowedPasswordChars[] = 'l';
        }
        if ($this->getPasswordUseUpperCase()) {
            $allowedPasswordChars[] = 'u';
        }
        if ($this->getPasswordUseDigits()) {
            $allowedPasswordChars[] = 'd';
        }
        if ($this->getPasswordUseSpecialChars()) {
            $allowedPasswordChars[] = 's';
        }

        if (empty($allowedPasswordChars)) {
            throw new \RuntimeException('The allowed password chars are not configured. Check extension manager settings.', 1454665870);
        }

        return implode('', $allowedPasswordChars);
    }
}
```
