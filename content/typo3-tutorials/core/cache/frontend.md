+++
title = "Caching Framework: Frontend"
linkTitle = "Frontend"
date = 2024-01-06T22:32:08+01:00
slug = "frontend"
aliases = ["frontend.html"]
+++

Mit Frontends kann pro Cache angegeben werden WIE die Daten gespeichert werden sollen. Nicht WO! Das Caching Framework bietet derzeit 3 Frontends an:

## \TYPO3\CMS\Core\Cache\Frontend\VariableFrontend

Der Allrounder unter den Frontends und somit der Standard, der von TYPO3 verwendet wird, wenn kein Frontend in der Cache-Konfiguration angegeben wurde.

Mit diesem Frontend könnt ihr sämtliche Datentypen verwalten, die serialisierbar sind `serialize()`. Dazu zählen Strings, Integer, Boolesche Werte, Arrays und Objekte. Nur im Falle des TransientMemoryBackends werden die zu cachenden Daten nicht serialisiert. Bis TYPO3 7.6 gibt es die Möglichkeit mithilfe des zusätzlichen PHP Modules igbinary das Serialisieren zu beschleunigen. Da dieses Modul jedoch kein Standard auf Hostingpaketen ist und auch die Entwicklung sehr hinterher hinkt, wurde igbinary mit TYPO3 8.0 aus dem Core entfernt: review.typo3.org

## \TYPO3\CMS\Core\Cache\Frontend\StringFrontend

Mit diesem Frontend könnt ihr nur Strings/Texte abspeichern. Da hier im Gegensatz zum VariableFrontend das `serialize()` wegfällt, ist dieser Cache einen Hauch schneller. Andere Datentypen werden mit folgender Fehlermeldung quittiert:

{{% notice style="red" title="Error" %}}
Given data is of type "array", but a string is expected for string cache.
{{% /notice %}}

## \TYPO3\CMS\Core\Cache\Frontend\PhpFrontend

Dieses Frontend ist eine Erweiterung des StringFrontends und akzeptiert somit nur den Datentyp String. Alles andere wird mit dieser Fehlermeldung quittiert:

{{% notice style="red" title="Error" %}}
The given source code is not a valid string.
{{% /notice %}}

Das Besondere an diesem Frontend ist: Ihr könnt PHP Quelltext (ohne "<?php") an den Cache schicken. Dieses Frontend klappt nur in Verbindung mit den dateibasierten Backends: SimpleFileBackend und FileBackend. Das liegt daran, weil `require_once()` einen Dateipfad erfordert.

Anstatt mit `get()` den Quellcode wieder aus dem Cache abzuholen, solltet ihr die Methode `requireOnce()` bevorzugen. Diese prüft, ob die Datei bzw. der Cacheeintrag existiert und läd den Inhalt direkt mittels `require_once()`.
