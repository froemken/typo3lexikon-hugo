+++
title = "Runtime Extensions"
date = 2024-01-06T19:11:24+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["runtime-extensions.html"]
+++

## Runtime activated Extensions

Seit TYPO3 6.2

Über die PackageStates.php wird exakt vorgegeben welche Extensions aktiv sind. Gerade beim Deployment kann es sinnvoll sein, dass lokal 1-2 Extensions mehr aktiv sind als auf der LIVE-Seite. Im Folgenden zeige ich Euch, wie man die bereits installierten Extensions devlog und filefill für die lokale Umgebung zusätzlich zu den bereits aktivierten Extensions in der PackageStates.php aktivieren kann:

```php
if ($_SERVER['REMOTE_ADDR'] === '172.18.0.5') {
    $GLOBALS['TYPO3_CONF_VARS']['EXT']['runtimeActivatedPackages'] = ['devlog', 'filefill'];
}
```

In diesem Beispiel entwickel ich lokal in einem Docker Container. Geht ins Installtool -> Environment. Ganz unten gibt es einen Button, um sich die PHP Info anzeigen zu lassen. Sucht hier nach REMOTE_ADDR. Die dort angegebene IP-Adresse müsst Ihr mit der IP-Adresse aus meinem Beispiel austauschen. Somit sind die angegebenen Extensions nur bei einem Client mit dieser IP-Adresse aktiv und können im Extensionmanager von nun an lokal auch nicht mehr deaktiviert werden.
