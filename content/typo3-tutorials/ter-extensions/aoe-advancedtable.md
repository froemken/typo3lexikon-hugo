+++
title = "aoe_advancedtable"
date = 2024-01-09T20:50:39+01:00
aliases = ["aoe-advancedtable.html"]
+++

Das Contentelement `Table` ist in seinen Möglichkeiten sehr beschränkt. Attribute wie `colspan` und `rowspan` sind völlig unbekannt und CSS-Klassendefinitionen pro Tabellenzelle sind auch nicht möglich. Das wird auch der Grund sein, warum immer mehr Redakteure zu dem Contentelement `HTML` für die Tabellengestaltung greifen. Natürlich, das funktioniert, aber ohne Quelltexterfahrung läuft hier gar nichts mehr.

Diese Probleme können mit der Extension `aoe_advancedtable` gelöst werden.

`aoe_advancedtable` baut auf die Extension `th_exttableservice` auf, die während der Installation auch gleich mitinstalliert werden muss. Auch wenn ich nicht der Fan davon bin für einen Zweck gleich zwei Extensions installieren zu müssen, so finde ich die Idee hinter `th_exttableservice` gar nicht mal so verkehrt. Denn mit ihrer Hilfe werden Tabellen XHTML-konform erzeugt. Soll heißen, dass Attribute wie `colgroup` und `col` nicht erlaubt sind und entfernt werden. Weiterhin werden alle Attribute auf Gültigkeit überprüft.

`aoe_advancedtable` ist leider gar nicht dokumentiert, weshalb ich mir den Quelltext dieser Extension angeschaut habe:

```php
$res['content'] = str_replace('\<', '<', $res['content']);
$res['content'] = str_replace('\^', '^', $res['content']);
$res['content'] = str_replace('\{', '{', $res['content']);
...
// $res landet später in $cellData
...
// ***Check colspan and rowspan******
if ($cellData['colspan']) {	
    $this->_setColspanForTable($col, $row, $table);
} elseif ($cellData['rowspan']) {
    $this->_setRowspanForTable($col, $row, $table);
}
```

Daraus folgt, dass ihr folgende Zeichen in die jeweiligen Tabellenzellen eintragen müsst, damit gewünschter Effekt eintritt:

- `<` = colspan
- `^` = rowspan
- {Attribut=blabla} = Siehe Attribute

### Attribute

Weiter unten im Quellcode habe ich folgenden Abschnitt gefunden:

```php
foreach ($cellData['attributes'] as $k => $v) {
    switch ($k) {
        case 'class':
            $_cellClass .= ' ' . $v;
            break;
        }
        case 'rowClass':
            $_rowClass .= ' ' . $v;
            break;
        case 'cellType':
            if ($v == 'header') {
                $table->setCellType($col, $row, 'th');
            }
            break;
        default:
    }
}
```

Heißt im Klartext: Wir können nicht einfach irgendwelche Attribute setzen, sondern müssen uns von diesen vorhandenen bedienen:

- {class:[meineKlasse]} = Setzt einen beliebigen Klassennamen für die aktuelle Zelle
- {rowClass=[meineKlasse]} = Setzte einen beliebigen Klassennamen für die komplette Tabellenzeile
- {cellType=header} = Ist dieser Wert in einer Zelle gesetzt, dann wird statt <td> ein <th> generiert.

### Das Ergebnis in Bildern

Im ersten Bild oben rechts zeige ich Euch, wie Ihr den Tabelleneditor von TYPO3 befüllen müsst, damit das Ergebnis wie im Bild 2 dabei herauskommt. Im letzten Bild zeige ich euch, wie der generierte HTML-Code der Tabelle ausschaut.
