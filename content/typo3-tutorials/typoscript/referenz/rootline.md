+++
title = "Rotline"
date = 2024-01-09T20:58:10+01:00
aliases = ["rootline.html"]
+++

Die Rootline ist der direkte Weg um von der Root-Seite (die Seite mit dem Haupttemplate bzw. die erste Seite nach der Seite mit der Weltkugel) bis zur aktuellen Seite. Schaut Euch dazu auch das Bild hier oben rechts an.

Die Rootline in diesem Beispiel beinhaltet 5 Seiten und zwar genau in dieser Reihenfolge

1. Stefans TYPO3-Seite
2. TYPO3
3. tslib
4. content

## Rootline

Zu jeder dieser Seiten beinhaltet das Rootline-Array die "wichtigsten" Felder aus der Tabelle pages. Diese "wichtigsten" Felder werden hartcodiert im TYPO3-Quellcode vorgegeben und sind mit Stand von TYPO3 4.5.0 die Folgenden:

- pid
- uid
- t3ver_oid
- t3ver_wsid
- t3ver_state
- t3ver_swapmode
- title
- alias
- nav_title
- media
- layout
- hidden
- starttime
- endtime
- fe_group
- extendToSubpages
- doktype
- TSconfig
- storage_pid
- is_siteroot
- mount_pid
- mount_pid_ol
- fe_login_mode
- backend_layout_next_level

Für jede Seite in der Rootline wird ein Array mit diesen Feldern erstellt. So könnt Ihr z.B. sehr einfach auf den Titel oder das Mediafeld der Seite aus der dritten Ebene zugreifen. Da die Ebenen immer bei 0 anfangen, wäre in unserem Beispiel die Seite aus der dritten Ebene, die mit dem Titel "content".

Wie Ihr diese Liste erweitern könnt, erkläre ich Euch weiter unten im Berech LEVELFIELD.

Mit TypoScript können wir Dank der getText-Funktion auf übergeordnete Seiten zugreifen:

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELTITLE:1
```

Dieses Beispiel liest die Rootline aus, findet 5 Seiten und gibt uns den Titel der Seite "TYPO3" wieder. Nicht vergessen, dass hier mit dem Zählen bei 0 angefangen wird.

## Negative Angaben

Normalerweise durchsucht Leveltitle die Rootline immer von links (Rootseite) nach rechts (aktuelle Seite). Mit negativen Angaben können wir Leveltitle anweisen von rechts nach links zu suchen:

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELTITLE:-3
```

Dieses Beispiel gibt uns den Seitentitel "tslib" wieder. Wenn Ihr Euch nun fragt: "Warum tslib und nicht TYPO3? Ich dachte es wird bei 0 angefangen zu zählen", dann gebe ich Euch zwar Recht, aber bei negativen Seiten ist das halt anders und somit ist die -1 die aktuelle Seite mit dem Titel "Rootline" und die -3 die "tslib"-Seite.

Hier mal eine Auflistung der Seiten und Ihren Leveln:

- Stefans TYPO3-Seite (0|-5)
- TYPO3 (1|-4)
- tslib (2|-3)
- content (3|-2)
- Rootline (4|-1)

## Wollt Ihr es genauer wissen?

In der Datei class.tslib_content.php gibt es eine Funktion mit dem Namen "getKey":

```php
function getKey($key,$arr) {
    $key = intval($key);
    if (is_array($arr)) {
        if ($key < 0) {
            $key = count($arr)+$key;
        }
        if ($key < 0) {
            $key=0;
        }
    }
    return $key;
}
```

Hier nochmal unser Beispiel dazu:

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELTITLE:-3
```

$key ist unsere -3 und $arr ist das Array, das all unsere Seiten der Rootline beinhaltet. Durchnummeriert von 0-4. In unserem Falle ist $key kleiner als 0, also werden nun alle Seiten in der Rootline gezählt (5 Seiten) und $key wird dieser Anzahl hinzugefügt. Wir haben also folgende Rechnung:

5 + -3

Wer in Mathe aufgepasst hat, weiß, dass zwei unterschiedliche Operatoren immer - ergeben. 5-3 ist also 2. Jetzt schauen wir wieder in unseren Seitenbaum und stellen fest, dass die Seite aus der 2ten Ebene den Titel "tslib" trägt.

## Übergroße Leveltitle

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELTITLE:-15
```

