+++
title = "Einfache Tabellenverbindung"
date = 2024-01-09T20:22:38+01:00
aliases = ["einfache-tabellenverbindung.html"]
+++

## ext_localconf.php

```php
<?php
if (!defined ('TYPO3_MODE')) die ('Access denied.');

Tx_Extbase_Utility_Extension::configurePlugin(
    $_EXTKEY,
    'Pi1',
    array(
        'Customer' => 'list',
    )
);
```

## ext_tables.php

```php
<?php
if (!defined ('TYPO3_MODE')) {
    die ('Access denied.');
}

Tx_Extbase_Utility_Extension::registerPlugin(
    $_EXTKEY,
    'Pi1',
    'Customer - Car - Sample'
);

$TCA['tx_sffluid_domain_model_customer'] = array (
    'ctrl' => array (
        'title'     => 'LLL:EXT:sffluid/locallang_db.xml:tx_sffluid_domain_model_customer',        
        'label'     => 'name',    
        'tstamp'    => 'tstamp',
        'crdate'    => 'crdate',
        'cruser_id' => 'cruser_id',
        'default_sortby' => 'ORDER BY name',    
        'delete' => 'deleted',    
        'enablecolumns' => array (        
            'disabled' => 'hidden',
        ),
        'dynamicConfigFile' => t3lib_extMgm::extPath($_EXTKEY).'tca.php',
        'iconfile'          => t3lib_extMgm::extRelPath($_EXTKEY).'icon_tx_sffluid_domain_model_customer.gif',
    ),
);

$TCA['tx_sffluid_domain_model_car'] = array (
    'ctrl' => array (
        'title'     => 'LLL:EXT:sffluid/locallang_db.xml:tx_sffluid_domain_model_car',        
        'label'     => 'type',    
        'tstamp'    => 'tstamp',
        'crdate'    => 'crdate',
        'cruser_id' => 'cruser_id',
        'default_sortby' => 'ORDER BY crdate',    
        'delete' => 'deleted',    
        'enablecolumns' => array (        
            'disabled' => 'hidden',
        ),
        'dynamicConfigFile' => t3lib_extMgm::extPath($_EXTKEY).'tca.php',
        'iconfile'          => t3lib_extMgm::extRelPath($_EXTKEY).'icon_tx_sffluid_domain_model_car.gif',
    ),
);
```

## tca.php

```php
<?php
if (!defined ('TYPO3_MODE'))     die ('Access denied.');

$TCA['tx_sffluid_domain_model_customer'] = array (
    'ctrl' => $TCA['tx_sffluid_domain_model_customer']['ctrl'],
    'interface' => array (
        'showRecordFieldList' => 'hidden,name,car'
    ),
    'feInterface' => $TCA['tx_sffluid_domain_model_customer']['feInterface'],
    'columns' => array (
        'hidden' => array (        
            'exclude' => 1,
            'label'   => 'LLL:EXT:lang/locallang_general.xml:LGL.hidden',
            'config'  => array (
                'type'    => 'check',
                'default' => '0'
            )
        ),
        'name' => array (        
            'exclude' => 0,        
            'label' => 'LLL:EXT:sffluid/locallang_db.xml:tx_sffluid_domain_model_customer.name',        
            'config' => array (
                'type' => 'input',    
                'size' => '30',
            )
        ),
        'car' => array (        
            'exclude' => 0,        
            'label' => 'LLL:EXT:sffluid/locallang_db.xml:tx_sffluid_domain_model_customer.car',        
            'config' => array (
                'type' => 'select',    
                'foreign_table' => 'tx_sffluid_domain_model_car',    
                'foreign_table_where' => 'ORDER BY tx_sffluid_domain_model_car.uid',    
                'size' => 1,    
                'minitems' => 0,
                'maxitems' => 1,
            )
        ),
    ),
    'types' => array (
        '0' => array('showitem' => 'hidden;;1;;1-1-1, name, car')
    ),
    'palettes' => array (
        '1' => array('showitem' => '')
    )
);



$TCA['tx_sffluid_domain_model_car'] = array (
    'ctrl' => $TCA['tx_sffluid_domain_model_car']['ctrl'],
    'interface' => array (
        'showRecordFieldList' => 'hidden,type'
    ),
    'feInterface' => $TCA['tx_sffluid_domain_model_car']['feInterface'],
    'columns' => array (
        'hidden' => array (        
            'exclude' => 1,
            'label'   => 'LLL:EXT:lang/locallang_general.xml:LGL.hidden',
            'config'  => array (
                'type'    => 'check',
                'default' => '0'
            )
        ),
        'type' => array (        
            'exclude' => 0,        
            'label' => 'LLL:EXT:sffluid/locallang_db.xml:tx_sffluid_domain_model_car.type',        
            'config' => array (
                'type' => 'input',    
                'size' => '30',
            )
        ),
    ),
    'types' => array (
        '0' => array('showitem' => 'hidden;;1;;1-1-1, type')
    ),
    'palettes' => array (
        '1' => array('showitem' => '')
    )
);
```

