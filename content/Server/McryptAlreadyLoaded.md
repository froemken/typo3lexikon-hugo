+++
title = "Module mcrypt already loaded"
linkTitle = "mcrypt already loaded"
date = 2024-01-09T20:59:37+01:00
slug = "mcrypt-already-loaded"
+++

Ich habe vor ein paar Tagen PHP 4.2.6 auf 4.2.10 geupdated und auf einmal erschien die Meldung:

> Module mcrypt already loaded in unknown on line 0

Nach einem Blick in die `phpinfo.php` habe ich dann entdeckt, dass das Modul einmal in der `php.ini` (`extension=mcrypt.so`) und einmal über die Datei `/etc/php5/apache2/conf.d/mcrypt.ini` eingebunden wird.

Das ist somit mindestens einmal zu viel, also ändern wir den entsprechenden Eintrag in:

```ini
;extension=mcrypt.so
```
