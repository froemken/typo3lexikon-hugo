+++
title = "rtehtmlarea"
date = 2024-01-09T20:31:21+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["rtehtmlarea.html"]
+++

Der RichText-Editor (RTE) auch bekannt unter dem Extensionkey "rtehtmlarea" ist gleichzeitig der absolute Hammer und ein Krampf. Die Konfiguration besteht nicht nur aus 1-2 Zeilen Quellcode und ein paar Klick-Klacks in irgendwelchen Einstellungen. Nein! Ich will Euch hier bewusst keine Hoffnung auf easy easy machen. Allein die Dokumentation erstreckt sich über 4 Dokumentationen.

Dokumentation zu rtehtmlarea

Dokumentation zur RTE API (Transformation)

Dokumentation zu HTMLparser

Dokumentation zu HTMLparser_tags

Wenn Ihr die alle durchgelesen und vor allem auch verstanden habt, dann seit Ihr die absoluten Kings des RTE.

Aller Anfang ist schwer

Auf dieser Seite will ich einen RTE erzeugen, der nur den p-Tag, b-Tag und den i-Tag erlaubt. Klassennamen sind verboten und auch Überschriften dürfen nicht gespeichert werden. Dieses Beispiel ist vielleicht nicht sehr produktiv, aber es soll Euch mal zeigen, welche Schritt alle zu machen sind, um das überhaupt zu realisieren.

Zu allererst ein bissl Theorie: Klickt mal in der linken Navigation auf Info, dann auf eine beliebige Seite und wählt dann oben im ersten DropDown-Menü "Seiten-TSconfig" aus. In dem zweiten DropDown wählt Ihr dann den Menüpunkt "RTE." aus. Der Punkt hinter RTE soll Euch zeigen, dass RTE ein Objekt ist und mit den enthaltenen Eigenschaften konfiguriert werden kann.

Ihr seht nun eine noch nichts sagende Liste an Werten. Diese Eigenschaften wurden nach der installation der rtehtmlarea automatisch gesetzt und erlauben Euch den RTE zu ca. 70 % zu nutzen. Richtig! Ihr arbeitet mit einer abgespeckten Version.

Wichtig zu wissen ist: In vielen anderen Dokumentationen baut Ihr auf diese vorgegebenen Defaultwerte auf. Heißt: Wenn wir später einen weiteren Button dem RTE hinzufügen, dann heißt das noch lange nicht, dass er auch funktioniert, denn so eine Auflistung (<ul>) muss auch erstmal in die Liste der erlaubten Tags aufgenommen werden. Sehr oft mischen sich die vorgegebenen Einstellungen des RTEs mit den Einstellungen aus den anderen Dokumentationen und der RTE oder die gewünschte Funktion klappt einfach nicht. Dieser Grund ist soooo wichtig, dass ich hier die vorgegebenen Werte immer komplett lösche und den RTE von Anfang an neu aufbaue. Denn nur so lernt Ihr den RTE auch wirklich kennen. Wer nicht viel programmieren will, kann im Extensionmanager aus 3 vorgegebenen Konfigurationen wählen:

- Minimal
- Typical (Default)
- Demo

Wählt Ihr hier "Demo" aus, dann zeigt sich Euch der RTE in seiner vollen Pracht.

## Den RTE konfigurieren

Im Folgenden wollen wir den RTE konfigurieren. Dazu gehen wir in die Seiteneigenschaften der Startseite. Alle Einstellungen werden von dort auf die Unterseiten vererbt und können nach Bedarf auf einer Unterseite wieder überschrieben werden. Auf dem Reiter "Ressourcen" befindet sich das Feld "TypoScript-Konfiguration". Wie bereits erwähnt leeren wir zunächst den RTE:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >
```

Nachdem der Cache gelöscht wurde, seht Ihr den RTE jetzt mal so richtig nackig.

## Buttons hinzufügen

Wir fügen dem RTE nun die drei Schaltflächen bold (fett), italic (kursiv) und "change mode" hinzu:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  showButtons = bold, italic, chMode
}
```

Ihr erhaltet nun einen RTE wie hier rechts abgebildet. Ich persönlich empfehle jedem Programmierer / Entwickler auch den Button "chMode" einzubinden. Damit könnt Ihr zwischen dem geschriebenen Text und dem vom RTE erstellten HTML-Text hin und her wechseln. Diese Schaltfläche ist gerade bei der Problemsuche sehr hilfreich.

## Das erste Problem

Obwohl wir mit dem Script von oben gerade noch am Anfang stehen, so haben wir trotzdem schon das erste Problem. Denn durch Copy und Paste von anderen Webseiten in den RTE werden alle CSS-Klassen, Farben, Styles und HTML übernommen und auch in der Datenbank gespeichert.

Lasst uns das mal mit der Schaltfläche chMode testen. Klickt auf diese Schaltfläche, tragt Folgendes in den RTE ein und speichert das Inhaltselement.

```html
<h1>Hallo zusammen</h1>
```