## xClasses/Controller/CustomerController.php

```php
<?php
class Tx_Sffluid_Controller_CustomerController extends Tx_Extbase_MVC_Controller_ActionController {

    /**
     * @var Tx_Sffluid_Domain_Repository_CustomerRepository
     */
    protected $customerRepository;
    
    /**
     * Initializes the current action
     *
     * @return void
     */
    public function initializeAction() {
        $this->customerRepository = t3lib_div::makeInstance('Tx_Sffluid_Domain_Repository_CustomerRepository');
    }
    
    /**
     * List action for this controller. Displays a list of customers
     *
     * @return string The rendered view
     */
    public function listAction() {
        $this->view->assign('customers', $this->customerRepository->findAll());
    }
}
```

## Classes/Domain/Repository/CustomerRepository.php

```php
<?php
class Tx_Sffluid_Domain_Repository_CustomerRepository extends Tx_Extbase_Persistence_Repository 
{
}
```

## Classes/Domain/Model/Customer.php

```php
<?php
class Tx_Sffluid_Domain_Model_Customer extends Tx_Extbase_DomainObject_AbstractEntity {
    protected $name;
    protected $car;
    
    public function getName() {
        return $this->name;
    }
    
    public function getCar() {
        return $this->car;
    }
}
```

Die Extension gibt nun einen Fehler aus:

```
#1251315967: Could not determine the child object object type.
```

Hier seht Ihr nun sehr deutlich, warum die Angabe von Kommentaren so wichtig ist, denn dort werden Informationen über den Typ einer Variable angegeben. Mit vollständigen Kommentaren schaut die Customer.php nun so aus:

```php
<?php
class Tx_Sffluid_Domain_Model_Customer extends Tx_Extbase_DomainObject_AbstractEntity {

    /**
     * The customers name
     *
     * @var string
     */
    protected $name;

    /**
     * The customers car
     *
     * @var Tx_Sffluid_Domain_Model_Car
     */
    protected $car;
    
    /**
     * Returns the customer's name
     *
     * @return string The customer's name
     */
    public function getName() {
        return $this->name;
    }
    
    /**
     * Returns the customer's car
     *
     * @return Tx_Sffluid_Domain_Model_Car The customer's car
     */
    public function getCar() {
        return $this->car;
    }
}
```

## Classes/Domain/Model/Car.php

```php
<?php
class Tx_Sffluid_Domain_Model_Car extends Tx_Extbase_DomainObject_AbstractEntity {

    /**
     * The car's type
     *
     * @var string
     */
    protected $type;

    /**
     * Returns the car's type
     *
     * @return string The car's type
     */
    public function getType() {
        return $this->type;
    }
}
```

Hier das benutzte Template in `Resources/Private/Templates/Customer/list.html`

```html
Meine Kunden fahren:

<f:for each="{customers}" as="customer">
    {customer.name} - {customer.car.type}
</f:for>
```
