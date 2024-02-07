+++
title = "Backend-Module not configured"
date = 2024-01-09T20:44:38+01:00
aliases = ["backend-module-not-configured.html"]
+++

## Backend module not configured in...

Ich hab bei mir die SVN-Version vom Kickstarter installiert. Als ich neulich ein Backend-Modul programmieren wollte erschiehn unter Information die rote Meldung:

```
It seems like there is a backend module in "mod1/conf.php" which is not configured in ext_emconf.php
```

Ich habe meine Extension mit ein paar anderen Extensions verglichen und herausgefunden, dass in der ext_emconf.php in der Eigenschaft modules der Eintrag mod1 fehlt.

Einfach

```php
'module' => '',
```
durch

```php
'module' => 'mod1',
```

ersetzen und schon ist die Meldung weg.
