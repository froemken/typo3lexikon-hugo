+++
title = "Du braucht Daten? Dann nimm getText!"
linkTitle = "gettext"
date = 2024-01-09T20:58:10+01:00
aliases = ["gettext.html"]
+++

getText ist wohl die mächtigste Funktion, um mal eben von wo auch immer ein paar Daten herzuzaubern. Im Content-Objekt selbst heißt die Funktion getData. Schaut selbst welche Möglichkeiten angeboten werden.

Die Eigenschaft getText besteht aus mehreren Parametern. Der erste Parameter wird mit einem Doppelpunkt von allen anderen Parametern getrennt. Alle weiteren Parameter werden mit Hilfe der | Pipe unterteilt.

## GP

```typo3_typoscript
page.10 = TEXT
page.10.data = gp:id
```

Dieses Beispiel gibt die aktuelle Seiten-ID wieder.

```typo3_typoscript
page.10 = TEXT
page.10.data = gp:tx_meineextension_pi1|artikel
```

Dieses Beispiel greift auf die GET-Variable tx_meineextension_pi1[artikel] zu, die von der Extension "Meine Extension" erzeugt wurde. Es wird die ID des aktuellen Artikels zurückgegeben.
GP bedeutet so viel wie Get und Post. Damit könnt Ihr also auf die Variablen zugreifen, die von Seite zu Seite per URL (GET) oder per Formular (POST) übertragen werden.

## GPvar

Deprecated. Bitte GP verwenden.

## TSFE

```typo3_typoscript
10 = TEXT
10.data = TSFE:TYPO3_CONF_VARS|GFX|gdlib_2
```

Dieses Beispiel greift auf die Settings im Installtool zu und gibt zurück, ob die GDlib Version 2 für diese TYPO3-Installation aktiviert wurde.

```typo3_typoscript
10 = TEXT
10.data = TSFE:fe_user|user|username
```

Dieses Beispiel greift auf das Frontend-Benutzer-Array zu und gibt den Benutzernamen des aktuell angemeldeten Users zurück.
TSFE ist das größte Array in TYPO3 und füllt als Ausdruck bei einer Schriftgröße von 6 über 20 Din A4-Seiten. In diesem Array sind sämtliche Settings, Scripte und Objekte definiert, die in TYPO3 verfügbar sind. Schaut selbst, was so im TSFE-Array zu finden ist.

## GETENV

```typo3_typoscript
10 = TEXT
10.data = GETENV:SERVER_NAME
```

Dieses Beispiel greift auf Variablen der Serverumgebung zu. So wird hier der Servername zurückgegeben. Auf Serverseite wird die PHP-Funktion getenv ausgeführt.

## GETINDPENV

```typo3_typoscript
10 = TEXT
10.data = GETINDPENV:SCRIPT_FILENAME
```

Hat die gleiche Funktion wie GETENV. Mit dem Unterschied, dass die zurückgegebenen Daten aus GETINDPENV seitens TYPO3 nochmals aufbereitet werden. Während ein GETENV:SCRIPT_FILENAME C:\\xampp\\htdocs\ zurückgibt, gibt ein GETINDPENV:SCRIPT_FILENAME ein sauberes C:/xampp/htdocs/ zurück.

## FIELD

```typo3_typoscript
10 = TEXT
10.data = FIELD:title
```

Dieses Beispiel klappt nur, wenn es im Haupttemplate eingefügt wurde. Wenn Ihr dieses Beispiel in die Textfelder von einer Extension wie sfjquery oder ts_cobj eintragt, dann klappt das Beispiel nicht. Woran liegt das?

10.data greift immer auf den aktuellen Datensatz zu. Dieser ist im Maintemplate das Page-Objekt (Tabelle: pages). Diese enthält auch ein Feld "title".

Aber dieses Beispiel in einer Extension kann immer nur auf das aktuelle Content-Objekt zugreifen (Tabelle: tt_content) und in dieser Tabelle gibt es keine Spalte mit dem Namen "title". Dort heißt die Überschrift "header":

```typo3_typoscript
10 = TEXT
10.data = FIELD:header
```

## PARAMETERS

```typo3_typoscript
10 = TEXT
10.data = PARAMETERS:trallala
```

Die Parameter werden nur im Bereich von Benutzerfunctionen gefüllt. Alle Parameter die einer Funktion übergeben werden, werden in dieses Array geschrieben und können dann mit PARAMETERS in der getText-Eigenschaft wieder aufgerufen werden. Schaut Euch dazu die Funktion _parseFunc in der Datei class.tslib_content.php an.

