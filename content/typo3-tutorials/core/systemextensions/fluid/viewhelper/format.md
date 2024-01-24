+++
title = "Format"
date = 2024-01-09T20:27:32+01:00
aliases = ["format.html"]
+++

## f:format.crop

| Parameter | Erklärung |
|-----------|-----------|
| maxCharacters | Anzahl Zeichen nach denen abgeschnitten werden soll. |
| append | Welche Zeichenkette soll nach dem Abschneiden angefügt werden? Standard ist `...` |
| respectWordBoundaries | Standardmäßig aktiv und führt dazu einen Schnitt innerhalb eines Wortes zu verhindern, indem der Schnitt vor dem Wort geschieht. Mit `0` zwingt man den ViewHelper dazu innerhalb von Wörtern zu schneiden. |
| respectHtml | Standardmäßig aktiviert und führt dazu innerhalb von HTML-Tags an den korrekten Stellen zu schneiden. Mit `0` kann es passieren, dass auch ein HTML-Tag zerschnitten wird. |

### Beispiel

```html
<f:format.crop maxCharacters="14">Diesen Text werden wir jetzt zerschnibbeln</f:format.crop>
```

Normalerweise würde dieser Befehl das Wort `werden` auftrennen. Aber dadurch, dass `respectWordBoundaries` standardmäßig gesetzt ist erhalten wir diese Ausgabe:

```html
Diesen Text...
```

### Beispiel: Trennung im Wort

```html
<f:format.crop maxCharacters="14" respectWordBoundaries="FALSE">Diesen Text werden wir jetzt zerschnibbeln</f:format.crop>
```

ergibt

```html
Diesen Text we...
```

### Beispiel mit respectHtml = 0

```html
<f:format.crop maxCharacters="25" respectHtml="0"><p>Diesen Text <strong>werden</strong> wir jetzt zerschnibbeln</p></f:format.crop>
```

ergibt

```html
Diesen Text...
```

Wie oben schon erwähnt, liegt das daran, dass nun auch alle Buchstaben der HTML-Tags mitgezählt werden.

## f:format.currency

Mit diesem ViewHelper könnt ihr Zahlen als Währung darstellen lassen. Doch zwei wichtige Informationen:

Übergebt ihr an diesen ViewHelper einen leeren Text, dann benötigt die in diesem ViewHelper befindliche Methode `PHP:number_format()` knapp 23 Millisekunden. Liefert ihr dieser Methode stattdessen direkt ein `0.00` ist die Methode in 1-2 Millisekunden durch. Das kann gerade bei größeren Listen Performance-Problemen entgegenwirken.

{{% notice style="info" title="Wichtig" icon="exclamation" %}}
Ihr dürft diesem ViewHelper keine Zahlen mit einem Komma als Dezimaltrenner mitgeben. Schaut euch dazu die Beispiele unten an.
{{% /notice %}}

| Parameter | Erklärung |
|-----------|-----------|
| currencySign | Das Währungskennzeichen wie `$` oder `€`. Dieses Zeichen wird immer hinter der Währung angezeigt. |
| decimalSeperator | Welches Zeichen soll für die Trennung von Euro und Cent verwendet werden. Standard: `,` |
| thousandsSeperator | Welches Zeichen soll als Tausendertrennzeichen verwendet werden. Standard: `.` |

### Beispiel

```html
<f:format.currency currencySign="$" decimalSeparator="." thousandsSeparator=",">1122334455.66</f:format.currency>
```

ergibt: `1,122,334,455.66 $`

### Beispiel mit nicht float kompatiblen Zahlen

```html
<f:format.currency currencySign="$" decimalSeparator="." thousandsSeparator=",">1122334455,66</f:format.currency>
```

ergibt: `1,122,334,455.00 $`

## f:format.date

