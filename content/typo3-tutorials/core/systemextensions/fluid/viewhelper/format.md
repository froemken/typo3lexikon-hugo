+++
title = "Format"
date = 2024-01-09T20:27:32+01:00
aliases = ["format.html"]
+++

## f:format.bytes

{{% badge style="green" icon="angle-double-up" %}}TYPO3 6.2{{% /badge %}}

Dieser ViewHelper teilt Bytes (Dateigröße) solange durch `1024`, bis das Resultat max. 3-stellig ist oder das Maximum an verfügbaren Einheiten erreicht ist.

### Parameter

| Parameter | Erklärung | Standard |
|-----------|-----------|----------|
| value | Die Dateigröße in Bytes. Muss eine Ganzzahl sein.  ||
| decimals | Angabe der Nachkommastellen | 0 |
| decimalSeparator | Zeichen für die Trennung der Nachkommastellen | `.` |
| thousandsSeparator | Zeichen, um die Tausenderstellen hervorzuheben | `,` |
| units | Angabe der Einheiten aufsteigend. Teilung findet in 1024er Schritten statt. Der hier aufgeführte Standardwert kommt aus einer englischen Übersetzungsdatei und kann somit je nach Sprache differenzieren. | B,KB,MB,GB,TB,PB,EB,ZB,YB |

### Beispiel: Nachkommastellen

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.bytes decimals="2">23748596</f:format.bytes>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```
22.65 MB
```
{{% /tab %}}
{{% /tabs %}}

### Beispiel: Array Notation

Hier seht ihr, dass der `thousandsSeparator` keine Auswirkung hat. Solange noch `units` zum Teilen verfügbar sind, findet der Parameter keine Anwendung.

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:variable name="filesize">8374832920384</f:variable>
{filesize -> f:format.bytes(decimals: 4, decimalSeparator: ',', thousandsSeparator: '.')}
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```
7,6169 TB
```
{{% /tab %}}
{{% /tabs %}}

### Beispiel: Eigene Units

Durch die Reduzierung auf 4 `units` kann die `value` Angabe nur 4-mal durch `1024` geteilt werden. Dadurch wirkt sich der `thousandsSeparator` erstmalig aus.

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.bytes decimals="2" units="Bytes,KiloBytes,MegaBytes,GigaBytes">25987463422954872</f:format.bytes>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```
24,202,711.34 GigaBytes
```
{{% /tab %}}
{{% /tabs %}}

## f:format.case

{{% badge style="green" icon="angle-double-up" %}}TYPO3 7.2{{% /badge %}}

Schreibt einen Text in Groß- oder Kleinschreibung, aber auch Wortanfänge können groß- oder kleingeschrieben werden.

### Parameter

| Parameter | Erklärung | Standard |
|-----------|-----------|----------|
| value | Der Text oder die Wörter, die groß- oder kleinzuschreiben sind. ||
| mode | Angabe eines Modus, wie der Text oder auch die Wörter zu schreiben sind. Siehe Tabelle `Modus` | upper |

### Modus

| Parameter | Erklärung |
|-----------|-----------|
| lower | Der komplette Text aus `value` wird kleingeschrieben. |
| upper | Der komplette Text aus `value` wird großgeschrieben. |
| capital | Der Text aus `value` wird nur am Anfang großgeschrieben.. |
| uncapital | Der Text aus `value` wird nur am Anfang kleingeschrieben. |
| capitalWords | {{% badge style="green" icon="angle-double-up" %}}TYPO3 8.7{{% /badge %}} Alle Wörter im Text aus `value` werden am Anfang großgeschrieben. |

### Beispiel

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.case mode="upper">ich werde mal ganz groß rauskommen</f:format.case><br>
<f:format.case mode="lower">Ich fühle mich ERNIEDRIGT</f:format.case><br>
<f:format.case mode="capital">ich werde am Anfang großgeschrieben</f:format.case><br>
<f:format.case mode="uncapital">Nur am Anfang werd ich klein</f:format.case><br>
<f:format.case mode="capitalWords">aller anfang ist groß</f:format.case><br>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```
ICH WERDE MAL GANZ GROSS RAUSKOMMEN
ich fühle mich erniedrigt
Ich werde am Anfang großgeschrieben
nur am Anfang werd ich klein
Aller Anfang Ist Groß
```
{{% /tab %}}
{{% /tabs %}}

