+++
title = "Lokales Git-Repo"
date = 2024-01-09T21:02:46+01:00
slug = "lokales-git-repository"
aliases = ["lokales-git-repo.html", "mein-eigenes-lokales-git-repository.html"]
+++

Geht in ein Verzeichnis Eurer Wahl und legt einen neuen Unterordner an. Zum Beispiel:

```shell
mkdir Klausur
```

Wechselt nun in dieses neu erstellte Verzeichnis

```shell
cd Klausur
```

Aktiviert nun für dieses Verzeichnis die Git-Versionsverwaltung:

```shell
git init
```

Wenn Ihr Git richtig installiert habt, dann solltet ihr nun eine Meldung dieser Art erhalten:

```shell
Initialized empty Git repository in /Pfad/zum/Verzeichnis/Klausur/.git/
```

Ab jetzt können wir Dateien anlegen und ein wenig experimentieren. Ihr könnt einen Einzeiler in eine Datei schreiben:

```shell
echo "Hallo zusammen" >> hallo.txt
```

Ihr könnt mithilfe von `vim`, `nano` oder anderen Konsolen basierten Editoren, Dateien erzeugen.

```shell
vi myDocument.txt
```

Oder auch irgendwelche Dateien in dieses Verzeichnis hinein kopieren. Nachdem ihr nun ein paar Dateien in dieses Verzeichnis hineinkopiert oder anderweitig erstellt habt, können wir uns anschauen, was Git dazu sagt:

```shell
git status
```

Das Ergebnis sollte ähnlich diesem hier aussehen:

```shell
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	Lieblingszitate.txt
#	hallo.txt
#	myDocument.txt
```

Bei `Untracked files` handelt es sich um Dateien, die sich zwar im Verzeichnis befinden, aber noch nicht in die Git-Versionsverwaltung aufgenommen wurden. Im nächsten Schritt werden wir eine von den 3 Dateien für die Übergabe (`commit`) an die Git-Versionsverwaltung anmelden (`staging`).

```shell
git add hallo.txt
```

Dieser Befehl liefert kein Ergebnis zurück. Er wird einfach nur ausgeführt und Ende. Ihr könnt ihn auch mehrfach ausführen, aber auch das wird nichts daran ändern. Nur wenn Ihr Dateien angebt, die nicht im Verzeichnis enthalten sind, meckert er:

```shell
fatal: pathspec 'bla' did not match any files
```

Schauen wir uns an, was Git dazu sagt:

```shell
git status
```

liefert nun:

```shell
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#	new file:   hallo.txt
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	Lieblingszitate.txt
#	myDocument.txt
```

Wie Ihr seht, wurde die Datei `hallo.txt` bei Git angemeldet und steht für die Übergabe (`commit`) an die Git-Versionsverwaltun bereit.

```shell
git commit
```

Nicht erschrecken. Je nach Betriebssystem und Einstellungen öffnet sich nun ein Editor, in dem ihr eine kurze Übergabenachricht angebt. Hier könnt Ihr angeben, was sich zum Beispiel zu vorherigen Versionen geändert hat. In unserem Fall gibt es noch keine vorherigen Versionen. Also tragt einfach so was wie `Erster Commit` ein.

Über den Editor habt Ihr die Möglichkeit mehrzeilige Nachrichten anzugeben. Bei so Einzeilern, wie wir sie gerade hatten, könnt ihr die Nachricht auch direkt im Git-Befehl mit angeben:

```shell
git commit -m "Erster Commit"
```

Git quittiert uns den Befehl wie folgt:

```shell
[master (root-commit) f5c9615] Erster Commit
1 file changed, 1 insertion(+)
create mode 100644 hallo.txt
```

Git zeigt uns hier nochmal unsere Nachricht an und informiert uns darüber, dass eine Datei in die Git-Versionsverwaltung aufgenommen wurde und insgesamt 1 Zeile übertragen wurde.

Fügt der Datei `hallo.txt` nun noch ein paar Zeilen hinzu und schaut euch die Ausgabe von `git status` noch mal an:

```shell
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   hallo.txt
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	Lieblingszitate.txt
#	myDocument.txt
no changes added to commit (use "git add" and/or "git commit -a")
```

