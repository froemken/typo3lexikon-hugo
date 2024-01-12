+++
title = "Was ist Git?"
linkTitle = "Git"
date = 2024-01-09T21:02:24+01:00
alwaysopen = false
slug = "git"
aliases = ["git/was-ist-git-genau"]
+++

Bei Git handelt es sich um ein Programm, das für die Versionsverwaltung von Dateien verwendet werden kann.

## Versionsverwaltung

Eine Versionsverwaltung ist eine Art Änderungsprotokoll. Immer wenn ihr etwas an euren Dateien geändert habt, könnt ihr diese Änderungen in das Protokoll `einchecken` und diese Änderung wird zusätzlich mit eurem Namen und der Uhrzeit im Protokoll festgehalten. Das Besondere an einer solchen Versionsverwaltung ist, dass nicht nur euer Name und das Datum festgehalten werden, sondern auch die Unterschiede zwischen der vorherigen und der neuen Version eurer Dateien im Protokoll gespeichert werden.

Wenn ihr merkt, dass ihr euch beim Programmieren verrannt habt, könnt ihr im Änderungsprotokoll nach einer früheren Version eurer Arbeit suchen und diese wiederherstellen. Je nachdem welches Programm ihr für die Versionsverwaltung benutzt, enthalten die Protokolleinträge unterschiedliche Ansätze, wie eine frühere Version wiederhergestellt werden kann. Der Vorteil von `Git` ist, dass es nur die Unterschiede zwischen früheren und neueren Versionen speichert. Dadurch sind die Protokolleinträge relativ klein. Nur wenn eine neue Datei in die Versionsverwaltung aufgenommen wird, wird sie vollständig im Protokoll gespeichert. Selbst wenn ihr später 1000de Änderungen an dieser Datei im Protokoll speichert, müssen auch 1000de Protokolleinträge verarbeitet werden, um die Datei auf den neuesten Stand zu bringen.

### Weiterführende Dokumentation

Ich habe im Internet eine [Git Anleitung](https://git-scm.com/book/de/v2) gefunden, die `Git` sehr gut erklärt. Ich selbst habe immer wieder auf diese Dokumentation zurückgegriffen, wenn ich nicht weiterkam. Vielleicht ist es auch für euch eine gute Einstiegshilfe. Und das Coole ist: Es gibt sie sogar auf Deutsch.
