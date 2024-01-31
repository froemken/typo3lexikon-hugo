+++
title = "smilie"
date = 2024-01-09T20:52:56+01:00
aliases = ["smile.html"]
+++

Mit der Extension `smilie` werden als Text geschriebene Smileys z.B. :-) als IMG-Tag ausgegeben. Nach der Installation von `smilie` musst Du in Deinem Template noch das statische Extensiontemplate `smilie` einbinden.

## Es passiert nichts automatisch

Nach der Installation dieser Extension passiert erstmal überhaupt nichts. Evtl. habt ihr dacht, dass nun alle Textsmileys im Bodytext/RTE automatisch in die grafischen Smileys umgewandelt werden. Damit liegt ihr falsch! Wie ihr oben schon gelesen habt, ist diese Extension eher für Extensionprogrammierer gedacht, die ihre eigene oder eine fremde Extension erweitern wollen. Es sollte uns also nicht wundern, dass nur PHP-Code-Beispiele in der smilie-Dokumentation zu finden sind.

## Ein in der Doku fehlendes TS-Beispiel

Hier zuerst die Information, dass der RTE selbst schon eine eigene Smileyunterstützung beinhaltet. Diese muss nur aktiviert werden oder ihr wechselt in den Extensionkonstanten der rtehtmlarea von dem Standardmodus in den "Demomodus".

Solltet ihr das, warum auch immer, nicht wollen, dann könnt ihr die Smilie-Extension folgendermaßen für die `tt_content` Spalte `bodytext` per TS aktivieren:

Tragt in das TS-Setup folgende Zeilen ein:

```typo3_typoscript
includeLibs = typo3conf/ext/smilie/class.tx_smilie.php
lib.parseFunc_RTE.nonTypoTagStdWrap.encapsLines.innerStdWrap_all.preUserFunc = tx_smilie->replaceSmilies
```

Nun könnt Ihr im RTE z.B. :-) eintragen und der Smiley wird als Grafik im Frontend ausgegeben.