So schön wie das mit den ganzen `\DateTime` Objekten ist, so prüft die Werte bitte ganz genau. Besucht Online-Timestamp-Konverter oder gleicht die Werte mit denen in eurer Datenbank ab. So steht in der Doku z.B. Wenn ihr einen Timestamp in ein `\DateTime` Objekt konvertieren wollt, dann fügt einfach ein `@`-Zeichen vorne an. Ich hoffe, ich erzähle jetzt nichts Falsches, aber dieser Timestamp wird nach dem RFC2822 konvertiert und das ist Zeitzone 0. In Deutschland würden wir dem resultierenden `\DateTime` Objekt also immer hinterherhinken. Die `\DateTime` Objekt sollten besser nach ISO8601 konvertiert werden. Dann klappt's auch mit der richtigen Zeitzone. Ich für meinen Teil hab mir einen eigenen ViewHelper geschrieben, der sich daran hält und auch Extbase arbeitet intern mit diesem ISO-Format. Siehe dazu auch die Methode in Extbase:

```html
DataMapper->mapDateTime()
```

Zitat: return new DateTime(date('c', $timestamp));

Warum das in den ViewHelper noch nicht eingeflossen ist, kann ich mir nicht erklären.

| Parameter | Erklärung |
|-----------|-----------|
| date | Entweder ein Objekt vom Typ `\DateTime` oder ein Text, der in ein `\DateTime` Objekt konvertiert werden kann. Z.B. `17.01.1979` geht. `17.01.79` geht nicht. |
| format | In welchem Format soll das Datum ausgegeben werden? Standard: Standard: `Y-m-d` |

### Beispiel

```html
<f:format.date date="17.01.1979" format="d/m/y"/>
```

### Beispiel Timestamp

```html
<f:format.date format="d.m.Y">@1334439765</f:format.date>
```

Wie schon gesagt: Je nach Land/Kontinent bitte mit Vorsicht zu behandeln.

## f:format.html

Wenn ihr ein Modell für die Tabelle `tt_content` erstellt habt, dann erhaltet ihr im Frontend die nackten Daten, wie sie aus der Datenbank kommen. Damit die Daten wieder so aussehen, wie wenn ihr sie mit `styles.content.get` ausgebt, hilft euch dieser ViewHelper. Dieser schnappt sich die Rohdaten und schleift diese einmal komplett durch den hinterlegten TypoScript-Objektpfad.

| Parameter | Erklärung |
|-----------|-----------|
| parseFuncTSPath | Formatiere einen Text anhand der TypoScript-Konfiguration, die sich standardmäßig in `lib.parseFunc_RTE` befindet. Diesen Pfad könnt ihr hier ändern. |

### Beispiel

```html
<f:format.html>{content.bodytext}</f:format.html>
```

### Beispiel/Trick mit abgeschalteten htmlspecialchars

```html
<f:format.html parseFuncTSPath="lib.parseFunc">{variable}</f:format.html>
```

## f:format.htmlentitiesDecode

| Parameter | Erklärung |
|-----------|-----------|
| value | Der Text der dekodiert werden soll |
| keepQuotes | Sollen einfache und doppelte Anführungsstriche auch dekodiert werden? Standard: deaktiviert |
| encoding | Wir sind hier im Bereich TYPO3 und da sollte der Zeichensatz auf UTF-8 und nix anderes stehen. Sollte wieder erwartend ein anderer Zeichensatz gewünscht sein, kann dieser hier angegeben werden. Standard: Wie im INSTALL_TOOL definiert. Normalerweise: UTF-8 |

### Beispiel

```html
<p><f:format.htmlentitiesDecode>M&uuml;ller &amp; Breuer</f:format.htmlentitiesDecode>
```

Im Quelltext sieht man wieder ein richtig sauberes: `Müller & Breuer`.

## f:format.htmlentities

Mit diesem ViewHelper wandelt ihr Sonderzeichen oder auch die deutschen Umlaute in web-sichere HTML-Codes um. Ein `ü` wird dann zu `&uuml;`. Damit kann gewährleistet werden, dass jeder Browser dieses Zeichen richtig anzeigt, und zwar egal wo auf der Welt. Seit der Standardzeichensatz von TYPO3 auf UTF-8 gesetzt ist, sollte diese Sache hier so nach und nach der Vergangenheit angehören.

| Parameter | Erklärung |
|-----------|-----------|
| value | Der Text der kodiert werden soll |
| keepQuotes | Sollen einfache und doppelte Anführungsstriche auch dekodiert werden? Standard: deaktiviert |
| encoding | Wir sind hier im Bereich TYPO3 und da sollte der Zeichensatz auf UTF-8 und nix anderes stehen. Sollte wieder erwartend ein anderer Zeichensatz gewünscht sein, kann dieser hier angegeben werden. Standard: Wie im INSTALL_TOOL definiert. Normalerweise: UTF-8 |

