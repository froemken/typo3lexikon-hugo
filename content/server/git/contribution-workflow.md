+++
title = "Ich will den Core patchen. Was ist zu tun?"
linkTitle = "Git für TYPO3 einrichten"
date = 2024-01-09T21:03:41+01:00
slug = "git-fuer-typo3-einrichten"
aliases = ["git-fuer-typo3-einrichten.html"]
+++

Hast Du Zeit? Viel Zeit? Na dann los:

## 1. Du brauchst einen TYPO3-Account

Geh auf die TYPO3-Seite und erstelle dir einen Benutzeraccount. Du findest ihn ein bisschen versteckt oben unter `Login` und dann in dem kleinen Fenster unten bei `Sign up!`. Nachdem du den Registrierungsprozess vollendet hast, gehörst du zur TYPO3-Community. Willkommen :-) Achte gut auf deinen TYPO3-Account, denn er ist nicht nur die Eintrittskarte für [typo3.org](https://www.typo3.org), sondern auch für sämtliche Unterprojekte wie z.B.

- [Review](http://review.typo3.org)
- [Forge](http://forge.typo3.org)
- [Git](http://git.typo3.org)

und vielen mehr.

## 2. Du musst der CLA zustimmen

Um am Quellcode von TYPO3 Flow mitzuentwickeln wird eine sogenannte Contributor License Agreement benötigt. Diese klärt die Nutzerrechte zwischen der TYPO3 Association und euch. Peter Kraume hat dazu einen sehr ausführlichen Bericht auf [typo3blogger](https://typo3blogger.de/was-ist-eigentlich-ein-contributor-license-agreement/) gepostet.

Alle Projekte, die auf Basis von TYPO3 Flow entwickelt wurden und zurückportiert wurden in den Quellcode von TYPO3 CMS wie zum Beispiel `Extbase` und `Fluid` erfordern ebenfalls diese unterschriebene CLA. Ohne CLA könnt ihr keine Patches für diese Extensions committen.

Nur der reine TYPO3-Quellcode und die nicht von TYPO3 Flow zurückportierten System-Extensions benötigen diese CLA nicht.

Es gibt wohl schon Überlegungen, die CLA auch für den TYPO3 Quellcode erforderlich zu machen. Da es also nur noch eine Frage der Zeit ist, könnt ihr die CLA auch jetzt schon unterschreiben.

## 3. Einen SSH-Key erstellen

Die Verbindung zwischen euch und dem TYPO3-Server muss immer abgesichert sein. Dafür gibt es SSH-Keys. [Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) hat dazu eine gute englische Dokumentation.

- Loggt Euch mit Eurem TYPO3-Account auf dem Review-Server ein
- Klickt dann oben rechts auf `Settings` und dann links auf `SSH Public Keys`. Wenn ihr bereits über einen public SSH-Key verfügt, dann könnt ihr diesen nun hier eintragen und die weiteren Punkte von Schritt 3 überspringen.
- Prüft, ob ihr bereits einen SSH-Key eingerichtet habt

  Öffnet eine Shell und wechselt in das versteckte `.ssh` -Verzeichnis:

  ```shell
  cd ~/.ssh
  ```

  Mit `ls -l` lasst ihr euch den Inhalt dieses Verzeichnisses anzeigen.

  Falls ihr in diesem Verzeichnis bereits einen Key erstellt habt, dann editiert die Datei (üblicherweise `id_rsa.pub`) und kopiert den Inhalt auf den Review-Server.

- Das versteckte Verzeichnis erstellen:

  Falls das Verzeichnis noch nicht existieren sollte, dann könnt ihr es folgendermaßen erstellen:

  ```shell
  mkdir ~/.ssh
  ```

- Den neuen SSH-Key generieren:

  Gebt folgenden Befehl ein:

  ```shell
  ssh-keygen -t rsa -C "DeineEmailAdresse"
  ```

  Ihr werdet gefragt, wohin der SSH-Key abgelegt werden soll. Wenn ihr noch keinen SSH-Key generiert habt, dann könnt Ihr den Pfad, wie vorgeschlagen, mit Enter bestätigen. Falls Ihr parallel zu Eurem bereits vorhandenem SSH-Key einen zusätzlichen, nur für TYPO3 gültigen SSH-Key generieren wollt, müsst Ihr den vorgeschlagenen Dateinamen anpassen. Z.B. `/home/stefan/.ssh/typo3`

  Es folgt die Frage nach einer Passphrase. Nennen wir es mal vereinfacht `Passwort`. Ihr könnt selber entscheiden, ob ihr eurem SSH-Key ein Passwort mitgeben wollt. Wenn jedoch ein Passwort angegeben wird, dann wird euch der Review-Server mit jeder neuen SSH-Verbindung, die ihr später aufbauen werden nach diesem Passwort fragen. Sicherheit geht vor, aber kein Passwort anzugeben funktioniert später genauso gut.

  In dem `.ssh` Verzeichnis befinden sich nun zwei neue Dateien. Eine davon endet auf `.pub`. Diese müsst Ihr editieren und den Inhalt auf den Review-Server kopieren.

- Für Spezialisten, die einen eigenen Dateinamen angegeben haben:

  Standardmäßig wird für jede neu erstellte SSH-Verbindung immer der SSH-Key aus der Datei `id_rsa` verwendet. Um SSH mitzuteilen, dass es für die Verbindung zum Review-Server einen anderen SSH-Key verwenden soll, müsst ihr eine `config` Datei im `.ssh` Verzeichnis erstellen. Der Inhalt könnte ungefähr so aussehen:

  ```shell
  Host review.typo3.org
  HostName review.typo3.org
  User froemken
  IdentityFile ~/.ssh/typo3
  ```
  
  Den Benutzernamen und den Pfad zum SSH-Key müsst Ihr natürlich anpassen. Als `HostName` könnt ihr die IP-Adresse des Review-Servers angeben. Da diese sich gelegentlich ändert, empfehle ich den Hostnamen wie oben einzutragen.

## 4. Git konfigurieren

Beim Übertragen von Patches zum Review-Server gibt es noch etwas, das überprüft wird, und zwar eure E-Mail-Adresse. Deshalb ist es sehr wichtig, dass die E-Mail-Adresse eures TYPO3-Benutzeraccounts unter Punkt 1. exakt der angegebenen E-Mail-Adresse in eurer Git-Konfiguration entspricht.

Geht dazu in das Verzeichnis des TYPO3-Quellcodes und gebt folgenden Befehl ein:

```shell
ls -lisa
```

Wenn in der Ausgabe ganz oben ein paar Dateien zu sehen sind, die mit `.git` anfangen, dann seid ihr richtig. Dann teilen wir Git mal mit wer wir sind und gebt die korrekte E-Mail-Adresse an:

```shell
git config --local user.name "Dein Vor- und Nachname"
git config --local user.email "DeineEmailAdresseVomTYPO3Account"
```

Damit ist die Konfiguration nur für dieses eine Verzeichnis gesetzt. Wenn ihr die absoluten TYPO3-Spezialisten seit und euer Computer/Mac nur TYPO3 kennt, für den empfiehlt es sich die Konfiguration global für alle Git-Verzeichnisse zu setzen:

```shell
git config --global user.name "Dein Vor- und Nachname"
git config --global user.email "DeineEmailAdresseVomTYPO3Account"
```

## Git Hook aktivieren

Ähnlich wie die Hooks in TYPO3, so kann auch Git bei bestimmten Prozessen durch weitere Funktionalität erweitert werden. Diese Hooks hat sich das TYPO3-Serverteam zunutze gemacht und beim Erstellen der Commit-Nachrichten (`git commit -m "Commitnachricht"`) eingegriffen. Egal was ihr für eine Commit-Nachricht angebt, dieser Hook fügt allen Nachrichten eine sogenannte `Change-Id` an. Dabei handelt es sich um den eindeutigen Schlüssel, der mit jedem Commit für eine Version in der Git-Versionierung erzeugt wird. Ich hatte den Schlüssel bereits in meinem vorherigen Tutorial erklärt: Abschnitt `Der Zweig (Branch)`.

Gebt nun folgenden Befehl ein, um den Hook herunterzuladen und zu aktivieren:

```shell
curl -o .git/hooks/commit-msg "https://typo3.org/fileadmin/resources/git/commit-msg.txt" && chmod +x .git/hooks/commit-msg
```

## Die Untermodule bei Git anmelden

Einige Extensions wie `extbase` und `fluid` wurden als sogenannte `Submodules` in die Versionierung des TYPO3-Quellcodes implementiert. Damit muss man als Entwickler nicht ständig den kompletten TYPO3-Quellcode updaten, sondern kann völlig autark in dem Verzeichnis dieser Submodule arbeiten. Trotz allem müssen diese Submodule bei Git angemeldet werden:

```shell
git submodule update --init
```

## Den Hook für die Submodule einbinden

Wir haben vorhin einen Hook für Git angemeldet. Das Gleiche müssen wir nun auch für die Submodule machen, da diese, wie bereits erwählt, völlig autark arbeiten. Die wissen also noch nichts von irgendwelchen Hooks:

```shell
for module in .git/modules/typo3/sysext/*; do curl -o $module/hooks/commit-msg "https://typo3.org/fileadmin/resources/git/commit-msg.txt" && chmod +x $module/hooks/commit-msg; done
```

Es handelt sich bei diesem Befehl, um eine Schleife, die in jedes Verzeichnis der TYPO3 System-Extensions geht und überprüft, ob es sich dabei um eine Extension handelt, die als Submodule angelegt wurde. Wenn ja, dann wird dort der Hook eingebaut. Wenn Ihr also demnächst für das Projekt `extbase` programmieren solltet, werden alle eure Commitnachrichten mit der zusätzlichen `Change-Id` versehen.

## Sende die Patches zum Review-Server

Ich hatte weiter oben erklärt, dass die TYPO3-Quellen vom TYPO3 Gitserver geladen werden: [TYPO3 Git](http://git.typo3.org). Wenn wir nun aber einen Patch senden (`git push`), würde dieser Patch direkt in den TYPO3-Quellen landen. Das hätte verheerende Folgen, wenn jeder direkten Zugriff auf die TYPO3-Quellen hätte. Jeder könnte tun und lassen, was er will. Das kann und darf nicht sein. Deshalb hat das TYPO3-Serverteam den Review-Server aufgesetzt. Hier landen alle Patches und können von anderen Entwicklern überprüft und auch getestet werden. Erst wenn genügend Entwickler den Patch für OK befunden haben, wird dieser Patch vom Review-Server aus in die TYPO3-Quellen gepushed.

Wir müssen Git also mitteilen, dass die Patches nicht auf dem Git-Server landen, sondern erstmal zum Review-Server geschickt werden:

```shell
git config --global url."ssh://<username>@review.typo3.org:29418".pushInsteadOf git://git.typo3.org
git config --global branch.autosetuprebase remote
```

Wunderbar, ihr habt es geschafft. Euer Gitverzeichnis ist nun fertig eingerichtet. Die TYPO3-Community freut sich auf Eure Patches.
