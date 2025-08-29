+++
title = "Doc Header Component"
linkTitle = "DocHeader"
date = 2025-08-29T19:34:01+01:00
aliases = ["doc-header-component.html"]
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 7.6{{% /badge %}}

![Nutzung DocHeader in einem TYPO3 Backend Modul](../doc-header-component.png "Nutzung DocHeader in einem TYPO3 Backend Modul")

Wie im Bild zu sehen ist der DocHeader eines Moduls in 4 Bereiche aufgeteilt: 

* Menüs: Diese erscheinen immer oben links
* Meta Informationen: Diese erscheinen immer oben rechts
* Button-Bar: Eine Zeile unterhalb der Menüs und Meta-Informationen über die ganze Breite
  * Buttons links: Diese erscheinen immer links in der Button-Bar
  * Buttons rechts: Diese erscheinen immer rechts in der Button-Bar

Um auf die API dieser Elemente zugreifen zu können, verwendet ihr `getDocHeaderComponent`. Auf die jeweiligen Methoden dieser API gehe ich weiter unten ein:

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    // Zugriff auf den Module Header
    $moduleTemplate->getDocHeaderComponent()->...;
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

## Methoden

### disable

Hiermit könnt ihr den kompletten Modul Kopfbereich (grauer Hintergrund) deaktivieren. Ich kann das nicht empfehlen, da dann die Ansicht im Backend nicht mehr stimmig ist.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $moduleTemplate->getDocHeaderComponent()->disable()
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

### enable

Hiermit könnt ihr den zuvor deaktivierten Modul Kopfbereich wieder aktivieren. Der DocHeader ist standardmäßig immer aktiv.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $moduleTemplate->getDocHeaderComponent()->enable()
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

### setMetaInformationForResource

Meta-Informationen werden immer oben rechts angezeigt. Durch Angabe einer Datei (Verzeichnisse sind auch möglich) wird das Verzeichnis und auch die Datei mit Datei-Typ Icon dargestellt. Die Verwendung dieser Methode gewinnt über der Verwendung von `setMetaInformation`.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $moduleTemplate->getDocHeaderComponent()->setMetaInformationForResource(
        $this->resourceFactory->retrieveFileOrFolderObject('1:user_upload/Blumentopf.jpg'),
    );
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

### setMetaInformation

Meta-Informationen werden immer oben rechts angezeigt. Durch Angabe eines Seiten-Datensatzes (pages) werden Pfad, Seiten-Titel und Seiten UID angezeigt. Intern heißt es zwar `setRecordArray()`, aber schon bei der Ausgabe des Pfades heißt die Variable `$pageRecord`. Daher kann ich es nicht empfehlen, hier Datensätze anzugeben, die nicht aus der pages-Tabelle stammen. Solltet ihr parallel auch noch `setMetaInformationForResource` verwenden, dann hat `setMetaInformation` keine Relevanz mehr und es werden die Informationen aus `setMetaInformationForResource` angezeigt.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $moduleTemplate->getDocHeaderComponent()->setMetaInformation(
        $this->getPageRecord(),
    );    

    return $moduleTemplate->renderResponse('Cars/List');
}

private function getPageRecord(): array
{
    $backendUser = $this->getBackendUser();
    $id = (int)($this->request->getQueryParams()['id'] ?? $this->request->getParsedBody()['id'] ?? 0);

    $pageRecord = BackendUtility::readPageAccess($id, $backendUser->getPagePermsClause(Permission::PAGE_SHOW)) ?: [];

    // Special key to show additional information on the right side
    $pageRecord['_additional_info'] = 'Additional Information';

    return $pageRecord;
}