Im Frontend sehen wir nun eine große Überschrift.

Heißt: Obwohl wir NUR bold und italic aktiviert haben, so lässt sich der RTE austricksen. Das müssen wir im nächsten Schritt unterbinden.

## Nur erlauben, was ich will

Wir wollen in diesem Schritt nur den <p>-Tag erlauben und damit das vorhin geschriebene <h1> beim Speichern automatisiert löschen lassen. Leider lassen sich solche Automatismen nicht mehr mit den reinen RTE-Einstellungen realisieren. TYPO3 bringt jedoch eine API mit, mit der Ihr umfangreiche Änderungen an generiertem HTML-Quellcode durchführen könnt. Diese PHP-Klasse befindet sich in TYPO3\CMS\Core\Html\HtmlParser.php und kann über die proc-Eigenschaft des RTE angesprochen werden.

Die Eigenschaft "proc" besitzt selbst viele weitere Eigenschaften. Die vielversprechendste lautet wohl "allowTags". Allerdings werden die Tags, die hier angegeben werden, einer Reihe bestehender Tags hinzugefügt. Folgende Tags sind von Grund auf in allowTags (hardcoded) schon aktiviert:

```typo3_typoscript
b,i,u,a,img,br,div,center,pre,font,hr,sub,sup,p,strong,em,li,ul,ol,blockquote,strike,span
```

Ein einfaches "allowTags=p" überschreibt diese Voreinstellung NICHT und da das p-Tag schon vorhanden ist, würde mit diesem Befehl auch nichts passieren. Aber es gibt eine weitere Eigenschaft namens "denyTags". Mit dieser Eigenschaft könnt Ihr angeben, welche Tags aus allowTags entfernt werden sollen.

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  showButtons = bold, italic, chMode
  proc {
    denyTags = u,a,img,br,div,center,pre,font,hr,sub,sup,strong,em,li,ul,ol,blockquote,strike,span
  }
}
```

Super: Jetzt haben wir schon einiges verboten und selbst wenn wir etwas mit Hilfe von chMode unterstreichen <u>Hallo</u>, dann wird diese Formatierung nach dem Speichervorgang wieder entfernt. ABER: Der <H1>-Tag ist immer noch da! Das ist korrekt, wenn man weiß, was mit dem Inhalt auf dem Weg zur Datenbank passiert. Kurz: Die Transformation css_transform kümmert sich darum, dass Überschriften unberührt in die Datenbanken wandern. Um nun auch dieses h1 zu entfernen, benötigen wir die Eigenschaft entryHTMLparser, die den Inhalt des RTE noch genauer unter die Lupe nehmen kann.

## Endlich: Das Ziel

Im folgenden Script finden wir nun endlich ein Script, dass wirklich NUR bold und italic unterstützt und nichts Anderes AUCH wenn man von Word oder anderen Programmen Code in diesen RTE hineinkopiert:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  showButtons = bold, italic, chMode
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext
    denyTags = u,a,img,br,div,center,pre,font,hr,sub,sup,strong,em,li,ul,ol,blockquote,strike,span
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i
    }
  }
}
```

Erklärung:

Wenn wir, wie in unserem Fall einen HTMLparser verwenden müssen, dann müssen wir diesen zuvor mit entryHTMLparser_db = 1 aktivieren. Dies gilt für die beiden HTMLparser VOR und NACH der Transformation. Für die beiden HTMLparser, die während der Transformation ausgeführt werden (HTMLparser_db, HTMLparser_rte) gilt dies nicht.

Das allowTags von proc kann NICHT mit dem allowTags vom HTMLparser-Objekt verglichen werden! Denn während im proc-Objekt, die angegebenen Tags den bereits vorhandenen allowTags hinzugefügt werden, werden die Tags bei der Eigenschaft allowTags des HTMLparsers komplett überschrieben. Deshalb müssen wir hier das p-Tag für den normalen Text, das b-Tag für fett und das i-Tag für kursiv nochmals angeben.

Ich habe hier noch die Eigenschaft allowedClasses angegeben. Grundsätzlich sind ALLE Classes erlaubt. Wir könnten also auch

```html
<p class="test">Hallo zusammen</p>
```

in den RTE schreiben und er würde es anstandslos speichern. Um fehlerhafte Usereingaben zu unterbinden, müssen wir mindestens eine Klasse hier angeben. Eine Einstellung wie "allowedClasses >" oder "allowedClasses =" haben keine Auswirkung. Da jeder reinen Textzeile die Klasse bodytext hinzugefügt wird, habe ich mich in unserem Beispiel an bodytext angelehnt. Die Klasse "test" von unserem oberen Beispiel wird also nun komplett mit dem Attribut "class" entfernt.

## Weiter führende Links

Nachdem wir nun ein erstes Gefühl für den RTE bekommen haben, werden wir uns nun um das ganze DrumRum des RTE kümmern. Lest Euch die folgenden Links zumindest mal durch, damit Ihr den Problematiken, die noch auf Euch zukommen werden aus dem Weg gehen könnt.

