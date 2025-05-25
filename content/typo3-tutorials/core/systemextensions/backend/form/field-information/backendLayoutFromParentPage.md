+++
title = "backendLayoutFromParentPage"
date = 2025-05-25T07:00:00+01:00
aliases = ["backendlayoutfromparentpage.html"]
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 8.6{{% /badge %}}

Der renderType `backendLayoutFromParentPage` wird in der TYPO3-TCA-Konfiguration verwendet, um Informationen über das **verwendete Backend-Layout** bereitzustellen. Dabei wird dargestellt, welches Layout von den **übergeordneten Seiten** rekursiv übernommen wird.

![Zeigt einen in rot eingerahmten Bereich zwischen Label und dem Form Element, wo die field information "backendLayoutFromParentPage" ausgegeben wird](../BackendLayoutFromParentPage.png "Zeigt den Bereich, wo backendLayoutFromParentPage ausgegeben wird")

## Beispiele

### backendLayoutFromParentPage hinzufügen

```php
[
    'columns' => [
        'backend_layout' => [
            'label' => 'LLL:EXT:frontend/Resources/Private/Language/locallang_tca.xlf:pages.backend_layout_formlabel',
            'config' => [
                'type' => 'select',
                'renderType' => 'selectSingle',
                'fieldInformation' => [
                    'backendLayoutFromParentPage' => [
                        'renderType' => 'backendLayoutFromParentPage',
                    ],
                ],
            ],
        ],
    ],
],
```