## REGISTER

```typo3_typoscript
10 = LOAD_REGISTER
10.variable = Hallo zusammen
20 = TEXT
20.data = REGISTER:variable
30 = TEXT
30.data = REGISTER:tx_templavoila_pi1.parentRec.uid
```

Das Register ist eine Art Registrierungsbereich für globale Variablen. Grade TemplaVoila nutzt dieses Register, um sämtliche Eigenschaften des Elternelements an TV-Elemente wie FCEs und TV-Templates zur Verfügung zu stellen. Natürlich kann auch mit dem TS-Objekt LOAD_REGISTER eine globale Variable angelegt werden und wie im Beispiel wieder darauf zugegriffen werden.

## GLOBAL

```typo3_typoscript
10 = TEXT
10.data = GLOBAL:BE_USER|username
```

TSFE ist zwar das größte Array in TYPO3, aber wie der Name schon sagt, kann es nur auf Daten des Frontendes zugreifen. Um aber auf Daten des aktuell angemeldeten Backendusers zuzugreifen benötigen wir den Zugriff auf das Array BE_USER.

## LEVELTITLE

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELTITLE:1
```

Leveltitle klappt nicht im TS von Extensions. Hier müsst Ihr Euch im Template Eurer Webseite befinden, damit das klappt.

In diesem Beispiel erhaltet Ihr den Titel der Seite aus der 2ten Ebene.

## LEVELMEDIA

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELMEDIA:-1, slide
```

Levelmedia klappt nicht im TS von Extensions. Hier müsst Ihr Euch im Template Eurer Webseite befinden, damit das klappt.

In diesem Beispiel werden alle Seiten in der Rootline von rechts nach links durchlaufen bis auf einer Seite im Feld Media ein Eintrag hinterlegt wurde.

## LEVELUID

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELUID:3
```

Leveluid klappt nicht im TS von Extensions. Hier müsst Ihr Euch im Template Eurer Webseite befinden, damit das klappt.

In diesem Beispiel erhaltet Ihr die UID der Seite aus der 4ten Ebene.

## LEVELFIELD

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELFIELD:0,TSconfig
```

Levelfield klappt nicht im TS von Extensions. Hier müsst Ihr Euch im Template Eurer Webseite befinden, damit das klappt.

In diesem Beispiel erhaltet Ihr die TSconfig der Rootseite. Natürlich kann auch hier slide verwendet werden:

```typo3_typoscript
page.10 = TEXT
page.10.data = LEVELFIELD:0,TSconfig,slide
```

## FULLROOTLINE

```typo3_typoscript
page.50 = TEXT
page.50.data = FULLROOTLINE:-1,title
```

Fullrootline klappt nicht im TS von Extensions. Hier müsst Ihr Euch im Template Eurer Webseite befinden, damit das klappt.

In diesem Beispiel erhaltet Ihr den Titel der Elternseite und NICHT die aktuelle Seite wie sonst üblich. Wenn Ihr Fullrootline "richtig" verwenden wollt, dann muss Eure Webseite aus mehreren Templates bestehen. +ext-Templates reichen aus, aber in diesen Templates MUSS das Flag "is root page" bzw. "Wurzelebene" aktiviert sein.

## DATE

```typo3_typoscript
page.40 = TEXT
page.40.data = date:0
page.50 = TEXT
page.50.data = date:d.m.Y
```

Wenn date mit 0 angegeben wird, wird automatisch ein Datum im Format "d/m Y" (20/02 2010) ausgegeben. Im 2ten Beispiel wird ein Datum im Format d.m.Y (20.02.2010) ausgegeben. Es wird nicht auf die aktuelle Zeit zugegriffen, sondern auf die Zeit, als die index.php von TYPO3 geladen wurde. Heißt: Sobald die index.php von TYPO3 aufgerufen wird, wird die aktuelle Zeit in einer Variable abgespeichert EXEC_TIME. Bis dann endlich die Funktionen von getText verarbeitet werden, können ein paar Millisekunden vergangen sein.

## PAGE

```typo3_typoscript
page.10 = TEXT
page.10.data = page:uid
```

In diesem Beispiel greift Ihr auf den aktuellen Seiten-Datensatz zu und holt Euch die UID der aktuellen Seite. Ihr habt auf alle Felder der pages-Tabelle Zugriff.