private function getBackendUser(): BackendUserAuthentication
{
    return $GLOBALS['BE_USER'];
}
```

### getMenuRegistry

Mit der Menü-Registrierung könnt ihr eigene Menüs oben links im DocHeader erstellen. Zunächst müsst ihr euch die `MenuRegistry` holen und könnt damit dann ein neues Menü erstellen lassen (`makeMenu`), das als Selectbox dargestellt wird. Optionen für die Selectbox erstellt ihr mit `makeMenuItem`. Den Zielpfad (href) könnt ihr dann mit dem UriBuilder zusammenbauen:

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $menuRegistry = $moduleTemplate->getDocHeaderComponent()->getMenuRegistry();

    $switchViewMenu = $menuRegistry->makeMenu();
    $switchViewMenu
        ->setIdentifier('switchView')
        ->setLabel('Switch View');

    $listViewMenuItem = $switchViewMenu->makeMenuItem();
    $listViewMenuItem
        ->setTitle('List View')
        ->setHref($this->uriBuilder->uriFor('list', null, 'Car'))
        ->setActive(true);
    $switchViewMenu->addMenuItem($listViewMenuItem);

    $managementViewMenuItem = $switchViewMenu->makeMenuItem();
    $managementViewMenuItem
        ->setTitle('Management View')
        ->setHref($this->uriBuilder->uriFor('edit', null, 'Management'))
        ->setActive(false);
    $switchViewMenu->addMenuItem($managementViewMenuItem);

    $menuRegistry->addMenu($switchViewMenu);

    return $moduleTemplate->renderResponse('Cars/List');
}
```

Schaut auch mal bei [ModuleTemplate API](_index.md) vorbei. Dort zeige ich, wie ihr ein solches Modul für all eure Backend-Sub-Module erstellen lassen könnt.

### docHeaderContent

Damit bekommt ihr alle Meta-Informationen, Menüs und Buttons aus dem DocHeader als Array zurück. Perfekt, um DocHeader zu debuggen.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

    DebuggerUtility::var_dump($moduleTemplate->getDocHeaderComponent()->docHeaderContent());
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

### getButtonBar

Wie oben bereits geschildert ist die Button-Bar ein eigener Bereich unterhalb der Menüs und Meta-Informationen über die vollständige Breite. Hier könnt ihr diverse Arten von Buttons sowohl links als auch rechts positioniert einordnen.

#### LinkButton

Dieser Button enthält die Möglichkeit, einen Link (href) aufzunehmen. Seid mit `setHref` bitte etwas vorsichtig. Euer BE-Modul wird in einem `<iframe>` dargestellt. Dank CSP (Content Security Policy) könnt ihr nicht einfach einen externen Link eintragen.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

    $linkButton = $buttonBar->makeLinkButton();
    $linkButton
        ->setTitle('Link Button')
        ->setHref($this->uriBuilder->uriFor('list', null, 'Car'))
        ->setShowLabelText(true)
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL));

    $buttonBar->addButton($groupLink2Button);
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

#### InputButton

Dieser Button enthält die Möglichkeit, ein Formular abzusenden, das sich in eurem BE-Modul befindet. Wenn ihr nur einen `<form>` Tag im Modul habt, dann wird dieses mit Klick abgeschickt. Bei mehreren Formularen solltet ihr zusätzlich `setForm()` verwenden und dort den Wert auf dem id-Attribut des Formulars angeben. Nach aktuellem Stand fehlt jedoch in TYPO3 das zugehörende JavaScript, weshalb dieses Feature derzeit nicht funktioniert. Derzeit müsstet ihr euch mit JavaScript selbst eine Lösung für diese Buttons bauen.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    // Die InputButtons benötigen einen <form>-Tag
    $moduleTemplate->setForm(
        sprintf(
            '<form method="post" action="%s" name="car" id="saveCar">',
            $this->uriBuilder->uriFor('update', null, 'Management'),
        )
    );

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

    $inputButton = $buttonBar->makeInputButton();
    $inputButton
        ->setTitle('Input Button')
        ->setName('type') // name und value werden an die Formular-Ziel-Adresse geschickt
        ->setValue('salami')
        ->setForm('saveCar')
        ->setShowLabelText(true)
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL));

    $buttonBar->addButton($inputButton);
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

#### ShortcutButton

Mit diesem Button wird ein kleines Menü gerendert, um einerseits einen Eintrag in die TYPO3 eigenen Bookmarks zu hinterlassen und andererseits die aktuelle URL eures Moduls in die Zwischenablage zu kopieren. Letzteres lässt sich, wie im Beispiel zu sehen, deaktivieren.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

    $shortcutButton = $buttonBar->makeShortcutButton();
    $shortcutButton
        ->setCopyUrlToClipboard(false) // Wenn "false", dann wird kein Link zum Kopieren der URL dargestellt
        ->setDisplayName('My Cars Module') // Dieses Label erscheint dann später im Bookmark von TYPO3 bookmarks
        ->setRouteIdentifier('web_cars'); // Verwendet das "Configuration"-Modul, um an den Route-Identifier herauszufinden

    $buttonBar->addButton($shortcutButton);
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

