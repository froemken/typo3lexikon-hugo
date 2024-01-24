+++
title = "Indizes"
linkTitle = "MySQL Indizes"
date = 2024-01-09T21:01:17+01:00
slug = "indizes"
aliases = ["indizes.html"]
+++

Während viele Webseiten die MySQL-Option `key_buffer` für das Wichtigste halten, halte ich die Erstellung und die richtige Nutzung des Tabellenindizes für am Wichtigsten.

Bei Tabellen ohne Indizes muss MySQL wie bei einem Buch vorne anfangen und die komplette Tabelle "durchlesen", bis das gewünschte Wort/Datensatz gefunden wurde. Um das zu umgehen, gibt es Indizes. Diese lesen z.B. alle Werte einer Tabellenspalte ein, sortieren diese und erstellen pro Eintrag eine Art Link zu dem Originaldatensatz in der Tabelle. Stellt Euch eine Liste vor, die mit jedem neu hinzugezogenem Bewohner einer Stadt einfach um eine weitere Zeile erweitert wird und zwar an das Ende dieser Liste. So schaut auch unsere Datenbank aus. Ein komplettes durcheinander. Jetzt sucht mal den Herrn Frömken auf dieser Bewohnerliste. Bei gut 10.000 Bewohnern bleibt Euch wohl nichts anderes übrig, als jede Zeile zu lesen. Aber zum Glück gibt es das Telefonbuch. Hier werden alle Nachnamen alphabetisch aufgelistet und wir fangen bestimmt nicht vorne an zu suchen. Wir wissen Frömken fängt mit F an und wir wissen, dass das F in der ersten Hälfte des Telefonbuches zu finden ist und mit ein bisschen Glück finden wir den Herrn Frömken sogar im ersten Viertel und genau so ein Telefonbuch baut sich auch der MySQL-Server auf. Nur steht hier nicht der Vorname und die Adresse neben dem Nachnamen, sondern eine ID bzw. eine Positionsangabe, wo sich der dazugehörige Datensatz in dem Wust von Daten in der Tabelle befindet.

## Zugriff auf den Index

Das Erstellen eines Indizes ist das Eine, aber das Verständnis dafür aufbringen wie man seine SQL-Queries an die Indizes oder die Indizes an die SQL-Queries anpasst ist die andere Geschichte. Folgendes Beispiel mit der Tabelle pages:

```mysql
SHOW INDEXES FROM pages;
```

Wer mit der Ansicht nicht klarkommt, dem empfehle ich ein Tool wie t3adminer oder phpMyAdmin. Hier kann man erkennen, dass die TYPO3-Entwickler bereits 4 Indizes für die Tabelle pages erstellt haben: PRIMARY, t3ver_oid, alias und parent.

Lassen wir uns mal MySQL-seitig erklären, was es mit folgenden SQL-Queries auf sich hat:

```mysql
EXPLAIN SELECT * FROM pages;
```

Im Bereich Indizes sind für uns in erster Linie die Spalten possible_keys, key und Extra wichtig. In diesem Beispiel sehen wir, dass genau diese drei Spalten entweder NULL oder überhaupt keinen Eintrag enthalten. Wir können also darauf schließen, dass kein Index verwendet werden konnte. Gehen wir mal einen Schritt weiter:

```mysql
EXPLAIN SELECT * FROM pages WHERE title="Startseite";
```

Wir sehen, dass `possible_keys` und `key` immer noch den Wert NULL haben. MySQL geht in diesem Fall also her und fängt wie in unserer Bewohlerliste oben an zu lesen und geht nun jeden Datensatz durch, um alle titel mit dem Wort "Startseite" zu finden. Im Feld Extra finden wir nur den Hinweis, dass unsere Abfrage einen WHERE-Teil beinhaltet. Über Geschwindigkeit sagt das nichts aus. Ähnliche Abfrage andere Spalte:

```mysql
EXPLAIN SELECT * FROM pages WHERE alias="Impressum";
```

Diesmal enthalten die Spalten possible_keys und key den Wert alias. MySQL hat also für unsere Abfrage das dazu passende "Telefonbuch" gefunden und kann somit die Daten schneller finden. Jetzt machen wir mal highspeed:

```mysql
EXPLAIN SELECT alias FROM pages WHERE alias="Impressum";
```

Obwohl sich diese Abfrage nicht groß von ihren Vorgängern unterscheidet, hat diese jedoch einen enormen Geschwindigkeitsschub erhalten. In Spalte `Extra` finden wir dieses Mal noch den zusätzlichen Hinweis auf "Using index". Das bedeutet, das MySQL auch in diesem Fall in seinem "Telefonbuch" nach "Impressum" sucht, aber dieses Mal hat MySQL anhand unserer Query festgestellt, dass wir nur den Wert aus der Spalte "alias" haben wollen. Dieser Wert steht aber schon im Index selbst drin. Warum also sollte MySQL nun noch den Positionseintrag aus dem Indes auslesen und die Spalte `alias` aus der Datenbanktabelle holen, wenn der vollständige Wert doch im Index selbst drin steht?

Jeder Zugriff auf die Festplatte kostet Zeit, weil die Festplatte das langsamste Glied (Flaschenhals) in einem PC-System ist. Der Arbeitsspeicher hingegen ist mehrere 1000x schneller und wir sollten uns immer bemühen unsere SQL-Abfragen in Verbindung mit Indizes zu erstellen.

## ORDER BY und die Festplatte

Nehmen wir an wir haben ein paar 1000 Datensätze in unserer Datenbank und wollen diese nun sortieren:

