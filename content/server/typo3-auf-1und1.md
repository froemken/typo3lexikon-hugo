+++
title = "TYPO3 auf Servern von 1und1"
linkTitle = "TYPO3 auf 1und1"
date = 2024-01-09T21:00:04+01:00
slug = "typo3-auf-1und1"
aliases = ["typo3-auf-1und1.html"]
+++

Seit TYPO3-Version 4.2 wird PHP in der Version 5 benötigt. Alle php Dateien bei 1und1 werden aber als PHP4 ausgeführt.

Damit php-Dateien als PHP5 gehandhabt werden muss im root-Verzeichnis eine .htaccess mit folgendem Inhalt erstellt werden:

```ini
AddType x-mapp-php5 .php
AddHandler x-mapp-php5 .php
```