### Beispiel

```html
<f:alias map="{name: 'Müller & Breuer'}">
    <p><f:format.htmlentities>{name}</f:format.htmlentities></p>
</f:alias>
```

## f:format.htmlspecialchars

Wenn ihr mit Userdaten (Formulardaten) arbeitet, dann sollte dieser Inhalt zuallererst durch diesen ViewHelper. Denn dieser wandelt alle spitzen Klammern von HTML-Tags in ein nicht mehr interpretierbares Format um. Die HTML-Tags können also keinen Schaden mehr verursachen und werden im Frontend angezeigt statt verarbeitet.

| Parameter | Erklärung |
|-----------|-----------|
| value | Der Text der formatiert werden soll |
| keepQuotes | Sollen einfache und doppelte Anführungszeichen auch formatiert werden. Standard: deaktiviert |
| encoding ||
| doubleEncode | Mit jedem Aufruf diesen ViewHelpers wird ein Text, der bereits `PHP:htmlspecialchars()` durchlaufen hat, erneut durch `PHP:htmlspecialchars()` geschickt. Somit wird aus `&` erst `&amp;`, dann `&amp;amp;` und dann `&amp;amp;amp;`. Um das zu verhindert, kann dieser Parameter auf `0` gesetzt werden. |

### Beispiel

```html
<f:format.htmlspecialchars><p><strong>fetter</strong> Text</p></f:format.htmlspecialchars>
```

## f:format.nl2br

Dieser ViewHelper besitzt keine Parameter. Den umzuwandelnden Inhalt bezieht er sich aus dem Text zwischen den Tags. Sinnvoll wird dieser ViewHelper beim Anzeigen von Inhalten aus `textarea` Tags. Denn hier wurden die Zeilenumbrüche mit ENTER (CHR(10)) realisiert. Im HTML sind diese Umbrüche aber völlig egal und würde den Text einfach hintereinander weg anzeigen. Um das zu verhindern, könnt ihr diesen ViewHelper verwender. Er konvertiert alle CHR(10)-Zeilenumbrüche in `<br>` Tags und so werden Zeilenumbrüche auch im Browser wieder richtig dargestellt.

### Beispiel

```html
<f:format.nl2br>Text
mit
Zeilenumbrüchen</f:format.nl2br>
```

ergibt: `Text<br>mit<br>Zeilenumbrüchen`

## f:format.number

Mit diesem ViewHelper könnt ihr Zahlen formatieren. Er arbeitet ähnlich dem `f:format.currency` ViewHelper.

| Parameter | Erklärung |
|-----------|-----------|
| decimals | Wie viele Nachkommastellen dürfen angezeigt werden. |
| decimalSeperator | Welches Zeichen soll für den Trenner zwischen Zahl und Nachkommastellen verwendet werden? Standard: `.` |
| thousandsSeperator | Welches Zeichen soll als Tausendertrennzeichen verwendet werden. Standard: `,` |

### Beispiel

```html
<f:format.number decimals="3" decimalSeparator="." thousandsSeparator=",">1122334455.667788</f:format.number>
```

ergibt: `1,122,334,455.668`.

Wie ihr seht, wird sogar automatisch aufgerundet.

## f:format.padding

| Parameter | Erklärung |
|-----------|-----------|
| padLength | Wie viel Zeichen lang darf der Text inkl. der hinzugefügten Abstandszeichen maximal werden? |
| padString | Welches Zeichen soll als Abstand dienen? Standard: Leerzeichen |
| padType | Wo sollen die Abstandszeichen eingefügt werden? Zur Auswahl steht `right`, `left` und `both`. Standard: `right` |

### Beispiel zur Funktionsweise

In diesem Beispiel wird das angegebene Zeichen so lange wiederholt, bis das Maximum an Zeichen erfüllt ist. Je nach Zeichensatz macht diese Darstellung wenig Sinn. Denn wie ihr seht beginnen die letzten beiden Wörter je Zeile immer an einer unterschiedlichen Stelle.