#### DropDownButton

Wenn ihr innerhalb eures Moduls zwischen diversen Controller-Actions hin und her wechseln müsst, kann ich euch diesen DropDownButton durchaus empfehlen. Jedoch empfinde ich die Umsetzung mit `GeneralUtility::makeInstance` nicht mehr so smooth wie bei den anderen Buttons und mit "Fluent API" hat das auch nichts mehr zu tun. Dennoch gefällt mir dieses Flat-Design durchaus gut und fügt sich hervorragend in die ButtonBar ein. Im Folgenden hättet ihr natürlich auch wieder den Extbase UriBuilder für `setHref` nutzen können.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

    $dropDownButton = $buttonBar->makeDropDownButton();
    $dropDownButton
        ->setLabel('Create Record')
        ->setTitle('Create Record')
        ->setIcon($this->iconFactory->getIcon('actions-heart', IconSize::SMALL))
        ->setShowLabelText(true)
        ->addItem(
            GeneralUtility::makeInstance(DropDownItem::class)
                ->setLabel('Content')
                ->setHref(
                    (string)$this->coreUriBuilder->buildUriFromRoute('record_edit', [
                        'edit' => [
                            'tt_content' => [
                                $this->getPageRecord()['uid'] => 'new',
                            ],
                        ],
                        'returnUrl' => (string)$this->coreUriBuilder->buildUriFromRoute('web_cars', [
                            'id' => $this->getPageRecord()['uid'],
                        ]),
                        'defVals' => [
                            'tt_content' => [
                                'CType' => 'textmedia',
                            ],
                        ],
                    ])
                )
        )
        ->addItem(
            GeneralUtility::makeInstance(DropDownItem::class)
                ->setLabel('Page')
                ->setHref(
                    (string)$this->coreUriBuilder->buildUriFromRoute('record_edit', [
                        'edit' => [
                            'pages' => [
                                -$this->getPageRecord()['uid'] => 'new',
                            ],
                        ],
                        'returnUrl' => (string)$this->coreUriBuilder->buildUriFromRoute('web_cars', [
                            'id' => $this->getPageRecord()['uid'],
                        ]),
                        'defVals' => [
                            'pages' => [
                                'doktype' => '1',
                            ],
                        ],
                    ])
                )
        );

    $buttonBar->addButton($dropDownButton);
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

#### SplitButton

Sehr ähnlich dem DropDownButton, jedoch werden die Optionen mit InputButtons realisiert. Wie bei InputButtons bereits geschildert, benötigen diese ein Formular. TYPO3 selbst hat diese Form der Buttons damals zum Speichern von Datensätzen genutzt. Der Button hieß "Speichern" und dann konnte man rechts vom Button weitere Speichermöglichkeiten wie "Speichern und Schließen" und "Speichern und Anzeigen" wählen. Durch individuelle name-Attribute konnten so dem Formular weitere Informationen mitgegeben werden, wie eben gespeichert wird. Wegen eines Bugs in TYPO3 klappt derzeit nur der primary Button. Die alternativen Buttons in dem Kontextmenü sind derzeit außer Funktion. Issue Ticket ist bereits erstellt.

Ja, man kann den SplitButton auch mit LinkButtons verknüpfen, aber dies ist vom CSS her nicht vorgesehen und sieht darum entsprechend aus.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    // Die InputButtons benötigen einen <form>-Tag
    $moduleTemplate->setForm(
        sprintf(
            '<form method="post" action="%s" name="car" id="saveCar">',
            $this->uriBuilder->uriFor('update', null, 'Management'),
        )
    );

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

    $saveTonnoPizza = $buttonBar->makeInputButton();
    $saveTonnoPizza
        ->setTitle('Save Tonno Pizza')
        ->setName('_save_pizza')
        ->setValue('tonno')
        //->setForm('saveCar')
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL))
        ->setShowLabelText(true);
    $saveAnanasPizza = $buttonBar->makeInputButton();
    $saveAnanasPizza
        ->setTitle('Save Ananas Pizza')
        ->setName('_save_pizza')
        ->setValue('ananas')
        //->setForm('saveCar')
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL))
        ->setShowLabelText(true);
    $saveHamPizza = $buttonBar->makeInputButton();
    $saveHamPizza
        ->setTitle('Save Ham Pizza')
        ->setName('_save_pizza')
        ->setValue('ham')
        //->setForm('saveCar')
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL))
        ->setShowLabelText(true);

    $splitButton = $buttonBar->makeSplitButton()
        // Diese Option wird als Primär deklariert.
        // Ohne Angabe des zweiten Argumentes wählt TYPO3 einfach eine primäre Option aus
        ->addItem($saveTonnoPizza, true)
        ->addItem($saveAnanasPizza)
        ->addItem($saveHamPizza);

    $buttonBar->addButton($splitButton);
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

