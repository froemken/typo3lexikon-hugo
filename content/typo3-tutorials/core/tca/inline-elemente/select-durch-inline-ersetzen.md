+++
title = 'Select durch Inline ersetzen'
date = 2024-01-09T20:43:33+01:00
aliases = ["select-durch-inline-ersetzen.html"]
+++

In meinem ersten Beispiel müssen wir erst mal eine Extension erstellen, um die jeweiligen Tabellen zu erstellen. Wir benötigen eine Tabelle "office" und eine Tabelle "computer". Füllt die Tabelle "office" schon mal mit dem Inhalt, wie ich ihn unten angegeben habe. Die Tabelle "computer" bleibt erstmal leer. "officeid" legt Ihr bitte als "database relation" zu der Tabelle "office" an.

Tabelle: office

| raumnr | raumname |
|--------|----------|
| 101    | Empfang  |
| 102    | EDV      |
| 103    | Vertrieb |
| 104    | Technik  |
| 405    | Flur     |

Tabelle: computer

| pcnr | officeid |
|------|----------|

Das Ergebnis ist eine Selectbox

Ich arbeite gerne mit der Extension: quixplorer. Damit könnt Ihr sehr einfach in das Extensionverzeichnis: typo3conf/ext/ wechseln und Euch die Datei tca.php mal genauer anschauen. Die database-relation von "officeid" sollte ähnlich dem hier entsprechen:

```php
'officeid' => array (        
    'exclude' => 0,        
    'label' => 'LLL:EXT:sfinline/locallang_db.xml:tx_sfinline_computer.officeid',        
    'config' => array (
        'type' => 'select',    
        'foreign_table' => 'tx_sfinline_office',    
        'foreign_table_where' => 'ORDER BY tx_sfinline_office.uid',    
        'size' => 1,    
        'minitems' => 0,
        'maxitems' => 1,
    )
)
```

## Das erste Inline-Element

Das Script von oben ändern wir nun folgendermaßen ab:

```php
'officeid' => array (        
    'exclude' => 0,        
    'label' => 'LLL:EXT:sfinline/locallang_db.xml:tx_sfinline_computer.officeid',        
    'config' => array (
        'type' => 'inline',    
        'foreign_table' => 'tx_sfinline_office',    
        'minitems' => 0,
        'maxitems' => 1,
    )
)
```

Wie Ihr seht wurde nicht sehr viel geändert. Die type-Eigenschaft muss auf "inline" gesetzt werden und die beiden Eigenschaften "size" und "foreign_table_where" sind komplett rausgeflogen. Das Ergebnis seht Ihr im Bild.

Dieses Beispiel soll zeigen, wie einfach es ist, von select auf inline zu wechseln. Aber ich gebe zu bedenken, dass sich Datenbanktechnisch einiges geändert hat. Denn in einem Büro können ja auch durchaus mehrere Computer stehen und so müsst Ihr bei dieser Lösung für jeden einzelnen Computer einen weiteren Eintrag für das Büro erstellen. Heißt: Im Büro stehen 5 PCs also musste das Büro auch 5mal angelegt werden. Sowas KANN gewollt sein, wenn Ihr z.B. Maßangaben verwendet. Dann macht es wenig Sinn eine endlos lange Selectbox mit allen bisher eingetragenen Maßen zu erstellen, oder habt Ihr Lust aus 1000 Maßangaben wie, 100cm, 101cm, 123cm, 124cm...die richtige auszuwählen? Ich denke nicht.

Für unsere 5-6 Büros möchte ich aber aus den vorgegebenen Datensätze der office-Tabelle auswählen können.
