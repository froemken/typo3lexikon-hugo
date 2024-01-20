+++
title = "HtmlParser und RteHtmlarea"
linkTitle = "Html"
date = 2024-01-09T20:18:28+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["html.html"]
+++

Auch wenn dieser Bereich arg nach Richtext Editor klingt, so hat dieser nichts mit den Editoren wie `rtehtmlarea` oder `CKEditor` zu tun. Die PHP Klassen in diesem Abschnitt kümmern sich nicht um das Aussehen und auch nicht um irgendwelche Buttons, Schriften und Farben irgendeines Editors, sondern um die Aufbereitung der Daten aus der Datenbank hin zum Editor und um die Speicherung der generierten Daten des Editors zurück zur Datenbank. Auch wenn Editor und HtmlParser in erster Linie nichts miteinander zu tun haben, so sollten sie zumindest im Einklang zusammen arbeiten. Dazu ein Beispiel: Wenn ihr den Editor so konfiguriert, dass nur fett und kursiv erlaubt ist, so könnten durch Copy&Paste oder andere Manipulationen ungewünschte HTML-Tags mit in den Editor reinwandern. Mithilfe der HtmlParser könnt ihr nun bestimmen, dass nur die Tags für fett und kursiv erlaubt sind. Alles andere fliegt gnadenlos raus. Auch ist es möglich, den veralteten b-Tag beim Speichern z.B. in den strong-Tag umzuwandeln.

Den Einsprungpunkt für diese Verarbeitung findet ihr in der RteHtmlParser in der Methode `RTE_transform`. Dort wird auf die Eigenschaft `proc` zugegriffen, die ich hier näher erklären möchte.

Unter Verwendung des CKEditors wurde die Konfiguration mittels YAML Datei hinzugefügt. Dort gibt es einen Abschnitt `processing`. In `core/Classes/Configuration/Richtext.php` gibt es diese Zeile:

```php
$configuration['proc.'] = $this->convertPlainArrayToTypoScriptArray($configuration['processing']);
```

Wie ihr seht, handelt es sich bei proc und processing um ein und dieselbe Konfiguration.

## Transformations

Bei den Transformations handelt es sich um besondere Tätigkeiten, die sich nicht so einfach mithilfe der proc Eigenschaften umsetzen lassen. Ein Beispiel: In TYPO3 gibt es schon seit einer Weile den LinkChecker. Das ist ein Scheduler Task, den ihr einrichten könnt, der euch über nicht mehr funktionierende Links informiert. Diese defekten Links werden euch im Editor gelb mit rotem Rahmen hervorgehoben. Es wäre doch fatal, wenn dieses ins Auge stechende "Design" auf eurer Webseite erscheint. Für genau solche Anwendungen gibt es die Transformation detectbrokenlinks. Beim Speichern des Inhalts des Editors werden diese CSS Stile und auch CSS-Klassen entfernt, damit sie sich nicht auf die Ausgabe im Frontend auswirken. Wenn ihr den Datensatz wieder im RTE öffnet, dann durchläuft der Inhalt abermals die Transformation detectbrokenlinks, diesmal jedoch prüft sie den Link und fügt die CSS-Klassen und CSS-Stile wieder hinzu, sollte es sich um einen ungültigen oder defekten Link handeln.

Aktuell gibt es folgende Transformations
: detectbrokenlinks
  : Entfernt bzw. fügt allen defekten Links einen gelben Hintergrund mit rotem Rahmen hinzu 
: ts_images
  : Fügt allen Bildern das alt-Attribut hinzu
  : Wenn dem Bild Größenangaben zugeordnet wurden, dann werden dem IMG-Tag noch die Attribute width und height hinzugefügt.
  : Wenn das Bild über FAL kommt, hat es nur eine UID. Diese UID wird in einen absoluten Pfad aufgelöst.
: ts_links
  : Konvertiere Links, sofern möglich, in die neue t3:// Syntax
: css_transform
  : Ändere leere p-Tags nach <p>&nbsp;</p>
  : Wendet auf den Inhalt innerhalb von p-Tags die Konfiguration von proc.HTMLparser_db (beim Speichern) bzw. proc.HTMLparser_rte (beim Laden) an.
: default
  : Keine Transformation an sich. Durch die Angabe von default werden automatisch alle 4 Transformations `detectbrokenlinks`, `ts_images`, `ts_links` und `css_transform` gesetzt. Wenn weder `proc.mode` noch `proc.overruleMode` gesetzt sind, wird `default` als Vorgabe gewählt.
: individueller Transformation Mode
  : Ihr könnt über den Hook `$GLOBALS['TYPO3_CONF_VARS']['SC_OPTIONS']['t3lib/class.t3lib_parsehtml_proc.php']['transformation'][$individuellerTransformationMode]` eure eigenen Transformations registrieren.

