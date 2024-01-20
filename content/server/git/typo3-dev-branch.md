+++
title = "TYPO3 Dev Branch auschecken"
linkTitle = "TYPO3 Dev Branch"
date = 2024-01-09T21:03:27+01:00
slug = "typo3-dev-branch"
aliases = ["typo3-dev-branch.html", "ich-will-den-aktuellen-entwicklungsstand-von-typo3.html"]
+++

Das TYPO3-Serverteam hat einen eigenen [Git-Server](https://git.typo3.org/typo3/typo3) aufgesetzt. Hier findet ihr auch den aktuellen Quellcode von TYPO3, viele Extensions, die bereits vom damaligen SVN-Server auf Git portiert wurden. Der TYPO3 Quellcode befinden sich in dem Projekt `TYPO3v4/Core.git`. Auf dieser Seite seht Ihr welcher Entwickler wann und was zuletzt dem Core hinzugefügt hat und eine URL die mit `git://` anfängt. Diese URL benötigt Ihr nun, um den aktuellen TYPO3-Entwicklungsstand herunterzuladen:

```shell
git clone --recursive git://git.typo3.org/TYPO3v4/Core.git typo3_src-git
```

Es würde auch folgender Befehl funktionieren:

```shell
git clone --recursive git://git.typo3.org/TYPO3v4/Core.git
```

Aber dieser hat den Vorteil/Nachteil, dass der Dateiname auch immer gleich dem neu anzulegenden Verzeichnis ist. Wenn Ihr diesen Befehl zum Beispiel in: `/var/www/` ausführt, dann wird automatisch das Unterverzeichnis `Core` (also: `/var/www/Core`) angelegt. Ich finde, `typo3_src-git` ist aussagekräftiger und empfehle euch bei der ersten Version zu bleiben.

Wunderbar ihr habt nun den aktuellen TYPO3-Entwicklungsstand auf Eurem Rechner.

Ihr könnt jetzt wie gewohnt eure Projekte auf den TYPO3 Quellcode verlinken. Im Projekteverzeichnis (da wo sich auch der `fileadmin` befindet) löscht ihr die vorhandene Verknüpfung `typo3_src` und erstellt eine Neue:

```shell
ln -s ../typo3_scr-git/ ./typo3_src
```

Damit ist Euer TYPO3-Projekt mit den aktuellen Quellen verknüpft. Evtl müsst ihr im Installtool noch ein `Database Compare` durchführen, damit alles so läuft, wie es sollte. Viel Spaß beim Ausprobieren.