```mysql
EXPLAIN SELECT * FROM pages ORDER BY title;
```

Wir sehen, dass kein Index gefunden wurde, der dem MySQL-Server beim Sortieren helfen könnte und wir sehen das Wort `filesort` in der Spalte Extra. Filesort ist so das Schlimmste was passieren kann. Denn in diesem Fall werden die Daten auf das Dateisystem des Betriebssystems ausgelagert und dort sortiert. Wie schon erwähnt ist die Festplatte das langsamste Glied und somit kosten solche Operationen richtig Zeit. Also sollte man zusehen, wie man filesort vermeiden kann.

```mysql
EXPLAIN SELECT * FROM pages ORDER BY alias;
```

Ich muss zugeben hier war ich auch baff. Obwohl es einen Index für die Spalte alias gibt, wird dieser aber nicht verwendet. Mir fallen auf Anhieb nur zwei Möglichkeiten ein: Entweder liegt es daran, dass der Index alias nicht eindeutig ist. Soll heißen, dass in dieser Spalte ein und derselbe Wert auch mehrfach vorkommen dürfen. Andererseits könnte es schlicht und einfach daran liegen, dass diese Typ Index einen WHERE-Teil in der Abfrage erwartet. Momentan tendiere ich zu Möglichkeit 1, denn siehe folgende Abfrage:

```mysql
EXPLAIN SELECT * FROM pages ORDER BY uid;
```

Wir sehen, dass der filesort verschwunden ist und das MySQL für die Sortierung den PRIMARY Index verwendet hat. OK, fügen wir mal ein WHERE hinzu:

```mysql
EXPLAIN SELECT * FROM pages WHERE alias="Impressum" ORDER BY alias;
```

Na Gott-Sei-Dank mit dem zusätzlichen WHERE klappt dann auch diese Sortierung ohne filesort und wie immer ist diese Abfrage dann wieder das non plus Ultra:

```mysql
EXPLAIN SELECT alias FROM pages WHERE alias="Impressum" ORDER BY alias;
```

Genau die gleichen Regeln gelten auch für GROUP BY.

## GROUP BY und mehrspaltige Indizes

Erstmal wieder ein Beispiel:

```mysql
EXPLAIN SELECT * FROM pages GROUP BY title;
```

Wir sehen, es wurde kein Index gefunden und es wird auch keiner verwendet. Allerdings sehen wir in der Spalte `Extra` einen noch unbekannten Wert namens `Using temporary`. Das ist die Katastrophe hoch drei. Nicht das nur die Daten auf Dateiebene vorsortiert werden müssen, nein es muss auch noch eine zusätzliche Datenbanktabelle erstellt werden, die unsere gruppierten Daten temporär aufnehmen kann. All das kostet Zeit. Viel Zeit! Schauen wir uns eine Abfrage an, in der wir das GROUP BY auf einem indizierten Feld anwenden:

```mysql
EXPLAIN SELECT * FROM pages GROUP BY alias;
```

Wunderbar: Der Index hat uns gerettet. Sowohl das erstellen der temporären Tabelle und das filesort sind verschwunden und es wird wieder unser Index wie in Spalte key angegeben verwendet.

Ganz zu Anfang haben wir uns auflisten lassen, dass es insgesamt 4 Indizes für die Tabelle pages gibt. 2 davon erstrecken sich über mehrere Spalten. Im Hinblick auf unser Telefonbuch hat das den Vorteil, dass die Einträge nicht nur nach dem Nachnamen sortiert sind, sondern innerhalb der Nachnamen auch nach den Vornamen und innerhalb der Vornamen dann schlussendlich nach der Strasse. Diese gerade aufgezählte Reihenfolge (Nachname, Vorname, Strasse) ist für unsere SQL-Abfrage extremst wichtig. Zurück in unserer Tabelle pages gibt es den Index parent. Dieser Index besteht aus den Spalten pid, sorting, deleted und hidden. Folgende Abfragen dazu:

```mysql
EXPLAIN SELECT * FROM pages GROUP BY pid;
```

Wunderbar: Kein filesort und der Index parent wird verwendet. Fügen wir mal eine Spalte hinzu:

```mysql
EXPLAIN SELECT * FROM pages GROUP BY pid, sorting;
```

Wunderbar: Kein filesort und der Index parent wird immernoch verwendet. Ändern wir mal die Spalte sorting in deleted:

```mysql
EXPLAIN SELECT * FROM pages GROUP BY pid, deleted;
```

Tja. Da ist wieder unser filesort und die temporäre Tabelle. Deshalb habe ich gerade gesagt, dass die Reihenfolge, wie die Spalten im Index definiert sind extremst wichtig sind. Diese Reihenfolge muss eingehalten werden. Außnahmen bestätigen die Regel:

```mysql
EXPLAIN SELECT * FROM pages WHERE sorting=64 GROUP BY pid, deleted;
```

Ihr könnt Spalten aus der Reihenfolge entfernen (in diesem Fall: sorting), aber nur wenn diese in einem WHERE-Teil vorkommen. Die Position innerhalb des WHERE-Teils ist dann bei mehreren Spalten uninteressant:

```mysql
EXPLAIN SELECT * FROM pages WHERE deleted=0 AND sorting=64 GROUP BY pid, hidden;
```

Auch hier gilt wieder highspeed, wenn ihr nur Felder aus dem Index benennt:

```mysql
EXPLAIN SELECT deleted, hidden, sorting FROM pages WHERE deleted=0 AND sorting=64 GROUP BY pid, hidden;
```