Früher konnte man die zu verwendenden Transformations im TCA über mode setzen. Ich habe im gesamt TYPO3 10er Core jedoch nichts mehr dazu gefunden und gehe mal davon aus, dass es diese Eigenschaft dort nicht mehr gibt. Innerhalb von pageTSconfig nutzt der TYPO3 Core die undokumentierte proc.mode Eigenschaft jedoch immer noch als Defaultwert. Offiziell wird erst proc.overruleMode dokumentiert, die eine komma separierte Liste von Transformations aufnehmen kann und den Wert aus proc.mode komplett überschreibt. Im Gegensatz zu proc.overruleMode darf proc.mode nur exakt eine Transformation beinhalten und die ist standardmäßig default.

## Die proc Eigenschaft

Mit der `proc` Eigenschaft könnt ihr sehr individuell einstellen, wie der Inhalt beim Speichern oder Laden aus der Datenbank modifiziert werden soll. Hier geht es um einfache HTML Tag Änderungen, Hinzufügen von CSS-Klassen oder Entfernung von HTML Attributen. Für komplexere Anwendungen, wie URL-Prüfungen oder dynamisches Hinzufügen von Tag Wrappings verwendet bitte entsprechende Plugins des Editors oder wie oben schon erklärt die Möglichkeit eigener Transformations.

### allowedClasses

Wirkt sich nur aus, wenn die css_transform Transformation aktiv ist.

Wirkt sich nur auf die Konfiguration unterhalb von proc.HTMLparser_db und proc.HTMLparser_rte aus.

Erlaubt ist die Angabe eines Komma separierten Strings oder mittels YAML die Array Syntax.

Angabe von CSS Klassen, die nur für p und span Tags erlaubt sind. Beispiel: Erlaubt nur die CSS Klassen text-left und text-right:

```yaml
processing:
  mode: default
  allowedClasses:
    - text-left
    - text-right
```

Es ist ein Irrglaube zu denken, dass sich dieser Wert auch auf Überschriften oder Listen auswirkt, auch wenn der Editor die CSS-Klassen nach einem Reload nicht mehr anzeigt. Öffnet mal die Dev-Tools im Browser und schaut euch die versteckte textarea genauer an.

```html
<textarea style="display: none; visibility: hidden;" data-formengine-validation-rules="[]" id="data_tt_content__7__bodytext_" name="data[tt_content][7][bodytext]">&lt;h2 class="text-bla"&gt;Header 2&lt;/h2&gt;
</textarea>
```

Die CSS Klasse text-bla wurde für die H2 Überschrift abgespeichert.

### allowTags

Wirkt sich nur aus, wenn die css_transform Transformation aktiv ist.

Wirkt sich nur auf die Konfiguration unterhalb von proc.HTMLparser_db und proc.HTMLparser_rte aus.

Erlaubt ist die Angabe eines Komma separierten Strings oder mittels YAML die Array Syntax.

Mit `allowTags` gebt ihr eine Liste von HTML Tags an, die innerhalb von p Tags erlaubt sind. Alle angegebenen Tags werden dieser Liste von Tags hinzugefügt:

```
b,i,u,a,img,br,div,center,pre,font,hr,sub,sup,p,strong,em,li,ul,ol,blockquote,strike,span,abbr,acronym,dfn
```

Da z.B. Überschriften bereits in der blockElementList enthalten sind, ist es arg unwahrscheinlich, dass sich Überschriften und andere Blockelemente innerhalb von p Tags befinden werden. Eigentlich kann man sagen, dass es keinen Sinn macht die Tags aus blockElementList hier zusätzlich zu definieren. Da auch ol, ul und blockquote zu den Blockelementen zählen, gehören diese Tags nach meinem Empfinden nicht in die Liste der allowTags rein und sollten mittels denyTags entfernt werden. Selbst wenn ihr eine sehr schräge Konfiguration habt und z.B. Überschriften aus den Blockelementen entfernt und hier bei den allowTags hinzufügt und es dann auch noch schafft sowas wie <p><h2>Header 2</h2></p> abzuspeichern, dann wird das sogar 1zu1 in der Datenbank gespeichert, aber spätestens beim Laden flippt der Editor aus und baut euch ein daraus ein <p>&nbsp</p><h2>Header 2</h2><p>&nbsp</p>. In dem versteckten textarea Element steht jedoch weiterhin:

```html
<textarea style="display: none; visibility: hidden;" data-formengine-validation-rules="[]" id="data_tt_content__7__bodytext_" name="data[tt_content][7][bodytext]">&lt;p&gt;&lt;h2&gt;Header 2&lt;/h2&gt;&lt;/p&gt;
</textarea>
```

Bitte bleibt dem HTML Standard treu und baut keine Blockelement in den p Tag rein.

### allowTagsOutside

Wirkt sich nur aus, wenn die css_transform Transformation aktiv ist.

Wirkt sich nur auf die Konfiguration unterhalb von proc.HTMLparser_db und proc.HTMLparser_rte aus.

Erlaubt ist die Angabe eines Komma separierten Strings oder mittels YAML die Array Syntax.

Tragt hier alle HTML Tags ein, die durch die blockElementList nicht verarbeitet wurden und sich außerhalb des p Tags befinden dürfen. Aber nochmal langsam: Nach der Verarbeitung der Blockelemente werden die p Tags verarbeitet. Alle Tags, die keine Blockelemente sind und sich nicht innerhalb des p Tags befinden gelten als Inhalt außerhalb. Beispiel

