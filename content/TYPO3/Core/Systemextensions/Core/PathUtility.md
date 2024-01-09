+++
title = "PathUtility"
date = 2024-01-06T21:43:23+01:00
slug = "pathutility"
+++

## PathUtility::basename

Liefert den letzten Teil eines Pfades. Das kann der Dateiname aber auch ein Ordnername sein.
Ganz grob gesagt: Der Pfad wird anhand der Slashes gesplittet und das letzte Element wird 
zurückgeliefert. Ist im Pfad kein Slash enthalten, wird der Pfad 1 zu 1 zurückgegeben. 
Eine Verwendung von `EXT:` sollte hier vermieden werden.

{{% notice style="info" %}}
TYPO3 berücksichtigt bei Verwendung dieser Methode auch die system locale aus
`$GLOBALS['TYPO3_CONF_VARS']['SYS']['systemLocale']`. Eine locale kann wie folgt
aussehen: `de_DE`, `fr`, `it_IT.uft8`, `POSIX`, `C`. Wenn ihr diesen Wert nicht konfiguriert
habt, kann es zu unerwarteten Rückgabewerten führen. Vielleicht habt ihr bei YouTube oder
Euren Mails schonmal so Sonderzeichen wie Blümchen, Herzchen und Co. im Text gesehen. Das
sind keine Bilder, das sind Zeichen aus dem 4 Byte Zeichensatz. Wenn ihr also als `locale`
nen Wert wählt, der weniger als diese 4 Bytes interpretieren kann, dann kann es zu defekten
oder leeren Rückgabewerten führen.
{{% /notice %}}

## PathUtility::basenameDuringBootstrap

Liefert den letzten Teil eines Pfades. Das kann der Dateiname aber auch ein Ordnername sein.
Ganz grob gesagt: Der Pfad wird anhand der Slashes gesplittet und das letzte Element wird 
zurückgeliefert. Ist im Pfad kein Slash enthalten, wird der Pfad 1 zu 1 zurückgegeben.
Eine Verwendung von `EXT:` sollte hier vermieden werden.

Im Gegensatz zu `PathUtility::basename()` wird hier nicht die PHP Funktion `basename()`
verwendet, da diese von den system locales abhängig ist. Diese locales sind zum Zeitpunkt 
des Bootvorganges von TYPO3 jedoch noch nicht geladen, weshalb man sich hier für eine 
Regular Expression entschieden hat, um an den letzten Namensteil dranzukommen.

Bitte verwendet diese Methode darum bitte nur im Bootstrap-Bereich von TYPO3.

## PathUtility::dirname

Diese Methode liefert Euch den Pfad zu dem Elternordner Eures angegebenen Pfades. 
Bei dem Pfad kann es sich sowohl um eine Datei als auch um einen Ordner handeln. 
Eine Verwendung von "EXT:" sollte hier vermieden werden.

**Pfad:** `/var/www/html/phpinfo.php`
> **Ergebnis:** `/var/www/html`

**Pfad:** `/var/www/html`
> **Ergebnis:** `/var/www`

Das Ergebnis enthält keinen abschließenden Slash!

{{% notice style="info" %}}
TYPO3 berücksichtigt bei Verwendung dieser Methode auch die system locale aus
`$GLOBALS['TYPO3_CONF_VARS']['SYS']['systemLocale']`. Eine locale kann wie folgt
aussehen: `de_DE`, `fr`, `it_IT.uft8`, `POSIX`, `C`. Wenn ihr diesen Wert nicht konfiguriert
habt, kann es zu unerwarteten Rückgabewerten führen. Vielleicht habt ihr bei YouTube oder
Euren Mails schonmal so Sonderzeichen wie Blümchen, Herzchen und Co. im Text gesehen. Das
sind keine Bilder, das sind Zeichen aus dem 4 Byte Zeichensatz. Wenn ihr also als `locale`
nen Wert wählt, der weniger als diese 4 Bytes interpretieren kann, dann kann es zu defekten
oder leeren Rückgabewerten führen.
{{% /notice %}}

## PathUtility::dirnameDuringBootstrap

Diese Methode liefert Euch den Pfad zu dem Elternordner Eures angegebenen Pfades. Bei 
dem Pfad kann es sich sowohl um eine Datei als auch um einen Ordner handeln. Eine 
Verwendung von `EXT:` sollte hier vermieden werden.

Im Gegensatz zu `PathUtility::dirname()` wird hier nicht die PHP Funktion `dirname()` 
verwendet, da diese von den system locales abhängig ist. Diese locales sind zum Zeitpunkt
des Bootvorganges von TYPO3 jedoch noch nicht geladen, weshalb man sich hier für eine
Regular Expression entschieden hat, um an den letzten Namensteil dranzukommen.

Bitte verwendet diese Methode darum bitte nur im Bootstrap-Bereich von TYPO3.

## PathUtility::getAbsolutePathOfRelativeReferencedFileOrPath

Liefert vorzugweise einen absoluten Pfad zu einer Datei, die mittels relativem Pfad als
2tes Argument dieser Methode übergeben wurde. Um den Pfad korrekt auflösen zu können muss
ein Basis-Pfad zu einer Datei oder einem Ordner (muss mit Slash enden) als erstes Argument
übergeben werden.

**Basis:** `/var/www/html/public/index.php`
**Pfad:* `../../phpinfo.php`
> **Ergebnis:** `/var/www/phpinfo.php`

**Basis:** `abc/one.txt`
**Pfad:** `../../two.txt`
> **Ergebnis:** `../two.txt`

**Basis:** `/var/www/../abc/one.txt`
**Pfad:** `../two.txt`
> **Ergebnis:** `/var/two.txt`

Wenn die Basis dieser Methode mit einem relativen Pfad befüllt wird, dann kann diese 
Methode auch nur einen relativen Pfad zurückliefern. Darum "vorzugsweise".

## PathUtility::getAbsoluteWebPath

Die Idee dieser Methode war: Schmeiß irgendwas rein und Du bekommst nen absoluten Pfad 
zurück. Dabei kann der Pfad ne URI, relative, absolute oder Teile wie "Resources/Public"
sein.

**Pfad:** `https://www.typo3lexikon.de/`
> **Ergebnis:** `https://www.typo3lexikon.de/`

**Pfad:** `/var/www/html/index.php`
> **Ergebnis:** `/index.php`

**Pfad:** `typo3/sysext/core/../fluid/composer.json`
> **Ergebnis:** `/typo3/sysext/fluid/composer.json`

Dadurch, dass diese Methode versucht alles abzufrühstücken, herrscht unter der Haube wohl
ordentlich Durcheinander. Aktuell sieht es so aus, als würde diese Methode bald entfernt
und durch eine striktere API ersetzt werden. TYPO3 empfiehlt diese Methode nur noch mit 
absoluten Pfaden oder mit den Rückgabewerten von PathUtility::getPublicResourceWebPath zu
befüllen