Anders als bei SVN (Subversion) muss bei Git jede zu übertragende Datei vorher mit `git add <PfadZurDatei>` für die Übertragung in die Git-Versionsverwaltung angemeldet werden. Ihr könnt gerne probieren `git commit` ohne vorherigem `git add hallo.txt` auszuführen. Als Ergebnis erhaltet ihr die gleiche Ausgabe, wie gerade von `git status`. Im Prinzip ist also nichts passiert.

Der Statusmeldung könnt Ihr entnehmen, dass Git bemerkt hat, dass es sich bei der Datei `hallo.txt` nicht mehr um eine neue, sondern um eine veränderte Datei (modified) handelt. Git empfiehlt uns diese Datei für die Übertragung in die Versionierung anzumelden:

```shell
git add hallo.txt
```

und wieder der Befehl, um diese Änderung zu übertragen:

```shell
git commit -m "Datei ist nun mehrzeilig"
```

Das Ergebnis:

```shell
[master f5b3469] Datei ist nun mehrzeilig
1 file changed, 4 insertions(+)
```

Im Gegensatz zu gerade wurde nun 1 Datei mit 4 zusätzlichen Zeilen in der Git-Versionsverwaltung aufgenommen.

Fügen wir nun noch die beiden anderen Dateien in die Git-Versionsverwaltung mit auf. Zwar könnten wir für jede Datei den Befehl `git add <PfadZurDatei>` aufrufen, aber spätestens bei mehreren hundert Dateien würde die Arbeit sehr schnell langweilig. Teilen wir Git nun mit, dass es alle Dateien des aktuellen Verzeichnisses für die Übertragung anmelden soll:

```shell
git add .
```

Bitte vergesst den Punkt am Ende nicht, sonst meckert Git:

```shell
Nothing specified, nothing added.
Maybe you wanted to say 'git add .'?
```

Ist Git nicht cool? Es hat gemerkt, was wir vorhaben und schlägt uns vor, den Befehl mit einem Punkt am Ende noch mal auszuführen. Ein `git status` zeigt uns nun folgende Ausgabe:

```shell
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
# new file:   Lieblingszitate.txt
# new file:   myDocument.txt
#
```

Die beiden Dateien wurden als neu erkannt und stehen nun zur Übertragung bereit:

```shell
git commit -m "Zwei Dateien hinzugefügt"
```

Jetzt wo alle Dateien des Verzeichnis versioniert sind, wird auch die Statusmeldung erheblich kürzer:

```shell
# On branch master
nothing to commit (working directory clean)
```

Es gibt nichts zu übertragen und unsere Arbeitsumgebung (unser aktuelles Verzeichnis) entspricht exakt dem Stand in der Git-Versionsverwaltung.

## Der Zweig (Branch)

Mit jedem Commit wird ein neuer Protokolleintrag in der Git-Versionsverwaltung erzeugt. Ihr könnt Euch die erstellten Protokolle mit folgendem Befehl anschauen:

```shell
git log
```

Die Ausgabe schaut ungefähr so aus:

```shell
commit 6ddf8a28004432e1c550fba9886e33c5a703c322
Author: Stefan Frömken <Mailadresse>
Date:   Sat Feb 9 21:14:31 2013 +0100
Zwei Dateien hinzugefügt
commit f5b346905068441bb1bef77871d400f33c89901a
Author: Stefan Frömken <Mailadresse>
Date:   Sat Feb 9 21:00:53 2013 +0100
Datei ist nun mehrzeilig
commit e4d74e0479dac9ffa20c2477c0d028be203af7cf
Author: Stefan Frömken <Mailadresse>
Date:   Sat Feb 9 20:47:02 2013 +0100
Erster Commit
commit e279d347750ba0b6413f1e56859ecbe69b9980be
Author: Stefan Frömken <Mailadresse>
Date:   Sat Feb 9 20:43:43 2013 +0100
Mehrzeilig
commit f5c961542e7cd0f21af823d189600ebdf294a1c2
Author: Stefan Frömken <Mailadresse>Date:   Sat Feb 9 20:29:11 2013 +0100
Erster Commit
```

Der erste Eintrag ist immer der Protokolleintrag, der mit dem letzten Commit erzeugt wurde. Ihr seht, dass ich bereits 5 Commits durchgeführt und somit 5 Protokolleinträge in der Git-Versionsverwaltung erstellt habe. Jeder Protokolleintrag erhält einen Schlüssel (SHA1) der einen Commit immer eindeutig identifizieren kann. All diese Commits werden in einer Art Inhaltsverzeichnis (Index) festgehalten.

