+++
title = "Backend Layouts"
date = 2024-01-09T20:48:06+01:00
aliases = ["backend-layouts.html"]
+++

Dieses Grid View hieß damals be_layout und funktionierte damals nur mit der 4.3er Version von TYPO3. Nach dem einige Projekte mit dieser Extension erstellt wurde, waren die Leute sehr begeistert und man entschied sich dieses Feature in den TYPO3 Core aufzunehmen.

## Was ist der Grid View?

In den damaligen TYPO3-Versionen konnten nur maximal 4 Spalten für den Inhalt verwendet werden. Es sei denn Ihr habt diese Funktionalität mit entsprechenden Extensions wie unter anderem TemplaVoila aufgebohrt.

Dieses Aufbohren ist nun nicht mehr erforderlich, da TYPO3 4.5.0 Dank dem Grid View beliebig viele Inhalts-"Spalten" zulässt. Spalten habe ich extra in Anführungszeichen geschrieben, weil die neuen Inhaltsbereiche nahezu frei positionierbar sind.

Ihr könnt nun die grobe Struktur Eurer Webseite im Grid View nachbauen, um Euren Redakteuren das Wiederfinden und die Pflege der Webseite deutlich zu erhöhen. Kurz: Die Inhalte sind nun dort zu finden, wo sie sich auch auf der Webseite befinden.

## Einen neuen Grid View anlegen

Grid Views können auf jeder Seite über das Listenmodul -> Neuer Datensatz -> Backend Layout erstellt werden. Neben dem Titel könnt Ihr auch ein Bild und eine Beschreibung hinterlegen. Am Wichtigsten aber jedoch ist der Bereich Konfiguration.

Da das Schreiben einer eigenen Konfiguration sehr fehleranfällig ist, haben Euch die TYPO3-Entwickler einen Konfigurationswizard als Icon rechts neben dem Konfigurationsfeld eingefügt. Ein Klick und Ihr erhaltet ein neues Fenster mit ein paar + und - Symbolen rechts und unten.

Drückt mal ein paar Male auf diese + Symbole und Ihr werdet merken, dass mit jedem Klick ein neues Feld bzw. Spalte/Zeile erscheint. Evtl. werdet Ihr schon bemerkt haben, dass sich auch das Symbol in der Mitte eines jeden Feldes etwas verändert hat. Dort erscheinen nun je nach Lage des Feldes hellgraue Pfeile nach rechts und unten, um dieses aktuelle Feld mit dem dadrunter oder daneben zu verbinden. Wer sich ein bisschen mit dem Tabellenaufbau in HTML auskennt, wird hier bestimmt die Befehle colspan und rowspan wieder erkennen.

Wie so ein Gitter aussehen kann seht Ihr hier im Bild

Bitte gebt jedem Bereich einen Namen und die Spaltennummer mit. Während Ihr den Spaltennamen frei wählen könnt wie z.B. "Header", "Navigation oben" oder "Inhaltsbereich" solltet Ihr die Spaltennummern aufsteigend von 0 an beginnend vergeben. Merkt Euch aber welches Feld welche Spaltennummer hat. Das ist wichtig für die spätere Einbindung der Inhaltselemente in das Template.

Hintergrund-Info: Die Spaltennummer spiegelt das Feld colpos der Tabelle tt_content wieder.

## Einbinden des Grid Views

Nachdem Ihr das Grid View oben links abgespeichert habt, könnt Ihr in den Seiteneigenschaften einer jeden Seite auf dem Tab "Erscheinungsbild" Euer neu erstelltes Grid View aus der Selectbox auswählen. Entscheidet selbst, ob Ihr Euer Grid View nur für die aktuelle Seite oder nur für alle Unterseiten zur Verfügung stellen wollt.

Nach dem Speichern der Seiteneigenschaften könnt Ihr nun Inhalt in jedes Feld einbinden, wie Ihr hier im Screenshot sehen könnt.

## Der Weg vom Grid View auf die Homepage

Wer sich schonmal die Extension css_styled_content näher angeschaut hat, hat bestimmt auch diese Zeilen schon entdeckt:

```typo3_typoscript
# get content
styles.content.get = CONTENT
styles.content.get {
  table = tt_content
  select.orderBy = sorting
  select.where = colPos=0
  select.languageField = sys_language_uid
}

# get content, left
styles.content.getLeft < styles.content.get
styles.content.getLeft.select.where = colPos=1

# get content, right
styles.content.getRight < styles.content.get
styles.content.getRight.select.where = colPos=2

# get content, margin
styles.content.getBorder < styles.content.get
styles.content.getBorder.select.where = colPos=3
```

Mit diesen Zeilen werden die bekannten vier Inhaltsspalten ausgelesen. Hier erkennt Ihr nun auch warum Ihr Euch die Spaltennummern merken solltet (colpos=x).

Natürlich können wir bei unserem kleinen Beispiel mit styles.content.getBorder auf unser letztes Feld (Inhaltsbereich) zugreifen, weil getBorder mit dem Inhalt aus colpos 3 gefüllt wird. Ich denke wir sollten uns lieber neue styles.content-Elemente erzeugen:

```typo3_typoscript
# hole Logo
styles.content.logo < styles.content.get
# hole header
styles.content.header < styles.content.get
styles.content.header.select.where = colPos=1
# hole navi
styles.content.navi < styles.content.get
styles.content.navi.select.where = colPos=2
# hole inhalt
styles.content.inhalt < styles.content.get
styles.content.inhalt.select.where = colPos=3
```

Jetzt können wir wieder wie vorher auch unsere Marker und Subparts im Template befüllen:

```typo3_typoscript
page = PAGE
page.10 = TEMPLATE
page.10 {
  template = FILE
  template.file = fileadmin/templates/template.html
  marks {
    LOGO < styles.content.logo
    NAVI < styles.content.navi
    HEADER < styles.content.header
    CONTENT < styles.content.inhalt
  }
}
```

## Flossen weg von LOGO und NAVI

Damit wir unsere Struktur beibehalten können, auch wenn nicht jedes Feld ein Inhaltselement bekommen soll, lassen sich, wie früher auch, nur die gewünschten Felder mit Hilfe von pageTSconfig aktivieren:

```typo3_typoscript
mod.SHARED.colPos_list = 1,3
```

Die anderen Felder werden nun nicht entfernt, wie die Spalten früher, sondern sie werden ausgegraut dargestellt. Sie erhalten dann Überschriften wie "Nicht zugewiesen" oder "Keine Bearbeitungsberechtigung".

## NAVI und LOGO per TS einbinden

Das Logo und die Navigation werden normalerweise nicht auf jeder Seite erneut eingetragen. Deshalb hier das TS, wie es bei Euch aussehen könnte:

```typo3_typoscript
styles.content.header < styles.content.get
styles.content.header.select.where = colPos=1
styles.content.content < styles.content.get
styles.content.content.select.where = colPos=3

page = PAGE
page.10 = TEMPLATE
page.10 {
  template = FILE
  template.file = fileadmin/templates/template.html
  marks {
    LOGO = IMAGE
    LOGO.file = Pfad zum Bild
    NAVI = HMENU
    ...    

    HEADER < styles.content.header    

    CONTENT < styles.content.content
  }
}
```

Natürlich lässt sich an diese Stelle auch automaketemplate verwenden.
