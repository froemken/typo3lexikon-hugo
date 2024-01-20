+++
title = "Cache: Performance"
linkTitle = "Performance"
date = 2024-01-06T22:32:33+01:00
slug = "performance"
aliases = ["performance.html"]
+++

Für diesen Test habe ich die 4 Core Caches

- extbase_object
- extbase_reflection
- extbase_datamapfactory_datamap
- cache_rootline

auf die gleichen Cachebackends abgeändert. Ich habe sowohl das Listenmodul als auch den Extensionmanager mit jedem Backend 3 mal hintereinander aufgerufen. Dabei wurde die `set()` Methode des Backends 71 mal aufgerufen. Bei den Angaben handelt es sich um die Summe aller Zeiten in einem Request in Sekunden.

## ApcuBackend

**Extensionmanager**

- 0.0026745796203613
- 0.0025899410247803
- 0.0018553733825684

**Listenmodul**

- 0.0023412704467773
- 0.0023889541625977
- 0.0058424472808838

## Typo3DatabaseBackend

Bei dem Testserver liegt der MySQL-Server auf der gleichen Festplatte wie der Apache.

**Extensionmanager**

- 0.095011234283447
- 0.097717046737671
- 0.098922491073608

**Listenmodul**

- 0.049378156661987
- 0.050210952758789
- 0.038385629653931

## SimpleFileBackend

Bei dem Testserver handelt es sich um ein SSD basiertes RAID10 mit 4 Festplatten.

**Extensionmanager**

- 0.020318031311035
- 0.023142576217651
- 0.022605180740356

**Listenmodul**

- 0.01460337638855
- 0.0147545337677
- 0.014485120773315

## Fazit

Durch den Einsatz von APCu konnte gegenüber zum Typo3DatabaseBackend eine 41-fache Beschleunigung erreicht werden. Was mich jedoch erstaunt ist, dass das SimpleFileBackend gegenüber dem Typo3DatabaseBackend um Faktor 4 schneller ist. Ich vermute, dass dieser Unterschied vor allem durch den Einsatz des SSD RAIDs kommt und dass keine Authorisierung und Analyse der Datenbankquery wie im Typo3DatabaseBackend vonnöten ist.
