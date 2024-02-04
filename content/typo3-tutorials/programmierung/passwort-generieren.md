+++
title = "Passwort generieren"
date = 2024-01-09T20:49:47+01:00
aliases = ["passwort-generieren.html"]
+++

Im Folgenden zeige ich euch meine Vorgehensweise, um im Backend von TYPO3 7 LTS einen "Passwort generieren" Button für das Passwort der Backendbenutzer zu erstellen. Der Button soll rechts vom Passwortfeld erscheinen. Mit jedem Klick soll ein AJAX-Call gestartet, der einerseits das Passwort setzt und andererseits das Passwort im Klartext in einem Bootstrap-Panel anzeigt.

Solche Aufgaben für das Frontend gibt es im Netz wie Sand am Meer, aber im Backend läuft so einiges anders. Mir war wichtig, diese Aufgabe möglichst Core kompatibel, also ohne Tricksen, Biegen und Brechen umzusetzen.

## Einen Wizard einfügen

Zunächst müsst ihr für das Passwort der `be_users` Tabelle einen weiteren Wizard hinzufügen. Erstellt in eurem SitePackage die Datei `[sitePackage]/Configuration/TCA/Overrides/be_users.php` mit diesem Inhalt:

```php
<?php
$GLOBALS['TCA']['be_users']['columns']['password']['config']['wizards'] = array(
    'generatePassword' => array(
        'type' => 'userFunc',
        'userFunc' => \StefanFroemken\SitePackage\Hooks\GeneratePassword::class . '->render',
    )
);
```

Mit dem TCA Typ `userFunc` und der Angabe einer PHP-Klasse könnt ihr euch nun komplett selbst, um das Rendering eines Wizards kümmern. Erstellt nun die Datei `[SitePackage]/Classes/Hooks/GeneratePassword.php`:

```php
<?php
namespace JWeiland\MyExt\Hooks;

use TYPO3\CMS\Backend\Form\Element\AbstractFormElement;
use TYPO3\CMS\Core\Imaging\IconFactory;
use TYPO3\CMS\Core\Utility\GeneralUtility;
use TYPO3\CMS\Extbase\Utility\LocalizationUtility;

/**
 * @license www.gnu.org/licenses/gpl.html GNU General Public License, version 3 or later
 */
class GeneratePassword
{
    /**
     * Render a wizard to generate a password
     *
     * @param array $parentArray
     * @param AbstractFormElement $formElement
     *
     * @return string The rendered Wizard
     */
    public function render(array $parentArray, AbstractFormElement $formElement)
    {
        // render the structure of the panel to show the password after Ajax request
        $parentArray['item'] .= sprintf(
            '
              
                %s
              
              
            ',
            LocalizationUtility::translate('generatedPassword', 'myExt')
        );

        // render the button
        /** @var IconFactory $iconFactory */
        $iconFactory = GeneralUtility::makeInstance(IconFactory::class);
        return sprintf(
            '%s',
            $parentArray['uid'],
            $parentArray['itemName'],
            $iconFactory->getIcon('actions-document-synchronize')
        );
    }
}
```

Was hier nicht zu sehen ist: TYPO3 ruft die `render()` Methode mithilfe von `GeneralUtility::callUserFunction()` auf. Das Besondere daran: Alle Parameter werden als Referenzen übergeben. Das ergibt gleich 2 verschiedene Anwendungsbereiche.

In dem Array `$parentArray` gibt es unter dem Key `item` das vollständige HTML des Passwortfeldes. In meinem Beispiel füge ich ein leeres Bootstrap Panel hinzu. Einzig eine Überschrift habe ich diesem verpasst. In den Panel-Body wird später per JavaScript das Passwort dynamisch eingebunden.

Den eigentlichen Wizard müsst ihr über den Returnwert realisieren. Ich habe mich hier für ein einfaches Icon entschieden, das ich mithilfe der neuen IconFactory erstelle. Hinzu kommt eine CSS-Klasse und die beiden Werte `uid` und `name` als `data` Attribute auf die ihr später mit z.B. jQuery wieder zugreifen könnt. Gerade beim Editieren von mehreren Backendbenutzern (z.B. inline) ist es wichtig diese Daten zu haben, damit ihr nicht beim Klick auf den Button auf einmal die Passwörter aller geöffneten Benutzer ändert. Aber dazu später mehr.

