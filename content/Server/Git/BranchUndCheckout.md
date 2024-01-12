+++
title = "Branch und Checkout"
date = 2024-01-09T21:02:55+01:00
slug = "branch-und-checkout"
+++

Wir haben im vorherigen Kapitel Zweige, sogenannte Branches, bereits kennengelernt. Wir konnten uns mithilfe von `git reset` durch die verschiedenen Versionen bewegen. Solche Zweige können nicht nur intern für die Versionierung von Git verwendet werden, sondern auch für die Dateien in Eurem Verzeichnis. Und es kommt noch besser: Für unser Verzeichnis sind Branches nicht einfach nur irgendwelche Zweige, sondern komplette Kopien des Produktivzweigs, dem sogenannten `main`.

Aber jetzt mal alles nacheinander

Gebt mal folgenden Befehl ein

```shell
git branch
```

Ihr seht, dass es bereits einen Branch mit dem Namen `main` gibt:

```shell
* main
```

Dieser Zweig (Branch) ist der Hauptzweig. In manchen Foren und Dokumentationen wird er auch als Produktivzweig bezeichnet. Alle Änderungen und Arbeiten, die wir in den letzten Kapiteln durch geführt haben, haben auf diesem Produktivzweig stattgefunden. Das Sternchen vor `main` besagt, dass es sich hierbei um den Zweig handelt, auf dem wir gerade arbeiten.

Wofür brauche ich denn jetzt meinen eigenen Branch? Wie Du in meinem bisherigen Tutorial gemerkt haben solltest, ist die Welt auch ohne Branches in Ordnung. Wenn Du alleine und nur für Dich an einem Buch oder an Deinem eigenen Projekt sitzt und Dich keiner stresst, dann wirst Du sehr wahrscheinlich keine Branches brauchen. Das ist auch der Grund warum im Internet überall steht, dass man Branches verwenden sollte aber nicht muss.

Aber wenn das Projekt bekannt ist, Kunden auf die Fertigstellung dieses Projektes warten und/oder andere Kollegen dir einreden, dass hier und da noch Fehler auftauchen, dann sind Branches eine wahre Bereicherung. Aber in diesem Fall wird niemals auf dem Produktivzweig (main) gearbeitet! Den Grund erkläre ich später. Stell zunächst sicher, dass Ihr eine saubere Arbeitsumgebung habt:

`git status` sollte Euch so was wie `working directory clean` mitteilen. Wenn das nicht der Fall ist, dann added und committet Eure Änderungen, bevor wir hier weiter machen.

Erstellen wir uns nun einen eigenen Branch:

```shell
git branch Kapitel_23
```

Wie Ihr den Branch benennt, bleibt euch überlassen.

Schaut euch nun die vorhandenen Branches an:

```shell
git branch
```

Es gibt nun 2 Branches:

```shell
Kapitel_2
* main
```

Da ich Euch gesagt habe, dass Ihr unter Verwendung von Branches niemals auf `main` arbeiten sollt, wechseln wir nun mit `git checkout` auf den neu angelegten Branch:

```shell
git checkout Kapitel_23
```

Wenn Ihr `git branch` nochmal eingebt, dann seht ihr, dass das Sternchen nun vor dem Branch `Kapitel_23` steht.

Jetzt tun wir so als würden wir 4 Stunden lang arbeiten und schreiben 2-3 zusätzliche Zeilen Text unten in die `hallo.txt`. Ein anschließendes `git status` sollte euch über die Veränderungen informieren und die Datei `hallo.txt` als `modified` anzeigen.

Es passiert, was kommen musste: Der Kunde ruft an und erklärt, dass er in `Kapitel 2` einen Rechtschreibfehler gefunden hat, der, warum auch immer, sofort zu beheben ist. Gut das wir mit Branches arbeiten. Wir speichern unsere bisherige Arbeit ab und wechseln zu dem Produktivzweig:

```shell
git checkout main
```

Von diesem Zweig aus erstellen wir uns nun einen neuen Branch und wechseln direkt in diesen Branch:

```shell
git checkout -b Fehler_Kapitel_2
```

Dieser Befehl ist eine Kombination aus beiden bisher gelernten Befehlen. Dieser erstellt den Branch und wechselt auch gleich in diesen neuen Branch hinein. Cool, oder?

Bearbeitet nun in der `hallo.txt` irgendwo am Anfang ein Zeichen oder fügt etwas hinzu und speichert die Datei ab. Danach übertragt Ihr diese wieder in die Git-Versionsverwaltung:

```shell
git commit -a -m "Rechtschreibfehler behoben"
```

Der Parameter `-a` meldet automatisch alle bearbeiteten und gelöschten Dateien in der Git-Versionsverwaltung an. Wir konnten den Parameter vorher nicht verwenden, da der Parameter auf neu hinzugefügte Dateien keine Auswirkung hat.

Wechseln wir nun wieder zurück in den Produktivzweig:

```shell
git checkout main
```

Wenn ihr euch nun wieder die Datei `hallo.txt` anschaut, werdet Ihr feststellen, dass eure Änderungen nicht vorhanden sind. Um nun die Änderungen, die ihr in dem Branch `Fehler_Kapitel_2` in den Branch `main` einzubinden verwendet Ihr den `merge`-Befehl:

```shell
git merge Fehler_Kapitel_2
```

Schaut euch jetzt noch mal die Datei `hallo.txt` an. Wunderbar, die Änderungen wurden in den Produktivzweig eingebunden. Somit ist der `main` Branch und der `Fehler_Kapitel_2` Branch auf demselben Stand. Zeit den überflüssigen Branch zu löschen:

```shell
git branch -d Fehler_Kapitel_2
```

Ein Branch kann nur dann gelöscht werden, wenn er nicht aktiv ist. Das erkennt ihr an dem Sternchen `*` nach Eingabe von `git branch`.

Sollten wieder erwartend diese Änderungen nicht mehr von Interesse sein, dann macht es auch keinen Sinn mehr diese Änderungen zu mergen. In diesem besonderen Fall könnt Ihr den Branch folgendermaßen löschen:

```shell
git branch -D Fehler_Kapitel_2
```

Bleibt noch die Antwort auf die Frage, warum man unter Verwendung von Branches niemals auf dem Produktivzweig `main` arbeiten soll. Ganz einfach: Macht mal die Änderungen direkt auf `main`. Nach 4 Stunden fleißigen Schreibens kommt nun der Kundenanruf und Ihr erstellt den neuen Branch zwecks Fehlerbehebung. Dieser neue Branch beinhaltet aber auch alle Texte, die ihr in den letzten 4 Stunden geschrieben habt. Spätestens nach dem `merge` wäre zwar der bemängelte Fehler behoben, aber die unfertige Arbeit eures Kapitels wäre auch in der Git-Versionsverwaltung enthalten. Bei Büchern und Kapiteln mag das alles noch nicht relevant sein, aber im Bereich der Programmierung könnte dieser unfertige Branch dazu führen, dass die Programmierung zu Fehlern im System führt. Deshalb bitte immer einen eigenen Branch für jedes Feature oder anderweitige Programmabschnitte verwenden.
