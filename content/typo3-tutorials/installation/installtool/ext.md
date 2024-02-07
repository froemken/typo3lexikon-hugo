+++
title = "EXT"
date = 2024-01-09T20:47:58+01:00
aliases = ["typo3-conf-vars-ext.html"]
+++

## $GLOBALS['TYPO3_CONF_VARS']['EXT']

### allowGlobalInstall Standard: deaktiviert

TYPO3 hat viele neue ExtensionManager durchlebt. Irgendwo zwischen TYPO3 4.5 und 4.6 ist diese Option ein bisschen untergegangen. Nach Aktivierung passiert nichts Visuelles. Es gibt keine Möglichkeit mehr mittels dem ExtensionManager eine Extension global in typo3/ext/ zu installieren. In Forge gibt es diverse Diskussionen diese Option komplett aus dem Core zu entfernen. Andere sagen, wer unbedingt Extensions global installieren will, der soll diese über die Shell in das typo3/ext/ Verzeichnis kopieren und installieren.

Durch Aktivierung dieser Option wird nur das Verzeichnis typo3/ext/ einer Liste hinzugefügt, um globale Extensions zumindest über den ExtensionManager wieder löschen zu können.

### allowLocalInstall Standard: aktiviert

Solange diese Option aktiviert ist, können Extensions in das vorgegebene Verzeichnis typo3conf/ext/ installiert werden. Nach Deaktivierung ist nicht mal mehr die installation einer Extension aus dem TER möglich. Selbst das Löschen von lokal installierten Extensions ist nicht mehr möglich. Extensions können nur noch über den Upload im ExtensionManager installiert werden.

### allowSystemInstall Standard: deaktiviert

Damals gab es im ExtensionManager noch Selectboxen mit denen entschieden werden konnte, ob eine Extension local, global oder sogar systemweit installiert werden dürfen. Diese Selectbox gibt es nicht mehr und selbst wenn Ihr allowSystemInstall aktiviert lassen sich Extensions aktuell nicht installieren. Aktuelle Fehlermeldung:

```
undefined not in allowed download paths
```

Extensions sollten nicht systemweit installierbar sein. Deshalb gibt es schon Stimmen, diese Option aus TYPO3 rauszuschmeißen. Von daher und wegen dem Fehler gehe ich nicht weiter auf diese Option ein. Lasst sie einfach deaktiviert.

```
excludeForPackaging (?:\..*(?!htaccess)|.*~|.*\.swp|.*\.bak|\.sass-cache|node_modules|bower_components)
```

Über den ExtensionManager könnt Ihr eine Extension auch als ZIP Datei herunterladen. Bevor die ZIP erstellt wird, werden alle Dateien, die diesem regulären Ausdruck entsprechen von der ZIP Erstellung ausgeschlossen.