Leert nach dem Speichern den Systemcache (roter Blitz) oder im Installtool `Flush Caches`. Der Wizard sollte zumindest schon zu sehen sein.

## Vorbereitungen für das RequireJS Modul

Dieser Part hat richtig Zeit gekostet. Es gibt diverse Hooks mit denen ich das JavaScript mithilfe des PageRenderers hätte einbinden können, jedoch gibt es ein Problem, das man nicht sofort bemerkt.

Es ist ein gewaltiger Unterschied, ob ihr einen Datensatz über das `List` Module direkt bearbeitet, oder ihr den Datensatz als Teil eines anderen Datensatzes (TCA Typ: inline) bearbeitet. Das Problem bei Typ `inline` ist, dass das komplette HTML über AJAX nachgeladen wird. Wenn ich das JavaScript global einfüge, dann sind dem JavaScript diese neuen Felder nicht bekannt. Binde ich das JavaScript direkt in den `be_users` Datensatz ein, dann klappt das zwar für das direkte Bearbeiten, jedoch nicht, wenn der `be_users` Datensatz über `inline` nachgeladen wird. Das JavaScript ist zwar da, wird aber nicht ausgeführt.

Erst bei genauer Analyse des AJAX-Requests bin ich auf einen interessanten Wert im `json` aufmerksam geworden: `scriptCall`.

Im FormInlineAjaxController wird dieser Wert erstmalig erstellt und über `mergeChildResultIntoJsonResult()` je nach Eingabefeld immer weiter aufgefüllt. Jedes Eingabefeld in TYPO3 hat grundsätzlich die Möglichkeit über den Array-Key `requireJsModules` diesen `scriptCall` zu befüllen. Problem an der Sache: Die neue Formengine von TYPO3 bietet keinen Hook an, um dieses Array zu befüllen. In Rücksprache mit dem Core gibt es nur eine Lösung: Wir müssen das Rendering des Passwortfeldes komplett überschreiben und dies als eigenen `renderType` registrieren.

Fügt dazu in eurer `ext_localconf.php` folgende Zeilen ein:

```php
// Add our own form elements, because we need the requireJSmodule for our password generation
$GLOBALS['TYPO3_CONF_VARS']['SYS']['formEngine']['nodeRegistry'][1454580921] = array(
    'nodeName' => 'myExtPassword',
    'priority' => '70',
    'class' => \JWeiland\MyExt\Form\Element\InputTextElement::class,
);
$GLOBALS['TYPO3_CONF_VARS']['SYS']['formEngine']['nodeRegistry'][1454581671] = array(
    'nodeName' => 'myExtRsaPassword',
    'priority' => '70',
    'class' => \JWeiland\MyExt\Form\Element\RsaInputElement::class,
);
```

Mit diesen Zeilen registriert ihr 2 neue renderTypen. 2 deshalb, weil wir je nachdem, ob `rsaauth` installiert ist oder nicht einen anderen renderTypen benötigen. Diese Unterscheidung realisieren wir wieder über unsere vorhin angelegte Datei `[sitePackage]/Configuration/TCA/Overrides/be_users.php`:

```php
<?php
if (\TYPO3\CMS\Core\Utility\ExtensionManagementUtility::isLoaded('rsaauth')) {
    $GLOBALS['TCA']['be_users']['columns']['password']['config']['renderType'] = 'myExtRsaPassword';
} else {
    $GLOBALS['TCA']['be_users']['columns']['password']['config']['renderType'] = 'myExtPassword';
}
```

Hier seht ihr auch wieder die zuvor registrierten nodeNames, die nun als renderTypen eingesetzt werden. Durch diese Angabe brecht ihr aus dem Standardrendering von TYPO3 aus und leitet das Rendering in eure eigenen PHP-Klassen um.

Kopiert euch die beiden Dateien aus dem Core in ein Verzeichnis eurer Extension:

```
sysext/backend/Classes/Form/Element/InputTextElement.php
sysext/rsaauth/Classes/Form/Element/RsaInputElement.php
```

Ich habe bei mir den Pfad vom Original übernommen: `[sitePackage]/Classes/Form/Element/`

Passt nun die `namespace` Zeilen an eure Extension an und erweitert mit `extends` die Originalklassen. Hier ein Ausschnitt, wie die Datei aussehen könnte:

```php
<?php
namespace JWeiland\MyExt\Form\Element;

use TYPO3\CMS\Backend\Utility\BackendUtility;
use TYPO3\CMS\Core\Imaging\Icon;
use TYPO3\CMS\Core\Imaging\IconFactory;
use TYPO3\CMS\Core\Utility\GeneralUtility;
use TYPO3\CMS\Core\Utility\MathUtility;
use TYPO3\CMS\Core\Utility\StringUtility;

/**
 * Generation of TCEform elements of the type "input type=text"
 */
class InputTextElement extends \TYPO3\CMS\Backend\Form\Element\InputTextElement
{
    /**
     * This will render a single-line input form field, possibly with various control/validation features
     *
     * @return array As defined in initializeResultArray() of AbstractNode
     */
    public function render()
    {
        /** @var IconFactory $iconFactory */
        $iconFactory = GeneralUtility::makeInstance(IconFactory::class);
        $languageService = $this->getLanguageService();

        $table = $this->data['tableName'];
        $fieldName = $this->data['fieldName'];
        $row = $this->data['databaseRow'];
        $parameterArray = $this->data['parameterArray'];
        $resultArray = $this->initializeResultArray();
        $resultArray['requireJsModules'] = array(
            'TYPO3/CMS/MyExt/GeneratePassword'
        );
        $isDateField = false;
        ...
```

Es klingt bescheuert, aber ihr müsst die komplette `render()` Methode aus der Originalklasse kopieren, nur um diese 3 Zeilen für das `requireJsModules` einzufügen.

Ähnlich sieht es in der Datei für das `RsaInputElement` aus. Hier wird bereits ein Modul geladen, das wir entsprechend erweitern müssen:

```php
$resultArray['requireJsModules'] = array(
    'TYPO3/CMS/Rsaauth/RsaEncryptionModule',
    'TYPO3/CMS/MyExt/GeneratePassword'
);
```

## Das JavaScript als RequireJS Modul