## CURRENT

```typo3_typoscript
page.50 = TEXT
page.50 {
  value = blau,grün,weiß,lila
  split {
    token = ,
    cObjNum = 1
    1.10 = TEXT
    1.10.current = 1
    1.20 = TEXT
    1.20.data = current:trallala
    1.wrap = <b>|</b><br />
  }
}
```

In diesem Beispiel greifen wir auf die vom Elternelement zur Verfügung gestellten Daten zu. current = 1 und data = current:trallala haben also die gleiche Funktion. Dieses trallala oder irgendein anderer beliebiger Wert müssen angegeben werden, weil current sonst nicht ausgeführt werden kann. Auch das ist eine Unschönheit im TYPO3-Code. Denn dort werden die Funktionen nur dann ausgeführt, wenn der 2te Parameter einen Wert <> '' hat.

## LEVEL

```typo3_typoscript
page.40 = TEXT
page.40.data = level:bla
page.50 = TEXT
page.50.data = level:1
```

Beide Beispiele geben Euch die Ebene zurück in der sich die aktuelle Seite befindet. Bitte nicht vergessen, dass bei 0 angefangen wird zu zählen. Hinter dem Doppelpunkt muss ein beliebiger Wert stehen, deshalb noch das Beispiel mit dem "bla".

## DB

```typo3_typoscript
page.50 = TEXT
page.50.data = DB:tt_content:106:header
```

Dieses Beispiel greift auf die Tabelle tt_content zu und sucht nach der UID 106. Von diesem Datensatz wird der Wert aus der Spalte "header" zurückgegeben. Alle Werte werden mit einem Doppelpunkt getrennt. Nach dem DB MÜSSEN 3 Angaben erfolgen (Tabelle, UID, Spalte), ansonsten wird nichts zurückgegeben.

## LLL

```typo3_typoscript
page.50 = TEXT
page.50.data = lll:EXT:tt_news/locallang.xml:pi_plus_wiz_description
```

In diesem Beispiel greifen wir auf die Sprachdatei der tt_news-Extension zu und holen uns den Wert aus pi_plus_wiz_description. Die Ausgabe ist sprachenabhängig. Wir erhalten also eine deutsche Ausgabe bei config.language = de.

## PATH

```typo3_typoscript
page.50 = TEXT
page.50.data = path:EXT:tt_news/res/tt_news_v2_template.html
```

Mit path können wir aus dem 2ten Parameter einen relativ zur TYPO3-Installation liegenden Pfadnamen erzeugen. So wird aus:

EXT:tt_news/res/tt_news_v2_template.html

typo3conf/ext/tt_news/res/tt_news_v2_template.htm

## COBJ

```typo3_typoscript
10 = CONTENT
10 {
  table = pages
  select.pidInList = 5
  renderObj = COA
  renderObj {
    10 = TEXT
    10.field = title
    20 = TEXT
    20.data = COBJ:parentRecordNumber
  }
}
```

COBJ beinhaltet nur den Parameter parentRecordNumber. Dieser Parameter liefert nicht wie erwartet die UID des Elternobjektes zurück, sondern ist eher als Datensatzzähler zu verstehen. So kommt es auch, dass diese Funktion nur im Bereich CONTENT und RECORD Sinn macht.

In diesem Beispiel greife ich auf eine Seite mit mehreren Unterseiten zu (5) und hole mit alle Titel der Unterseiten. Für jede Unterseite lasse ich mir auch gleich den Datensatzzähler ausgeben. Das Ergebnis schau ungefähr so aus:

- Home1
- Produkte2
- Kontakt3
- Impressum4

## DEBUG

```typo3_typoscript
page.30 = TEXT
page.30.data = debug:rootline
page.30.wrap = |<br />
page.40 = TEXT
page.40.data = debug:fullRootLine
page.40.wrap = |<br />
page.50 = CONTENT
page.50.select.pidInList = 107
page.50.table = pages
page.50.renderObj = COA
page.50.renderObj {
  10 = TEXT
  10.field = title
  20 = TEXT
  20.data = debug:data
}
```

Debug kann drei Parameter interpretieren: rootline, fullRootLine und data. Data ist nicht immer gefüllt, deshalb hier auch gleich ein Beispiel, in dem data mit den Titeln der Unterseiten gefüllt wird. Aber nicht nur das. In diesem Beispiel enthält data für jede Seite ALLE Spalten der Tabelle pages.
