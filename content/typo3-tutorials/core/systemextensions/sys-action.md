+++
title = "sys_action"
date = 2024-01-09T20:15:03+01:00
aliases = ["sys-action.html"]
+++

Jeder der mal in der linken Menüleiste auf Benutzerwerkzeuge --> Aufgaben geklickt hat, wird außer den "Voreinstellungen für den Export" nichts vorfinden. Mit der TYPO3-eigenen System-Extension `sys_action` kann dieser Bereich, um eigene Aufgaben erweitert werden.

Diese neuen Aufgaben beziehen sich überwiegend auf Datenbankabfragen. Ihr könnt dort also Aufgaben hinterlegen, die es euch ermöglichen z.B. Backendbenutzer anzulegen oder eine komplette Tabelle aufzulisten wie im Listenmodul. Aber das Schmankerl dieser Aufgaben ist eine Sektion, die sich damit beschäftigt eigene Datenbankabfragen in Listform darstellen zu können. Darum geht es hier in diesem Tutorial.

Bevor die Aufgaben in den Benutzerwerkzeugen verwendet werden können, muss mithilfe des Extensionmanagers die Systemextension sys_action installiert werden. Danach könnt ihr auf der Weltkugelseite (PID 0) über das Listenmodul eigene Aufgaben anlegen (siehe Bild).

## Eine Aufgabe anlegen

Nach dem Klick zum Anlegen der neuen Aufgabe erhaltet ihr ein kleines Formular mit dem ihr derzeit unter "Typ" zwischen 5 Varianten wählen könnt:

## Create Backend user

Wie der Name schon sagt, könnt ihr hiermit einen neuen Backend user anlegen. Vergebt einen Titel für diese Aufgabe und wählt einen bereits angelegten Backenduser aus, den ihr als Vorlage für die neu zu erstellenden Backenduser verwenden wollt.

## SQL-Query

Das ist der Haupt-Punkt, um den es hier geht. Mit dieser unscheinbaren Variante könnt ihr eigene SQL-Querys erstellen. Ihr seht keinen Bereich für SQL-Befehle oder sonstiges? Völlig korrekt...ich auch nicht. Vergebt dieser Aufgabe erstmal einen Titel und merkt euch den.

Der Trick ist, der SQL-Befehl wird nicht hier eingegeben, sondern über den Link DB-Überprüfung erstellt:

DB-Überprüfung (Menü links) --> DropDown oben auf "Vollsuche" stellen --> 2te DropDown-Liste auf "Erweiterte Abfrage" umstellen --> und nun bei MAKE QUERY die gewünschte Tabelle auswählen.

Für mein Beispiel habe ich hier die Tabelle "Seite" ausgefählt und unter "select fields" noch die Felder "stichworte" und "Beschreibung" hinzugenommen. Unter "make query" könnt ihr nun eure Abfrage noch weiter einschränken. Für mein Beispiel habe ich hier nur die Seiten ausgewählt, die die Seite mit der PID = 1 als Elternseite besitzen (siehe Bild oben rechts). Ganz unten werden bei mir nun 8 Seiten angezeigt. Mein Query ist fertig.

Weiter oben auf der Seite findet ihr einen Abschnitt "LOAD / SAVE". "new" steht bereits in dem DropDown-Feld drin. Es ist vielleicht ein bisschen verwirrend, aber über diese DropDown-Box könnt ihr wählen, ob ihr eure aktuelle Query unter einem neuen Namen abspeichern wollt oder unter einem bereits vorhandenen Namen und siehe da: Ganz unten in der DropDown-Box findet ihr euren Titel eurer Aufgabe wieder. Wählt also eure Aufgabe aus und klickt auf "Save" und bestätigt die Überschreibenmeldung mit "OK".

Über Benutzerwerkzeuge --> Aufgaben --> klick auf `Actions` klappt sich nun eure Liste von Aufgaben auf, unter anderem auch die gerade erstellte Aufgabe. Nach Auswahl erhaltet ihr links nun die Ausgabe eurer Query und könnt jeden Eintrag auch noch direkt bearbeiten.

## Record List

Nicht so dynamisch wie der SQL-Query ist die "record list". Auch mit ihr könnt ihr euch bestimmte Datensätze einer Seite/Sysfolders anzeigen lassen. Im Gegensatz zur SQL-Query müsst ihr hier aber eine Seiten-ID wählen, die eure Datensätze beinhaltet.

Das Ergebnis ist ähnlich dem aus dem Listenmodul. Hier könnt ihr gegenüber der SQL-Query aber selbst die gewünschten Felder hinzufügen bzw. abwählen. Mit den Bleistiften über jeder Spalte lässen sich alle Datensätze gleichzeitig bearbeiten. Das kann manchmal sehr sinnvoll sein.

## Edit Records

Obwohl ich mit der "records list" schon alle Datensätze auch gleich bearbeiten kann, macht dieser Eintrag trotzdem Sinn. Den mit dieser Variante könnt ihr völlig Tabellen unabhängig beliebige Datensätze rauspicken, die die Backendbenutzer einer ganz bestimmten Gruppe editieren dürfen.

## New Record

Bei dieser Variante müsst ihr wie bei der "Record list" eine Seite bzw. Sysfolder auswählen und auch angeben welche Art von Datensätzen hinzugefügt werden dürfen.