## f:format.cdata

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.7{{% /badge %}}
{{% badge style="blue" icon="angle-double-up" %}}typo3fluid/fluid 1.0.6{{% /badge %}}

Bei [CDATA](https://de.wikipedia.org/wiki/CDATA) handelt es sich um einen Identifikator innerhalb vom XML, um dem XML-Parser mitzuteilen, dass der nachfolgende Text nicht als XML zu interpretieren ist. Die Sprachdateien in TYPO3 sind eine XML-Struktur. Wenn die Übersetzungen HTML, was auch eine XML-Struktur ist, enthalten, dann können diese HTML-Tags zu Fehlern führen.

Oder ein RSS-Feed. Dieser ist auch eine XML-Struktur. Wenn da Inhaltselemente ausgegeben werden, dann können diese auch HTML beinhalten und die XML-Ausgabe stören und Fehler provozieren.

Solltet ihr mit Fluid-Template solche XML-Strukturen bauen, dann verwendet bitte diesen ViewHelper. Es findet keine Konvertierung (escapen) des Inhaltes statt. Der Inhalt wird 1 zu 1 verwendet und nur in `<![CDATA[Euer Text mit <b>HTML</b>]]>` eingewickelt.

### Parameter

| Parameter | Erklärung |
|-----------|-----------|
| value | Der Text mit möglicherweise enthaltenen HTML-Tags, der für eine XML-Ausgabe in CDATA-Tags eingewickelt werden soll. |

### Beispiel

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```xml
<event>
    <date>23.05.2024</date>
    <title>TYPO3 Camp</title>
    <bodytext><f:format.cdata>Ich freue mich <strong>riesig</strong> auf das TYPO3 Camp</f:format.cdata></bodytext>
</event>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```xml
<event>
    <date>23.05.2024</date>
    <title>TYPO3 Camp</title>
    <bodytext><![CDATA[Ich freue mich <strong>riesig</strong> auf das TYPO3 Camp]]></bodytext>
</event>
```
{{% /tab %}}
{{% /tabs %}}

## f:format.crop

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Schneidet den Text zwischen öffnendem und schließendem Tag nach X Zeichen an geeigneter Stelle. Der abgeschnittene Text wird mit dem Wert aus `append` ersetzt. Dieser ViewHelper wird gerne verwendet, um lange Texte für Teaser anzupassen.

### Parameter

| Parameter | Erklärung | Standard |
|-----------|-----------|----------|
| maxCharacters | Maximale Anzahl Zeichen, nach denen der Text zwischen öffnendem und schließendem Tag abgeschnitten werden soll. ||
| append | Zeichenkette, die dem Leser signalisiert, dass hier der Text abgeschnitten wurde. | `&hellip;` -> ... |
| respectWordBoundaries | Wenn aktiv, dann wird verhindert, dass mitten in einem Wort ein Schnitt stattfindet. Der Schnitt findet dann bei der nächstmöglichen freien Stelle vor dem Wort statt. | `true` |
| respectHtml | Wenn aktiv, dann wird verhindert, dass enthaltene HTML Tags im Text nicht beschnitten werden. Der Schnitt findet dann bei der nächstmöglichen Stelle vor dem HTML Tag statt. | `true` |

### Beispiel: Wörter respektieren

Da `respectWordBoundaries` standardmäßig aktiv ist, findet hier keine Trennung im Wort `werden` statt, sondern in dem Leerraum davor.

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.crop maxCharacters="14">Diesen Text werden wir jetzt kürzen</f:format.crop>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```
Diesen Text…
```
{{% /tab %}}
{{% /tabs %}}

### Beispiel: Im Wort trennen

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.crop maxCharacters="14" respectWordBoundaries="false">Diesen Text werden wir jetzt kürzen</f:format.crop>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```
Diesen Text we…
```
{{% /tab %}}
{{% /tabs %}}

### Beispiel: Im HTML trennen

Wenn `respectHtml` deaktiviert wird, dann wird der komplette Text als reiner Text interpretiert (Klasse: `\TYPO3\CMS\Core\Text\TextCropper`).

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.crop maxCharacters="36" respectHtml="false" respectWordBoundaries="true"><p>Ein schönes Bild</p><img src="#" width="128" height="128" title="nice" alt="sonne"><p>Nicht wahr?</p></f:format.crop>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```html
<p>Ein schönes Bild</p><img src="#"&hellip;
```
{{% /tab %}}
{{% /tabs %}}

### Beispiel: Nicht im HTML trennen

Wenn `respectHtml` aktiviert ist, dann bezieht sich `maxCharacters` nur auf den reinen Text. Jegliches HTML wird für die Zählung nicht berücksichtigt (Klasse: `\TYPO3\CMS\Core\Text\HtmlCropper`). Jeder einzelne HTML-Tag und jeder Text werden in einzelne Abschnitte aufgeteilt. `Ein schönes Bild` und `Nicht wahr?` sind also individuelle Abschnitte, die sich nicht kennen.

Im ersten Crop befindet sich der 18te Buchstabe innerhalb des Wortes `Nicht`. Da `respectWordBoundaries` aktiv ist, wird die nächste freie Stelle vor dem Wort gesucht. Da es sich hier um das erste Wort in diesem Abschnitt handelt und der `HtmlCropper` nichts von dem anderen Abschnitt weiß, würde er keine Ausgabe generieren. Um das zu verhindern, schneidet er trotz aktivierten `respectWordBoundaries` innerhalb des Wortes.

Im zweiten Crop befindet sich der 24te Buchstabe innerhalb des zweiten Wortes `wahr` des zweiten Abschnittes. Da `respectWordBoundaries` aktiv ist, wird die nächste freie Stelle vor dem Wort gesucht. Der `HtmlCropper` findet die freie Stelle und schneidet sauber zwischen den beiden Wörtern.

Im dritten Crop befindet sich der 24te Buchstabe auch innerhalb des zweiten Wortes `wahr` des zweiten Abschnittes. Da `respectWordBoundaries` nicht aktiv ist, wird innerhalb des Wortes `wahr` getrennt.

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.crop maxCharacters="18" respectHtml="true" respectWordBoundaries="true"><p>Ein schönes Bild</p><img src="#" width="128" height="128" title="nice" alt="sonne"><p>Nicht wahr?</p></f:format.crop>
<f:format.crop maxCharacters="24" respectHtml="true" respectWordBoundaries="true"><p>Ein schönes Bild</p><img src="#" width="128" height="128" title="nice" alt="sonne"><p>Nicht wahr?</p></f:format.crop>
<f:format.crop maxCharacters="24" respectHtml="true" respectWordBoundaries="false"><p>Ein schönes Bild</p><img src="#" width="128" height="128" title="nice" alt="sonne"><p>Nicht wahr?</p></f:format.crop>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```html
<p>Ein schönes Bild</p><img src="#" width="128" height="128" title="nice" alt="sonne"/><p>Ni&hellip;</p>
<p>Ein schönes Bild</p><img src="#" width="128" height="128" title="nice" alt="sonne"/><p>Nicht&hellip;</p>
<p>Ein schönes Bild</p><img src="#" width="128" height="128" title="nice" alt="sonne"/><p>Nicht wa&hellip;</p>
```
{{% /tab %}}
{{% /tabs %}}

## f:format.currency

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Mit diesem ViewHelper könnt ihr Zahlen als Währung darstellen lassen. Doch zwei wichtige Informationen:

Übergebt ihr an diesen ViewHelper einen leeren Text, dann benötigt die in diesem ViewHelper befindliche Methode `PHP:number_format()` knapp 23 Millisekunden. Liefert ihr dieser Methode stattdessen direkt ein `0.00` ist die Methode in 1-2 Millisekunden durch. Das kann gerade bei größeren Listen Performance-Problemen entgegenwirken.

{{% notice style="info" title="Wichtig" icon="exclamation" %}}
Ihr dürft diesem ViewHelper keine Zahlen mit einem Komma als Dezimaltrenner mitgeben. Schaut euch dazu die Beispiele unten an.
{{% /notice %}}

### Parameter

| Parameter | Erklärung | Standard |
|-----------|-----------|----------|
| currencySign | Das Währungskennzeichen wie `$` oder `€`. ||
| decimalSeperator | Zeichen zum Einleiten der Nachkommastellen. | `,` |
| thousandsSeperator | Zeichen zum Hervorheben der Tausenderstellen. Standard:  | `.` |
| prependCurrency | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}} Wenn aktiviert, wird das Währungskennzeichen dem Betrag vorangestellt. Interessant für Währungen in Dollar. | `false` |
| separateCurrency | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}} Wenn aktiv, wird zwischen Währungskennzeichen und Betrag ein Leerzeichen eingefügt. | `true` |
| decimals | {{% badge style="green" icon="angle-double-up" %}}TYPO3 6.1{{% /badge %}} Anzahl der Nachkommastellen. | 2 |
| useDash | {{% badge style="green" icon="angle-double-up" %}}TYPO3 9.4{{% /badge %}} Wenn aktiviert, dann werden bei vollen Beträgen bzw. wenn alle Nachkommastellen `0` sind, diese durch einen Bindestrich ersetzt. | `false` |

### Beispiel

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.currency currencySign="$" prependCurrency="true" decimalSeparator="." thousandsSeparator=",">1122334455.66</f:format.currency>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```
$ 1,122,334,455.66
```
{{% /tab %}}
{{% /tabs %}}

## f:format.date

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Modifiziert und gibt ein Datum in einem vorgegebenen Format aus.

### Parameter

| Parameter | Erklärung | Standard |
|-----------|-----------|----------|
| date | Entweder ein Datum vom Typ `\DateTime` oder ein Wert, der in ein `\DateTime` Objekt konvertiert werden kann (z.B. `17.01.1979`). Es kann auch der Timestamp (Sekunden seit 01.01.1970) angegeben werden. Relative Angaben wie `+2 months` modifizieren das aktuelle Datum. Um relative Angaben auf ein anderes Datum anzuwenden, muss das Datum in `base` definiert werden. ||
| format | Formatvorgaben für die Ausgabe des Datums. [DateTime Formatierungen](https://www.php.net/manual/en/datetime.format.php) werden intern mit `PHP:date()` formatiert, können jedoch nur englische Ausgaben (Sunday, ...).<br>{{% badge style="orange" icon="angle-double-up" %}}PHP 8.1{{% /badge %}} [strftime Formatierungen](https://www.php.net/manual/en/function.strftime) werden intern mit `PHP:strftime()` formatiert und können auch mehrsprachige Ausgabe erzeugen (Sonntag, ...). Die Unterstützung für `strftime` ist in TYPO3 13 noch gegeben, könnte jedoch mit TYPO3 14 entfernt werden. | `Y-m-d` |
| pattern | {{% badge style="green" icon="angle-double-up" %}}TYPO3 12.3{{% /badge %}} Formatvorgaben für die Ausgabe des Datums. Da `PHP:date()` nur englische Ausgaben kann und `PHP:strftime()` veraltet ist, wird `PHP:IntlDateFormatter` diese ersetzen. Dieser Formatter benötigt die Formatvorgaben jedoch im [Unicode ICO Format](https://unicode-org.github.io/icu/userguide/format_parse/datetime/#datetime-format-syntax). Wenn angegeben, wird `format` nicht mehr berücksichtigt. ||
| locale | {{% badge style="green" icon="angle-double-up" %}}TYPO3 12.3{{% /badge %}} Angabe der zu verwendenden Sprache in dem Format `de_AT`, `fr` oder `en_US.utf8`. Wird nur berücksichtigt, wenn auch `pattern` benutzt wird. ||
| base | {{% badge style="green" icon="angle-double-up" %}}TYPO3 7.4{{% /badge %}} Um in `date` mit [relativen Formaten](https://www.php.net/manual/en/datetime.formats.php#datetime.formats.relative) arbeiten zu können, muss das zugrundeliegende Datum hier angegeben werden. Akzeptiert nur `\DaTime` Objekt oder Timestamp (Sekunden seit 01.01.1970) ||

### Beispiel: date

Wochentage und Monatsnamen werden in englischer Sprache ausgeben.

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.date date="17.01.1979" format="l \d\e\n d F y"/><br>
<f:format.date date="285375600" format="l \d\e\n d F y"/><br>
{person.birthday -> f:format.date(format: 'l \d\e\n d F y')}
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```
Wednesday den 17 January 1979
Wednesday den 17 January 1979
Wednesday den 17 January 1979
```
{{% /tab %}}
{{% /tabs %}}

### Beispiel: strftime

Wochentage und Monatsnamen werden in der Sprache ausgegeben, wie mit `PHP:setlocale()` konfiguriert.

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.date date="17.01.1979" format="%A den %d %B %Y"/><br>
<f:format.date date="285375600" format="%A den %d %B %Y"/><br>
{person.birthday -> f:format.date(format: '%A den %d %B %Y')}
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```
Mittwoch den 17 Januar 1979
Mittwoch den 17 Januar 1979
Mittwoch den 17 Januar 1979
```
{{% /tab %}}
{{% /tabs %}}

### Beispiel: Relative Formatierung

Mit relativen Formatierungen kann ein Datum modifiziert und anschließend formatiert werden..

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<div>Aktuelles Datum + 2 Wochen</div>
<f:format.date date="+2 weeks" format="%A den %d %B %Y"/><br>
<div>Anderes Datum + 2 Wochen</div>
<f:format.date date="+2 weeks" base="285375600" format="%A den %d %B %Y"/><br>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```html
<div>Aktuelles Datum + 2 Wochen</div>
Saturday den 02 March 2024<br>
<div>Anderes Datum + 2 Wochen</div>
Tuesday den 30 January 1979<br>
```
{{% /tab %}}
{{% /tabs %}}

### Beispiel: Unicode ICU

Mit PHP 8.1 ist `PHP:strftime` veraltet. Um weiterhin mit übersetzten Wochentagen und Monatsnamen arbeiten zu können, muss mit `pattern` und `locale` gearbeitet werden.

{{< tabs >}}
{{% tab title="Fluid Template" %}}
```html
<f:format.date date="+2 weeks" pattern="EEEE 'den' dd LLLL yyyy"/><br>
```
{{% /tab %}}
{{% tab title="Ausgabe" %}}
```html
Samstag den 02 März 2024<br>
```
{{% /tab %}}
{{% /tabs %}}

## f:format.html

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Wenn ihr ein Modell für die Tabelle `tt_content` erstellt habt, dann erhaltet ihr im Frontend die nackten Daten, wie sie aus der Datenbank kommen. Damit die Daten wieder so aussehen, wie wenn ihr sie mit `styles.content.get` ausgebt, hilft euch dieser ViewHelper. Dieser schnappt sich die Rohdaten und schleift diese einmal komplett durch den hinterlegten TypoScript-Objektpfad.

### Parameter

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

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}

### Parameter

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

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}

Mit diesem ViewHelper wandelt ihr Sonderzeichen oder auch die deutschen Umlaute in web-sichere HTML-Codes um. Ein `ü` wird dann zu `&uuml;`. Damit kann gewährleistet werden, dass jeder Browser dieses Zeichen richtig anzeigt, und zwar egal wo auf der Welt. Seit der Standardzeichensatz von TYPO3 auf UTF-8 gesetzt ist, sollte diese Sache hier so nach und nach der Vergangenheit angehören.

### Parameter

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

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}
{{% badge style="blue" icon="angle-double-up" %}}typo3fluid/fluid 1.0.6{{% /badge %}}

Wenn ihr mit Userdaten (Formulardaten) arbeitet, dann sollte dieser Inhalt zuallererst durch diesen ViewHelper. Denn dieser wandelt alle spitzen Klammern von HTML-Tags in ein nicht mehr interpretierbares Format um. Die HTML-Tags können also keinen Schaden mehr verursachen und werden im Frontend angezeigt statt verarbeitet.

### Parameter

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

## f:format.json

{{% badge style="green" icon="angle-double-up" %}}TYPO3 8.6{{% /badge %}}

Dokumentation folgt

## f:format.nl2br

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Dieser ViewHelper besitzt keine Parameter. Den umzuwandelnden Inhalt bezieht er sich aus dem Text zwischen den Tags. Sinnvoll wird dieser ViewHelper beim Anzeigen von Inhalten aus `textarea` Tags. Denn hier wurden die Zeilenumbrüche mit ENTER (CHR(10)) realisiert. Im HTML sind diese Umbrüche aber völlig egal und würde den Text einfach hintereinander weg anzeigen. Um das zu verhindern, könnt ihr diesen ViewHelper verwender. Er konvertiert alle CHR(10)-Zeilenumbrüche in `<br>` Tags und so werden Zeilenumbrüche auch im Browser wieder richtig dargestellt.

### Beispiel

```html
<f:format.nl2br>Text
mit
Zeilenumbrüchen</f:format.nl2br>
```

ergibt: `Text<br>mit<br>Zeilenumbrüchen`

## f:format.number

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Mit diesem ViewHelper könnt ihr Zahlen formatieren. Er arbeitet ähnlich dem `f:format.currency` ViewHelper.

### Parameter

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

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

### Parameter

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

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}
{{% badge style="blue" icon="angle-double-up" %}}typo3fluid/fluid 1.0.6{{% /badge %}}

Mit diesem ViewHelper können Platzhalter in einem Text mit den Werten aus dem Array ersetzt werden.

### Parameter

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

{{% badge style="green" icon="angle-double-up" %}}TYPO3 8.0.0{{% /badge %}}
{{% badge style="blue" icon="angle-double-up" %}}typo3fluid/fluid 1.0.6{{% /badge %}}

Es gibt viele ViewHelper, die Inhalte vor der Ausgabe durch `PHP:htmlspecialchars()` schicken. Dies zu verhindern stellt auf jeden Fall ein Sicherheitsrisiko dar, kann aber mit diesem ViewHelper erreicht werden. Ich denke gerade im Bereich von Formulardaten, kann dieser ViewHelper evtl. Verwendung finden.

### Parameter

| Parameter | Erklärung |
|-----------|-----------|
| value | Der Text, der unangetastet/unverändert ausgegeben werden soll |

### Beispiel

```html
<f:format.raw value="{formData.nachricht}"/>
```

## f:format.stripTags

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}

Dieser ViewHelper entfernt sämtliche HTML-Tags aus einem Text.

### Parameter

| Parameter | Erklärung |
|-----------|-----------|
| value | Der Text aus dem die HTML-Tags entfernt werden sollen |

### Beispiel

```html
<f:format.stripTags>Ein <strong>fetter</strong> Text mit einigen <span style="color: blue;">bunten</span> HTML-Tags.</f:format.stripTags>
```

## f:format.trim

{{% badge style="green" icon="angle-double-up" %}}TYPO3 12.4{{% /badge %}}

Dokumentation folgt

## f:format.urlencode

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}

In Texten und Firmennamen kommen immer wieder Sonderzeichen wie `@`, `&` oder `%` vor. Diese Zeichen sind nicht URL-sicher und sollten vor der Übermittlung durch diesen ViewHelper verarbeitet werden.

### Parameter

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