#### FullyRenderedButton

Macht, wozu auch immer ihr Bock habt. Ihr baut das HTML eures Buttons, es wird 1 zu 1 übernommen. Funktionalität? Euer Bier.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

        $fullyRenderedButton = $buttonBar->makeFullyRenderedButton();
        $fullyRenderedButton->setHtmlSource('<button id="whatever">Hyper Hyper</button>');

        $buttonBar->addButton($fullyRenderedButton);
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

#### Button

Die Basis eines jeden bisher erklärten Buttons. Ihr wollt nicht nur das HTML eures Buttons selbst definieren, sondern auch das Rendering-Verhalten, die Validierung, einfach alles? Dann haltet euch an die Vorgaben vom `ButtonInterface` und holt ihn euch mit `makeButton`:

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

    $bestButton = $buttonBar->makeButton(MyMuchBetterButton::class);
    
    $buttonBar->addButton($bestButton);
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

### Positionierung

Standardmäßig werden Buttons immer links in der ButtonBar einsortiert. Ausnahme ist der ShortcutButton. Ihr könnt die Position eurer Buttons selbst definieren:

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

    $leftButton = $buttonBar->makeLinkButton();
    $leftButton
        ->setTitle('Left Link Button')
        ->setHref('https://www.typo3.org')
        ->setShowLabelText(true)
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL));

    $buttonBar->addButton($leftButton, ButtonBar::BUTTON_POSITION_LEFT);

    $rightButton = $buttonBar->makeLinkButton();
    $rightButton
        ->setTitle('Left Link Button')
        ->setHref('https://www.typo3.org')
        ->setShowLabelText(true)
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL));

    $buttonBar->addButton($rightButton, ButtonBar::BUTTON_POSITION_RIGHT);
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```

### Gruppierung

Standardmäßig werden Buttons immer der Gruppe `1` hinzugefügt. Durch die Verwendung mehrerer Gruppen entsteht zwischen den Buttons unterschiedlicher Gruppen ein kleiner Abstand von ca. 5 Millimetern.

```php
public function listAction(): ResponseInterface
{
    $moduleTemplate = $this->moduleTemplateFactory->create($request);

    $buttonBar = $moduleTemplate->getDocHeaderComponent()->getButtonBar();

    $linkButton = $buttonBar->makeLinkButton();
    $linkButton
        ->setTitle('Left Link Button')
        ->setHref('https://www.typo3.org')
        ->setShowLabelText(true)
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL));

    // Ohne weitere Argumente wird der Button immer links angezeigt
    // und der Gruppe "1" zugeordnet
    $buttonBar->addButton($group1Button);

    // Zwischen dem $linkButton und den beiden nachfolgenden Buttons
    // erscheint ein kleiner Abstand
    $typo3Button = $buttonBar->makeLinkButton();
    $typo3Button
        ->setTitle('TYPO3 Link Group 2')
        ->setHref('https://www.typo3.org')
        ->setShowLabelText(true)
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL));

    $buttonBar->addButton($typo3Button, ButtonBar::BUTTON_POSITION_LEFT, 2);

    // Dieser Button klebt direkt an $typo3Button
    $docsButton = $buttonBar->makeLinkButton();
    $docsButton
        ->setTitle('Docs Link Group 2')
        ->setHref('https://docs.typo3.org')
        ->setShowLabelText(true)
        ->setIcon($this->iconFactory->getIcon('actions-view', IconSize::SMALL));

    $buttonBar->addButton($docsButton, ButtonBar::BUTTON_POSITION_LEFT, 2);
    
    return $moduleTemplate->renderResponse('Cars/List');
}
```
