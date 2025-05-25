+++
title = "tcaDescription"
date = 2025-05-25T07:00:00+01:00
aliases = ["tcadescription.html"]
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 8.6{{% /badge %}}

Der renderType `tcaDescription` wird in TYPO3 verwendet, um eine detaillierte Beschreibung zu einem Formular-Element anzuzeigen. Dabei übernimmt es den `description`-Eintrag der TCA-Spaltendefinition eines spezifischen Felds. Wenn dieser Wert mit `LLL:` beginnt, wird der Text automatisch übersetzt (basierend auf der angegebenen Sprachdatei). Sollte kein `LLL:`-Eintrag vorhanden sein, wird der Text unverändert ausgegeben.

![Zeigt einen in rot eingerahmten Bereich zwischen Label und dem Form Element, wo die field information "tcaDescription" ausgegeben wird](../TcaDescription.png "Zeigt den Bereich, wo tcaDescription ausgegeben wird")

{{% notice style="note" title="Wichtig" icon="exclamation" %}}
The HTML tags for this specific area are limited to: `<a>`, `<br>`, `<br/>`, `<div>`, `<em>`, `<i>`, `<p>`, `<strong>`, `<span>`, `<code>`.
{{% /notice %}}

Die `tcaDescription` ist standardmäßig für die meisten Formular-Elemente in TYPO3 aktiviert. Dadurch wird die Anzeige von zusätzlichen Beschreibungen innerhalb von Formularen unterstützt, basierend auf der TCA-Konfiguration.

- BackendLayoutWizardElement.php
- CategoryElement.php
- CheckboxElement.php
- CheckboxLabeledToggleElement.php
- CheckboxToggleElement.php
- CodeEditorElement.php
- ColorElement.php
- DatetimeElement.php
- EmailElement.php
- FolderElement.php
- GroupElement.php
- ImageManipulationElement.php
- InputSlugElement.php
- InputTextElement.php
- JsonElement.php
- LinkElement.php
- NoneElement.php
- NumberElement.php
- PasswordElement.php
- RadioElement.php
- SelectCheckBoxElement.php
- SelectMultipleSideBySideElement.php
- SelectSingleBoxElement.php
- SelectSingleElement.php
- SelectTreeElement.php
- TablePermissionElement.php
- TextElement.php
- TextTableElement.php
- UserSysFileStorageIsPublicElement.php
- UuidElement.php

## Beispiele

### tcaDescription aktivieren

In TYPO3 ist es für die meisten nativen Formular-Elemente nicht erforderlich, die `tcaDescription` manuell zu aktivieren, da sie standardmäßig aktiviert ist.

Bevor jedoch die `tcaDescription` einem Formular-Element hinzugefügt wird, sollte geprüft werden, ob das entsprechende Element die **Darstellung von `fieldInformation`** unterstützt.

```php
[
    'columns' => [
        'my_own_column' => [
            'label' => 'My own column',
            'description' => 'LLL:EXT:seo/Resources/Private/Language/locallang_tca.xlf:pages.canonical_link.description',
            'config' => [
                'type' => 'input',
                'renderType' => 'mySpecialRenderingForInputElements',
                'default' => '',
                'fieldInformation' => [
                    'tcaDescription' => [
                        'renderType' => 'tcaDescription',
                    ],
                ],
            ],
        ],
    ],
],
```

### Verwendung von description

Da die `tcaDescription` für die meisten nativen Formular-Elemente in TYPO3 standardmäßig aktiviert ist, muss lediglich die **`description`-Eigenschaft** festgelegt werden, um eine Beschreibung hinzuzufügen.

```php
[
    'columns' => [
        'canonical_link' => [
            'exclude' => true,
            'label' => 'LLL:EXT:seo/Resources/Private/Language/locallang_tca.xlf:pages.canonical_link',
            'description' => 'LLL:EXT:seo/Resources/Private/Language/locallang_tca.xlf:pages.canonical_link.description',
            'displayCond' => 'FIELD:no_index:=:0',
            'config' => [
                'type' => 'link',
                'allowedTypes' => ['page', 'url', 'record'],
                'size' => 50,
                'appearance' => [
                    'browserTitle' => 'LLL:EXT:seo/Resources/Private/Language/locallang_tca.xlf:pages.canonical_link',
                    'allowedOptions' => ['params', 'rel'],
                ],
            ],
        ],
    ],
],
```