```html
<p><f:format.padding padLength="10" padString="-=">TYPO3</f:format.padding>ist cool</p>
<p><f:format.padding padLength="10" padString="#">Stefan</f:format.padding>ist cool</p>
<p><f:format.padding padLength="10" padString=" ">Ich</f:format.padding>bin cool</p>
```

### Beispiel mit sinnvoller Einrückung

Hier nahezu das gleiche Beispiel wie oben. Allerdings fangen die letzten beiden Wörter je Zeile nun immer an der gleichen Position an. Mit Hilfe diesen ViewHelpers könnte man also so eine Art Tabulator erstellen.

```html
<pre><f:format.padding padLength="10" padString=" ">TYPO3</f:format.padding>ist cool<br />
<f:format.padding padLength="10" padString=" ">Stefan</f:format.padding>ist cool<br />
<f:format.padding padLength="10" padString=" ">Ich</f:format.padding>bin cool</pre>
```

## f:format.printf

Mit diesem ViewHelper können Platzhalter in einem Text mit den Werten aus dem Array ersetzt werden.

| Parameter | Erklärung |
|-----------|-----------|
| arguments | Gebt hier Werte in Arraynotation ein, die die Platzhalter in dem Text, der sich zwischen den Tags befindet, ersetzt. |

### Beispiel

Mit %1, %2 und %3 wird der jeweilige Wert aus dem Array geholt. Da es unterschiedliche Datentypen gibt, müssen wir jedem Wert noch mitteilen, um was für einen Datentyp es sich handelt. Dabei steht `$s` für `string` also Text. `$d` für einen Zahlenwert, der auch ein Vorzeichen enthalten darf und wie ihr evtl. schon gesehen habt, habe ich die Zahl `4` als Text deklariert, gebe sie aber als Typ Integer an den Text weiter.

```html
<f:format.printf arguments="{0: 'Stefan', 1: 2, 2: '4'}">%1$s hat eine %3$d-seitige Dokumentation geschrieben und %2$d Tage dafür gebraucht.</f:format.printf>
```

Wenn dieser ViewHelper überhaupt nicht angezeigt wird, dann habt ihr entweder einen Fehler in eurem Array oder ihr habe die Typdefinition der Variablen wie `$s` oder `$d` vergessen mit anzugeben.

## f:format.raw

Es gibt viele ViewHelper, die Inhalte vor der Ausgabe durch `PHP:htmlspecialchars()` schicken. Dies zu verhindern stellt auf jeden Fall ein Sicherheitsrisiko dar, kann aber mit diesem ViewHelper erreicht werden. Ich denke gerade im Bereich von Formulardaten, kann dieser ViewHelper evtl. Verwendung finden.

| Parameter | Erklärung |
|-----------|-----------|
| value | Der Text, der unangetastet/unverändert ausgegeben werden soll |

### Beispiel

```html
<f:format.raw value="{formData.nachricht}"/>
```

## f:format.stripTags

Dieser ViewHelper entfernt sämtliche HTML-Tags aus einem Text.

| Parameter | Erklärung |
|-----------|-----------|
| value | Der Text aus dem die HTML-Tags entfernt werden sollen |

### Beispiel

```html
<f:format.stripTags>Ein <strong>fetter</strong> Text mit einigen <span style="color: blue;">bunten</span> HTML-Tags.</f:format.stripTags>
```

## f:format.urlencode

In Texten und Firmennamen kommen immer wieder Sonderzeichen wie `@`, `&` oder `%` vor. Diese Zeichen sind nicht URL-sicher und sollten vor der Übermittlung durch diesen ViewHelper verarbeitet werden.

| Parameter | Erklärung |
|-----------|-----------|
| value | Der Text, der für die Übergabe per Link vorbereitet werden soll |

### Beispiel

```html
<f:format.urlencode>Text mit ein npaar Sonderzeichen, die für die URL entsprechend maskiert werden müssen: @+%/</f:format.urlencode>
```

Ergebnis:

```
Text%20mit%20ein%20npaar%20Sonderzeichen%2C%20die%20f%C3%BCr%20die%20URL%20entsprechend%20maskiert%20werden%20m%C3%BCssen%3A%20%40%2B%25%2F 
```