## Jetzt kommt Bewegung in die Versionierung

Wir haben gerade gelernt, dass mit jedem Commit ein neuer Protokolleintrag in der Git-Versionsverwaltung erstellt wird und jeder Protokolleintrag durch einen eindeutigen Schlüssel zu identifizieren ist.

Im folgenden Beispiel werden wir die Datei `hallo.txt` mit der Version unseres ersten Commits vergleichen. Dazu teilen wir Git mit, die Version des ersten Commits zu laden ohne die Dateien im aktuellen Verzeichnis zu verändern. Dazu suchen wir uns mithilfe von "git log" den eindeutigen 40-Zeichen langen Schlüssel raus und gebe nun folgenden Befehl ein:

```shell
git reset f5c961542e7cd0f21af823d189600ebdf294a1c2
```

Ersetzt den angegebenen Schlüssel hier im Beispiel durch den Schlüssel aus euren eigenen Protokollen.

Git hat nun den Cursor, Zeiger, die interne Positionsangabe oder wie auch immer Ihr es bezeichnen wollt auf den ersten Commit verschoben. Lasst uns das mal mit `git status` genauer anschauen:

```shell
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   hallo.txt
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	Lieblingszitate.txt
#	myDocument.txt
no changes added to commit (use "git add" and/or "git commit -a")
```

Wow. Krass. Git weiß noch, dass nach unserem ersten Commit die beiden Dateien `Lieblingszitate.txt` und `myDocuments.txt` noch nicht in die Git-Versionsverwaltung übertragen wurden. Aber hey! Was ist mit der `hallo.txt`? Stand damals nicht `new file` dort? Richtig! Git vergleicht in seiner Statusmeldung den alten Stand mit dem aktuellen Stand in unserem Verzeichnis und hat dabei festgestellt, dass die Datei `hallo.txt` einen anderen Inhalt aufweist als damals nach dem ersten Commit.

Um nun unsere aktuelle `hallo.txt` mit der ersten Version zu vergleichen reicht folgender Befehl:

```shell
git diff hallo.txt
```

Ihr könnt aber auch alle Dateien aus eurem Verzeichnis mit allen Dateien aus der ersten Version vergleichen, in dem ihr das hier eingebt:

```shell
git diff
```

Lasst uns jetzt mal zu unserem 3ten Branch wechseln. Suchen wir also wieder den eindeutigen Schlüssel mit:

```shell
git log
```

Ja Rotz noch mal, was ist denn das:

```shell
commit f5c961542e7cd0f21af823d189600ebdf294a1c2
Author: Stefan Frömken <Mailadresse>
Date:   Sat Feb 9 20:29:11 2013 +0100
Erster Commit
```

Wo sind denn die ganzen Commits hin? Tja, das ist Git. Wir haben Git vorhin mitgeteilt, dass es auf den ersten Commit zurückspringen soll und innerhalb dieser Version weiß Git einfach nicht was zukünftig passierte. Wir haben jetzt 3 Möglichkeiten:

1. Wir scrollen in unserer Konsole ein bisschen hoch und hoffen noch irgendwo die vollständige Auflistung von `git log` zu finden und springen dann wieder mit `git reset <Schlüssel>` zu der gewünschten Position.
2. Git speichert die Stelle, auf der wir uns zuletzt befunden haben immer in der Variable `ORIG_HEAD`. Da wir uns zuvor noch auf dem aktuellsten Branch befunden haben, können wir ganz einfach zu dieser Stelle zurückspringen:

   ```shell
   git reset ORIG_HEAD
   ```

3. Es wird kompliziert. Solltet Ihr mehrere verschiedene Commits mit `git reset <Schlüssel>` hintereinander angesprungen sein, dann zeigt `ORIG_HEAD` immer auf den vorherigen Branch. In vielen Foren wird behauptet, dass `ORIG_HEAD` immer auf den aktuellsten Branch zeigt. Das ist falsch! Wenn ihr mit `git reset` 3 mal durch die unterschiedlichsten Commits gesprungen seid, dann ist `ORIG_HEAD` ein Verweis auf den 2ten Branch, den ihr angesprungen seid.

