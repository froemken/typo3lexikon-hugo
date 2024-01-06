+++
title = "APCu kompilieren"
date = 2024-01-06T22:32:22+01:00
slug = "apcu-kompilieren"
weight = 3
+++

Gerade bei der Verwendung vieler Extbase Extensions macht es Sinn den Objects und den 
Reflection Cache auf das ApcuBackend umzubiegen. Dieses PHP-Modul ist jedoch bei den
meisten Shared Hostern nicht verfügbar oder nur in sehr alten Versionen installiert.
Im Folgenden möchte ich euch eine Anleitung zeigen, wie ihr APCu bei jweiland.net
und auch Domainfactory installieren könnt.

Meine Pfadangaben sind Beispiele und können nach Belieben geändert werden.

## Download APCu

Ich lege immer ein Verzeichnis für diverse PHP Module an (Speicherort für die *.so 
Dateien) und ein install Verzeichnis für die heruntergeladenen Sourcen:

```shell
mkdir ~/php-modules
mkdir ~/install
cd ~/install
```

APCu Modul von PECL herunterladen und mit z.B. mc oder tar entpacken. Danach in das 
APCu Verzeichnis wechseln. Bitte passt den Verzeichnisnamen an die entsprechende 
Versionsnummer an.

```shell
cd apcu-5.1.18
```

Sonderbehandlung für PHP 7.3: Bei Domainfactory wird das PCRE2 Paket nicht gefunden.
Also muss dies manuell heruntergeladen werden, entpacken und `./configure --enable-jit` 
mit anschließendem `make` ausführen. Im `src` Verzeichnis befindet sich eine `pcre.h`
Datei. Diese muss in das include Verzeichnis von APCu kopiert werden, bevor Ihr mit dem
nächsten Schritt weiter macht.

## APCu kompilieren

Ab jetzt ist die PHP Version wichtig, für die APCu kompiliert werden soll. Im Folgenden
kompilieren wir APCu für PHP 7:

```shell
/usr/local/bin/phpize72
./configure --with-php-config=/usr/local/bin/php72-config
make
```

## APCu einrichten

Nach dem Kompilieren liegt die `apcu.so` Datei im Unterverzeichnis `modules`:

```shell
cd modules
```

Kopiert nun die `apcu.so` in unser zuvor angelegtes php-modules Verzeichnis

```shell
cp apcu.so ~/php-modules
```

## Anpassungen in der php.ini

jweiland.net und Domainfactory bieten über das Kundenmenü die Möglichkeit an, die 
`php.ini` pro Domain individuell einzustellen. Wechselt zunächst in das php-modules
Verzeichnis

```shell
cd ~/php-modules
pwd
```

Mit `pwd` erhalten wir den absoluten Pfad zum Verzeichnis. Den brauchen wir nun, um
das Extension-Verzeichnis anzugeben:

```ini
extension_dir = "/www/123456_12345/rp-hosting/1234/5678/php-modules"
extension = "apcu.so"
apc.shm_size = "128M"
```

Die Standardgröße des APCu Caches liegt bei 32 MB. Laut Installtool zu wenig für den 
Extbase Object Cache. Die Option auf den APCu Cache umzuswitchen erscheint erst bei
einer Mindestgröße von 100 MB.
