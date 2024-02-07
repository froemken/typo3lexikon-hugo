+++
title = "Clone method on non object"
date = 2024-01-09T20:46:34+01:00
aliases = ["clone-method-called-on-non-object.html"]
+++

## __clone method called on non-object

Bei dieser Fehlermeldung:

```
Fatal error: __clone method called on non-object in /Applications/MAMP/htdocs/typo3_62/typo3_src-62/typo3/sysext/extbase/Classes/Persistence/Repository.php on line 256
```

handelt es sich nicht um einen Fehler in Extbase, sondern um einen Fehler von Euch.

Zum Beispiel bei einem Konstrukt, wie diesem:

```php
/**
 * @var \TYPO3\CMS\Extbase\Persistence\Generic\QuerySettingsInterface
 */
protected $defaultQuerySettings = array(
    'status' => QueryInterface::ORDER_ASCENDING
);
```

Ihr könnt die Sortierung nicht einfach in die defaultQuerySettings rein tun, dafür gibt es eine spezielle Eigenschaft. So muss das aussehen:

```php
/**
 * @var array 
 */
protected $defaultOrderings = array(    
    'status' => QueryInterface::ORDER_ASCENDING
);
```