Ich habe euch erzählt, dass mit jedem Commit ein neuer Protokolleintrag mit einem eindeutigen Schlüssel angelegt wird. Neben dem `ORIG_HEAD` gibt es eine weitere Variable mit dem Namen `HEAD`. Diese Variable zeigt immer auf den Branch, der mit dem letzten `git commit` erzeugt wurde. Jedes mal, wenn diese Variable `HEAD` auf einen anderen (`git reset`) oder einen neuen (`git commit`) Protokolleintrag zeigt, wird diese Variablenänderung in einer sogenannten `Referencelog` festgehalten. Lasst sie euch anzeigen:

```shell
git reflog
```

Bei mir schaut die so aus:

```shell
e4d74e0 HEAD@{0}: reset: moving to HEAD@{1}
f5c9615 HEAD@{1}: reset: moving to HEAD@{3}
e4d74e0 HEAD@{2}: reset: moving to HEAD@{1}
f5c9615 HEAD@{3}: reset: moving to ORIG_HEAD
e4d74e0 HEAD@{4}: reset: moving to e4d74e0479dac9ffa20c2477c0d028be203af7cf
f5c9615 HEAD@{5}: reset: moving to f5c961542e7cd0f21af823d189600ebdf294a1c2
6ddf8a2 HEAD@{6}: commit: Zwei Dateien hinzugefügt
f5b3469 HEAD@{7}: commit: Datei ist nun mehrzeilig
e4d74e0 HEAD@{8}: commit: Erster Commit
e279d34 HEAD@{9}: commit: Mehrzeilig
f5c9615 HEAD@{10}: commit (initial): Erster Commit
```

Hier seht Ihr wie oft und auf welchen Commit die Variable `HEAD` verändert wurde. Während ihr mit `git reset` einen Abstecher in die Vergangenheit macht, macht ihr mit `git commit` einen Sprung zurück in die Zukunft. Demnach sollte klar sein, dass die Variable `HEAD` mit dem 6ten Stand auf den aktuellsten Branch zeigt. Wer mag, kann dies in einer noch detaillierteren Aufstellung überprüfen:

```shell
git log -g
```

Es öffnet sich wieder ein Editor. Sucht nach `HEAD@{6}` aber ersetzt die 6 durch den Stand, der bei Euch zutrifft. 3 Zeilen tiefer steht auch immer das Datum mit genauer Uhrzeit. Hierbei handelt es sich nicht um das Datum, wann der Wert von `HEAD` verändert wurde, sondert um den Zeitstempel, wann der Branch erstellt wurde. In meinem Fall haben `HEAD@{10}`, `HEAD@{5}`, `HEAD@{3}` und `HEAD@{1}` alle auf den Commit gezeigt, der mit dem Schlüssel `f5c9615` anfing. Somit beinhalten diese 4 HEAD-Änderungen in der Logdatei auch alle das gleiche Datum.

Zurück zu unserer Aufgabe: Im Log haben wir festgestellt, dass `HEAD@{6}` auf den aktuellsten Branch zeigt. Nun also 3 Möglichkeiten, um wieder zu dem aktuellsten Stand zu gelangen:

1. Unter Verwendung dieser besonderen HEAD-Notation:

   ```shell
   git reset HEAD@{6}
   ```

2. Unter Verwendung des vollständigen Schlüssels:

   ```shell
   git reset 6ddf8a28004432e1c550fba9886e33c5a703c322
   ```

3. Unter Verwendung des verkürzten Schlüssels. Git erlaubt es uns auch nur die ersten paar Zeichen des Schlüssels anzugeben. Mindestens müssen jedoch 4 Zeichen angegeben werden:

   ```shell
   git reset 6ddf
   ```

Es können aber auch die ersten 6, 13 oder 20 Zeichen sein. Das bleibt euch überlassen.

## Weitere Möglichkeiten mit `git reset`

Alle vorherigen `git reset` Befehle hätte man auch so schreiben können:

```shell
git reset --mixed <Schlüssel>
```

`mixed` ist die Standardoption für `git reset` und bedeutet, dass innerhalb der Versionierung der Commit gewechselt wird, aber die Dateien in unserem Verzeichnis unberührt bleiben. Neben `--mixed` gibt es noch weitere Optionen:

```shell
git reset --soft <Schlüssel>
```

`soft` gibt an, dass nur die Variable `HEAD` auf den Commit, mit dem angegeben Schlüssel zeigen soll. Es wird kein Commit aus der Git-Versionsverwaltung geladen und es wird auch keine Datei in unserem Verzeichnis verändert. Demnach liefert auch `git diff` keine Veränderungen. Nur `git status` zeigt uns, welche Dateien sich zu den aktuellen Dateien im Verzeichnis verändert haben.

