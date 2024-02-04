+++
title = "cshmanual"
date = 2024-01-09T20:13:45+01:00
aliases = ["cshmanual.html"]
+++

Die Systemextension `EXT:cshmanual` enthält das Modul `TYPO3 Manual`, das über das Help Menü in der Titelleiste des Backends zu erreichen ist. Sie zeigt zusätzliche Feldinformationen pro Spalte und pro Tabelle an. Dazu gehören: Beschreibung, Detailbeschreibung, Siehe auch Verweis, Bild und Bildbeschreibung. Diese 5 Element können teilweise, gar nicht oder auch vollständig mittels Sprachdateien hinterlegt werden.

Um neue Sprachdateien in TYPO3 zu registrieren, benötigt ihr einen Einzeiler in der `ext_tables.php`:

```php
\TYPO3\CMS\Core\Utility\ExtensionManagementUtility::addLLrefForTCAdescr(
    '_MOD_web_layout', 
    'EXT:frontend/Resources/Private/Language/locallang_csh_weblayout.xlf'
);
\TYPO3\CMS\Core\Utility\ExtensionManagementUtility::addLLrefForTCAdescr(
    'fe_groups',
    'EXT:frontend/Resources/Private/Language/locallang_csh_fe_groups.xlf'
);
```

Obere Zeile fügt Feldbeschreibungen für die Tabelle `fe_groups` hinzu.

Die untere Tabelle fügt Feldbeschreibungen für ein Modul hinzu. In diesem Falle `web_layout`.

Schaut euch die `xlf` Dateien mal genauer an. Das Befüllen all dieser Informationen macht nicht unbedingt Spaß, aber es hilft dem Kunden und Redakteur enorm.