```html
<p>Fließtext</p>
<br>
<hr>
<h2>Header 2</h2>
```

Der Inhalt wird von oben nach unten durchlaufen, bis der HtmlParser auf das erste Blockelement stößt. In diesem Fall ist es das H2 Tag. Das p Tag ist zwar auch ein HTML technisch gesehen ein Blockelement, aber es ist in blockElementList nicht als solches deklariert und sollte dort auch nie als Blockelement definiert werden, da es eine besondere Verarbeitung im Parser wiederfährt. Jeglicher Inhalt bis zum ersten Blockelement H2 wird nun an die spezielle Verarbeitungsprozedur für p Tags weitergereicht. Hier wird nun zwischen innerhalb und außerhalb des p Tags unterschieden. Der Fließtext ist innerhalb und wird mittels proc.HTMLparser_db und proc.HTMLparser_rte weiter verarbeitet. Die Tags außerhalb werden komplett entfernt. Ausnahme: Die Tags wurden mittels allowTagsOutside explizit erlaubt. In diesem Falle werden sie mittels einem p Tag umwickelt. Wenn ihr also br und hr als allowTagsOutside erlaubt, dann steht beim Speichern in der Datenbank:

```html
<p>Fließtext</p>
<p><br></p>
<p><hr></p>
<h2>Header 2</h2>
```

Je nach Konfiguration des Editors kann es sein, dass das br Tag verschwindet, es ist aber technisch gesehen noch vorhanden. Siehe Quelltext eurer textarea.

### blockElementList

Wirkt sich nur aus, wenn die css_transform Transformation aktiv ist.

Wirkt sich noch vor der Konfiguration von proc.HTMLparser_db und proc.HTMLparser_rte aus.

Erlaubt ist nur die Angabe eines Komma separierten Strings, auch in YAML ist nur ein String erlaubt. Da hier mit GeneralUtility::inList gearbeitet wird, darf der String keinerlei Leerzeichen zwischen den Tags aufweisen und alle Tags müssen in Großbuchstaben angegeben werden.

Tragt in blockElementList alle Tags ein, die als Blockelemente (außer dem p Tag) zu verarbeiten sind. Zu den Blockelementen gehören üblicherweise Tags wie blockquote, ul und div. Alle Blockelemente werden nahezu 1zu1 in der Datenbank abgespeichert und unterliegen nicht der Konfiguration aus proc.HTMLparser_db und proc.HTMLparser_rte. Auch die Eigenschaft allowedClasses findet hier keine Anwendung. Um Blockelemente zu manipulieren benutzt bitte die Konfiguration für die exit- und entryHTMLparser_db.

Alle HTML Tags, die nicht durch die blockElementList abgedeckt sind, werden an die spezielle p Tag Verarbeitung in proc.HTMLparser_db weitergeleitet.

### denyTags

Wirkt sich nur aus, wenn die css_transform Transformation aktiv ist.

Wirkt sich nur auf die Konfiguration unterhalb von proc.HTMLparser_db und proc.HTMLparser_rte aus.

Erlaubt ist nur die Angabe eines Komma separierten Strings, auch mittels YAML muss es ein String sein.

Da TYPO3 von Haus aus schon ca. 25 Tags erlaubt (siehe allowTags) könnt ihr hier bestimmte Tags verbieten.

### entryHTMLparser_db

Mit diesem Parser könnt ihr den Inhalt des Editors auf dem Weg zur Datenbank modifizieren. Der Parser wird noch VOR den Transformations ausgeführt.

### entryHTMLparser_rte

Mit diesem Parser könnt ihr den Inhalt des Editors auf dem Weg zum Editor modifizieren. Der Parser wird noch VOR den Transformations ausgeführt.

### exitHTMLparser_db

Mit diesem Parser könnt ihr den Inhalt des Editors auf dem Weg zur Datenbank modifizieren. Der Parser wird NACH den Transformations ausgeführt.

### exitHTMLparser_rte

Mit diesem Parser könnt ihr den Inhalt des Editors auf dem Weg zum Editor modifizieren. Der Parser wird NACH den Transformations ausgeführt.

### HTMLparser_db

Mit diesem Parser beeinflusst ihr die Inhalte innerhalb der p Tags beim Speichern. Ihr habt hier keinen Einfluss auf Überschriften, Listen und anderen Blockelementen.

### HTMLparser_rte

Mit diesem Parser beeinflusst ihr die Inhalte innerhalb der p Tags beim Laden. Ihr habt hier keinen Einfluss auf Überschriften, Listen und anderen Blockelementen.

### overruleMode

Mit dieser Eigenschaft überschreibt ihr die Standard Transformation von TYPO3, die üblicherweise auf default eingestellt ist. Im Gegensatz zu proc.mode, wo nur exakte eine Transformation erlaubt ist, könnt ihr bei proc.overruleMode mehrere Transformations Komma separiert angeben.