```shell
git reset --hard <Schlüssel>
```

`hard` setzt `HEAD` auf den angegebenen Schlüssel und läd die Dateien aus diesem Commit. Hinzu kommt, dass alle Dateien in unserem Verzeichnis, mit denen aus dem angegebenen Commit überschrieben werden (Das trifft nur auf versionierte Dateien zu). Das ist z.B. nützlich, wenn ihr in einem größeren Projekt mal was testen wollt und dadurch viele Änderungen eingebaut habt. Falls der Test fehlschlägt, wollt ihr natürlich möglichst zügig wieder den Ursprungszustand laden, ohne alle Änderungen manuell mit STRG-Z oder ähnlichem rückgängig zu machen. Hier hilft dann ein:

```shell
git reset --hard HEAD
```

`HEAD` zeigt immer auf den aktuellen Commit, in dem ihr euch befindet. Somit sind alle Änderungen von da an wieder verworfen und wieder auf dem Stand wie vor den Änderungen, die für den Test notwendig waren.

## Änderungen wieder herstellen

Wir haben gerade die Datei `hallo.txt` wieder aus dem Staging-Bereich entfernt, um an dieser Datei noch ein paar Änderungen vorzunehmen. Schauen wir uns doch erstmal an, was für Änderungen wir vorgenommen hatten:

```shell
git diff
```

Ergebnis:

```shell
diff --git a/hallo.txt b/hallo.txt
index b8800e2..5147b12 100644
--- a/hallo.txt
+++ b/hallo.txt
@@ -1,5 +1,5 @@
Hallo zusammen
-Wie geht es Euch
+Wie geht es Euch?
Mir geht es gut

Stefan
```

Mit jedem Commit wird auch immer eine neue Version in Git erstellt. Jede dieser Versionen erhält einen eindeutigen SHA1-Schlüssel. Da dieser Schlüssel sehr lang ist (40 Zeichen) verwendet Git immer nur die ersten 7 Zeichen. Wir sehen hier nun, dass Git uns die Unterschiede zwischen der Version `b8800e2` und der Version `5147b12` aufführt. Weiter unten sehen wir ein paar Zeilen meiner Datei. Zeilen ohne `+` oder `-` sind unveränderte Zeilen. Die Zeile mit dem `-` wurde in der bearbeiteten Datei entfernt und stattdessen die Zeile mit dem `+` eingefügt.

Um jetzt die Änderungen, in der `hallo.txt` wieder auf den Stand des letzten Commits zu setzen, können wir diese Datei entweder editieren und das Fragezeichen entfernen oder aber wir lassen Git die Arbeit für uns machen:

```shell
git reset --hard HEAD
```

Der Parameter `--hard` bedeutet, dass nicht nur der Index der Versionierung auf den Stand von `HEAD` verschoben werden soll, sondern auch die Dateien innerhalb unseres Verzeichnisses. Alle Änderungen an unseren versionierten Dateien in unserem Verzeichnis werden also wieder rückgängig gemacht.

## Arbeiten mit dem Staging

Beim Staging handelt es sich um Dateien, die für die Übertragung in die Git-Versionsverwaltung angemeldet wurden. Jede Datei, die mit `git add` angemeldet wurde, befindet sich somit im Übertragungsbereich (Staging).

Ändern wir mal alle Dateien und bewegen diese in den Stagingbereich:

```shell
git add .
```

Und genau jetzt fällt uns ein, dass wir die Änderungen an der Datei `hallo.txt` doch noch nicht in die Git-Versionsverwaltung übertragen wollen. Aber wie können wir solche Dateien von der anschließenden Übertragung wieder ausschließen? Führen wir dazu ein `git status` aus:

```shell
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
# modified:   Lieblingszitate.txt
# modified:   hallo.txt
# modified:   myDocument.txt
```

Ist Git nicht nett? Es zeigt uns den Befehl zum Entfernen sogar direkt an:

```shell
git reset HEAD hallo.txt
```

Als Ergebnis erhalten wir:

```shell
Unstaged changes after reset:
M	hallo.txt
```

Git informiert uns darüber, dass sich in unserem Verzeichnis nun eine veränderte (M von modified) Datei mit dem Namen `hallo.txt` befindet und mit der nächsten Übertragung (Commit) nicht in die Git-Versionsverwaltung aufgenommen wird. Wunderbar: Ziel erreicht.