Bei einem negativen Leveltitle werden übergroße Werte einfach auf 0 gesetzt. Ihr erhaltet also immer den Titel der Rootseite. Schaut Euch dazu auch die PHP-Funktion oben an. Bei einem übergroßen positiven Leveltitle erhaltet Ihr allerdings einen leeren Wert. Diese "Unschönheit" habe ich im TYPO3-Bugtracker gemeldet und habe folgende Antwort erhalten:

Bei positiven Werten wird von fixen Seiten ausgegangen. Soll heißen: LEVELTITEL:2 zieht sich IMMER den Titel der Seite aus der 2ten Ebene.

Bei negativen Werten sieht das allerdings etwas anders aus. Hier wird von variablen Seiten gesprochen. So zieht sich der Wert LEVELTITLE:-1 immer den Titel der aktuellen Seite. Diese Seite kann aber in Ebene 1,3 oder 7 sein.

## Rootline-Slider

Stellen wir uns mal vor Ihr habt 100 Seiten und 5 Kategorien. Alle Seiten innerhalb einer Kategorie sollen das gleiche Bannerbild erhalten. Ihr habt nun entweder die Möglichkeit, in den Seiteneigenschaften einer jeden Seite, das entsprechende Kategoriebild einzufügen, oder aber Ihr gebt nur in der Startseite der jeweiligen Kategorie ein Bild an und alle Unterseiten greifen auf dieses Bild zu. Hätte auch den Vorteil, wenn das Bild mal geändert werden muss, dass Ihr nicht zig Seiten mit dem neuen Bild versehen müsst, sondern nur die Startseite der Kategorie.

TYPO3 bietet dafür den Zusatz "slide" an:

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELMEDIA:-1, slide
```

In den oberen Beispielen haben wir gelernt, dass wir mit -1 immer auf die aktuelle Seite zugreifen. Das slide gibt nun an, dass alle Seiten von der aktuellen Seite bis hin zur Rootseite durchwühlt werden sollen, bis im Feld "media" einer Seite ein Eintrag hinterlegt ist. "Slide" würde natürlich auch mit LEVELTITLE oder LEVELUID funktionieren, macht aber wenig Sinn, da der Titel und die Uid grundsätzlich für jede Seite vorhanden sind.

Das Wort "slide" sollte mit einem Komma von dem Ebenenwert getrennt sein. In der Praxis sind derzeit auch folgende Konstrukte möglich:

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELMEDIA:-1slide
page.10 = TEXT
page.10.data = LEVELMEDIA:-1trallalaslideblabla
```

ABER: Das funktioniert nicht mehr bei den Werten LEVELFIELD und FULLROOTLINE. Denn dort werden die einzelnen Elemente an Hand des Kommas aufgesplittet.

Im Internet habe ich auch schon folgende Aufrufe gesehen:

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELMEDIA:-2, slide
```

Dieses Beispiel greift gar nicht erst auf die aktuelle Seite zu und durchsucht von der Elternseite bis hin zur Rootseite alle Mediafelder. Das kann gewünscht sein, aber ich kann nun in meiner aktuellen Seite soviele Bilder hinterlegen wie ich will, aber sie werden nicht angezeigt!

Auch dieses Beispiel will erklärt sein:

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELMEDIA:3, slide
```

Dieses Beispiel bedeutet, dass von Seiten in der vierten Ebene bis zur Rootseite nach Einträgen im Feld media durchkämmt werden sollen. Heißt aber auch, dass Einträge bei Seiten in der fünften und größeren Ebenen nicht mehr berücksichtigt werden.

Mit LEVELMEDIA könnt Ihr nur auf bestimmte Felder der Tabelle pages zugreifen (siehe Auflistung weiter oben). Damit Ihr aber auf z.B. subtitle und Felder, die Ihr evtl. mit Hilfe einer eigenen Extension der Tabelle pages hinzugefügt habt, drauf zugreifen könnt, müsst Ihr diese weiteren Felder im INSTALL_TOOL in der Eigenschaft [FE][addRootLineFields] kommasepariert hinterlegen.
