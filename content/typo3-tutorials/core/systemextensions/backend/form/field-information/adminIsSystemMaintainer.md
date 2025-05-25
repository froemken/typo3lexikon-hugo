+++
title = "adminIsSystemMaintainer"
date = 2025-05-25T07:00:00+01:00
aliases = ["adminissystemmaintainer.html"]
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 8.6{{% /badge %}}

Der renderType `adminIsSystemMaintainer` wird in der TYPO3-TCA-Konfiguration verwendet, um eine zusätzliche Information im Bearbeitungsformular eines Backend-Benutzers anzuzeigen.

Wird ein Benutzer als _Administrator_ definiert und im **Install Tool** als _System Maintainer_ markiert, wird ein Hinweis ("_Dieser Benutzer ist ein System-Maintainer_") zwischen der Label-Beschriftung und dem tatsächlichen Formularfeld ausgegeben. Dies erfolgt durch die Konfiguration der `fieldInformation`-Eigenschaft in der TCA.

![Zeigt einen in rot eingerahmten Bereich zwischen Label und dem Form Element, wo die field information "adminIsSystemMaintainer" ausgegeben wird](../AdminIsSystemMaintainer.png "Zeigt den Bereich, wo adminIsSystemMaintainer ausgegeben wird")

## Beispiele

### adminIsSystemMaintainer hinzufügen

```php
[
    'columns' => [
        'admin' => [
            'label' => 'LLL:EXT:core/Resources/Private/Language/locallang_tca.xlf:be_users.admin',
            'config' => [
                'type' => 'check',
                'renderType' => 'checkboxToggle',
                'default' => 0,
                'fieldInformation' => [
                    'adminIsSystemMaintainer' => [
                        'renderType' => 'adminIsSystemMaintainer',
                    ],
                ],
            ],
        ],
    ],
],
```