TYPO3 bringt von Haus aus [RequireJS](https://requirejs.org/) mit und sucht automatisch in dem Pfad `[SitePackage]/Resources/Public/JavaScript` nach RequireJS Modulen. Die Registrierung eines RequireJS Moduls haben wir bereits im vorherigen Abschnitt durchgeführt. Legt nun eine js-Datei an. Meine heißt `GeneratePassword.js` mit folgendem Inhalt:

```javascript
/**
 * Module: TYPO3/CMS/MyExt/GeneratePassword
 */
define("TYPO3/CMS/MyExt/GeneratePassword", ["jquery"], function($) {
    $(function() {
        // hide all panels
        $("div.myExtGeneratedPassword").hide();

        // start ajax call onclick
        $("a.myExtGeneratePassword").on("click", function(event) {
            event.preventDefault();
            var itemName = $(this).data("itemname");
            var itemUid = $(this).data("itemuid");
            $.ajax({
                url: TYPO3.settings.ajaxUrls['myExtGeneratePassword'],
                dataType: 'text',
                cache: false,
                success: function(response) {
                    // set new password
                    $("[data-formengine-input-name='" + itemName + "']").val(response);
                    $("input[name='" + itemName + "']")
                        .siblings("div.myExtGeneratedPassword")
                        .show()
                        .find("div.panel-body")
                        .find("code")
                        .text(response);
                    TBE_EDITOR.fieldChanged('be_users', itemUid, 'password', itemName);
                }
            });
        });
    });
});
```

Sehr wichtig: Anders als bei Extbase-Extensions müssen eure RequireJS Module mit dem `TYPO3\CMS` Vendornamen registriert werden. Der 2te Parameter ist ein Array von Modulen, das wir für unser Modul zwingend benötigen. In unserem Modul benötigen wir `jquery`, das wir dann im dritten Parameter über `$` in unserem Modul zur Verfügung stellen.

Im Weiteren greift ihr nun auf die `data` Attribute unseres Wizards zu, um dann damit das exakte Passwortfeld ansprechen zu können und das Passwort zu verändern. Außerdem befüllt ihr noch den panel-body und zeigt es an.

## Das Ajax-Script

Backend AJAX-Scripte sind zunächst in der `ext_localconf.php` zu registrieren: 

```php
\TYPO3\CMS\Core\Utility\ExtensionManagementUtility::registerAjaxHandler(
    'myExtGeneratePassword',
    \JWeiland\MyExt\Ajax\GeneratePassword::class . '->generate',
    true
);
```

Dank des eindeutigen Namens `myExtGeneratePassword` erzeugt TYPO3 für euch völlig automatisch eine vollständige AJAX-Url inkl. HashWerten, die ihr wie oben gesehen mit `TYPO3.settings.ajaxUrls` in eurem AJAX-Call verwenden könnt. Mit dem 2ten Parameter könnt ihr eine PHP-Klasse verknüpfen, die bei dem AJAX-Call aufgerufen werden soll. Meine `GeneratePassword.php` hat folgenden Inhalt:

```php
<?php

namespace JWeiland\MyExt\Ajax;

use JWeiland\MyExt\Configuration\ExtConf;
use TYPO3\CMS\Core\Http\AjaxRequestHandler;
use TYPO3\CMS\Core\Utility\GeneralUtility;

/**
 * @license www.gnu.org/licenses/gpl.html GNU General Public License, version 3 or later
 */
class GeneratePassword {

    /**
     * Generate a password
     *
     * @param array $ajaxParameters
     * @param AjaxRequestHandler $ajaxRequestHandler
     * @return string
     */
    public function generate(array $ajaxParameters, AjaxRequestHandler $ajaxRequestHandler)
    {
        /** @var ExtConf $extConf */
        $extConf = GeneralUtility::makeInstance(ExtConf::class);
        $ajaxRequestHandler->setContent(array(
            $this->generateStrongPassword(
                $extConf->getPasswordLength(),
                $extConf->getAllowedPasswordChars()
            )
        ));
    }

    /**
     * Generate a strong password
     * This method was inspired by: gist.github.com/tylerhall/521810
     *
     * @param int $length
     * @param string $availableSets
     *
     * @return string
     */
    public function generateStrongPassword($length = 12, $availableSets = 'luds')
    {
        $sets = array();
        if (strpos($availableSets, 'l') !== false) {
            $sets[] = 'abcdefghjkmnpqrstuvwxyz';
        }
        if (strpos($availableSets, 'u') !== false) {
            $sets[] = 'ABCDEFGHJKMNPQRSTUVWXYZ';
        }
        if (strpos($availableSets, 'd') !== false) {
            $sets[] = '23456789';
        }
        if (strpos($availableSets, 's') !== false) {
            $sets[] = '!@#$%&*?';
        }
        $all = '';
        $password = '';
        foreach ($sets as $set)
        {
            $password .= $set[array_rand(str_split($set))];
            $all .= $set;
        }
        $all = str_split($all);
        for ($i = 0; $i < $length - count($sets); $i++) {
            $password .= $all[array_rand($all)];
        }
        $password = str_shuffle($password);
        return $password;
    }
}
```

## Das Passwort konfigurierbar machen

Legt im Rootverzeichnis eurer Extension eine Datei mit dem Namen `ext_conf_template.txt` an. Mit dieser Datei könnt ihr eure Extension über den Extensionmanager konfigurierbar machen:

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

### Die Übersetzung: ExtConf.xlf

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

### Besserer Zugriff auf die Configuration mit ExtConf

```php
<?php

namespace JWeiland\MyExt\Configuration;

use TYPO3\CMS\Core\SingletonInterface;

/**
 * @license www.gnu.org/licenses/gpl.html GNU General Public License, version 3 or later
 */
class ExtConf implements SingletonInterface
{
    /**
     * passwordLength
     *
     * @var int
     */
    protected $passwordLength = 12;
    
    /**
     * passwordUseLowerCase
     *
     * @var bool
     */
    protected $passwordUseLowerCase = true;
    
    /**
     * passwordUseUpperCase
     *
     * @var bool
     */
    protected $passwordUseUpperCase = true;
    
    /**
     * passwordUseDigits
     *
     * @var bool
     */
    protected $passwordUseDigits = true;
    
    /**
     * passwordUseSpecialChars
     *
     * @var bool
     */
    protected $passwordUseSpecialChars = true;
    
    /**
     * allowedPasswordChars
     *
     * @var string
     */
    protected $allowedPasswordChars = 'luds';
    
    /**
     * constructor of this class
     * This method reads the global configuration and calls the setter methods.
     */
    public function __construct()
    {
        // get global configuration
        $extConf = unserialize($GLOBALS['TYPO3_CONF_VARS']['EXT']['extConf']['my_ext']);
        if (is_array($extConf)) {
            // call setter method foreach configuration entry
            foreach ($extConf as $key => $value) {
                $methodName = 'set' . ucfirst($key);
                if (method_exists($this, $methodName)) {
                    $this->$methodName($value);
                }
            }
        }
    }
    
    /**
     * Returns the passwordLength
     *
     * @return int $passwordLength
     */
    public function getPasswordLength()
    {
        if (empty($this->passwordLength)) {
            return 12;
        } else {
            return $this->passwordLength;
        }
    }
    
    /**
     * Sets the passwordLength
     *
     * @param int $passwordLength
     * @return void
     */
    public function setPasswordLength($passwordLength)
    {
        $this->passwordLength = (int)$passwordLength;
    }
    
    /**
     * Returns the passwordUseLowerCase
     *
     * @return bool $passwordUseLowerCase
     */
    public function getPasswordUseLowerCase()
    {
        return $this->passwordUseLowerCase;
    }
    
    /**
     * Sets the passwordUseLowerCase
     *
     * @param bool $passwordUseLowerCase
     * @return void
     */
    public function setPasswordUseLowerCase($passwordUseLowerCase)
    {
        $this->passwordUseLowerCase = (bool)$passwordUseLowerCase;
    }
    
    /**
     * Returns the passwordUseUpperCase
     *
     * @return bool $passwordUseUpperCase
     */
    public function getPasswordUseUpperCase()
    {
        return $this->passwordUseUpperCase;
    }
    
    /**
     * Sets the passwordUseUpperCase
     *
     * @param bool $passwordUseUpperCase
     * @return void
     */
    public function setPasswordUseUpperCase($passwordUseUpperCase)
    {
        $this->passwordUseUpperCase = (bool)$passwordUseUpperCase;
    }
    
    /**
     * Returns the passwordUseDigits
     *
     * @return bool $passwordUseDigits
     */
    public function getPasswordUseDigits()
    {
        return $this->passwordUseDigits;
    }
    
    /**
     * Sets the passwordUseDigits
     *
     * @param bool $passwordUseDigits
     * @return void
     */
    public function setPasswordUseDigits($passwordUseDigits)
    {
        $this->passwordUseDigits = (bool)$passwordUseDigits;
    }
    
    /**
     * Returns the passwordUseSpecialChars
     *
     * @return bool $passwordUseSpecialChars
     */
    public function getPasswordUseSpecialChars()
    {
        return $this->passwordUseSpecialChars;
    }
    
    /**
     * Sets the passwordUseSpecialChars
     *
     * @param bool $passwordUseSpecialChars
     * @return void
     */
    public function setPasswordUseSpecialChars($passwordUseSpecialChars)
    {
        $this->passwordUseSpecialChars = (bool)$passwordUseSpecialChars;
    }
    
    /**
     * Returns the allowedPasswordChars
     *
     * @return string $allowedPasswordChars
     *
     * @throws \Exception
     */
    public function getAllowedPasswordChars()
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
            throw new \Exception('The allowed password chars are not configured in the extension configuration. Set some in the extension manager', 1454665870);
        }
        return implode('', $allowedPasswordChars);
    }
}
```
