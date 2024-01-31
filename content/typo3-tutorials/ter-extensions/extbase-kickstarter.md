+++
title = "extbase_kickstarter"
date = 2024-01-09T20:51:15+01:00
aliases = ["extbase-kickstarter.html"]
+++

## Wie komme ich an den extbase_kickstarter?

Fragt mich nicht warum! Obwohl in den TYPO3-Listen mehrfach diskutiert wurde, dass es zu viele Anlaufstellen für Extensions gibt, hat man sich trotzdem dafür entschieden das GIT-Projekt, als weiteres Repository aufzunehmen. So kommt es, dass der `extbase_kickstarter` als Projekt zwar bei [Forge](https://forge.typo3.org) gelistet ist, aber nur noch bei [TYPO3 GIT](https://git.typo3.org) heruntergeladen werden kann.

Da GIT allerdings ein eigenes Übertragungsprotokol mitbringt, können die Projekte als Windowsuser nicht mehr mit TortoiseSVN heruntergeladen werden, sondern müssen mit einem extra auf GIT zugeschnittenen GIT-Client heruntergeladen werden. Ich hab mal ein bissl gesucht und mSysGit gefunden. Dort gibt es auch eine portable Version, die Ihr einfach nur entpacken müsst. Ganz ohne Installation.

Nach dem Entpacken sucht Ihr bitte nach Eurem Extensionverzeichnis. Bei mir heißt es:

```
C:\xampp\htdocs\typo3\typo3conf\ext
```

Geht nun mit Start -> Ausführen in die Eingabeaufforderung und navigiert zu dem Verzeichnis in dem Ihr GIT entpackt habt. Im GIT-Verzeichnis gibt es einen Ordner `bin`. Hier findet Ihr die git.exe mit der Ihr GIT-Projekte herunterladen könnt. Dieses Tool hat keine Benutzeroberfläche und kann nur in der Eingabeaufforderung ausgeführt werden.

Im damaligen SVN-Verzeichnis vom `extbase_kickstarter` gibt es sogar einen Befehl, wie man die Extension auschecken kann. Bitte passt den hinteren Pfad an Euer System an:

```shell
git clone git://git.typo3.org/TYPO3v4/Extensions/extbase_kickstarter.git C:\xampp\htdocs\typo3\typo3conf\ext\extbase_kickstarter
```

Der `extbase_kickstarter` befindet sich nun in eurem Extensionverzeichnis und kann somit mithilfe des Extensionmanagers im TYPO3-Backend installiert werden. Bitte installiert entweder vorher die beiden Extensions `extbase` und `fluid` oder ihr werdet bei der `extbase_kickstarter` Installation darauf hingewiesen, dass diese beiden Extensions noch zu installieren sind.

Nach einem Reload des TYPO3-Backends sollte der `extbase_kickstarter` links im Hauptmenü erscheinen.

### UPDATE: extension_builder

Ich habe heute (28.06.2011) festgestellt, dass die Extension umbenannt wurde in `extbase_builder`. Demzufolge muss der Befehl nun folgendermaßen lauten:

```shell
git clone git://git.typo3.org/TYPO3v4/Extensions/extension_builder.git C:\xampp\htdocs\typo3\typo3conf\ext\extension_builder
```
