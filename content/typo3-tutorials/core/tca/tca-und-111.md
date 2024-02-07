+++
title = "TCA und diese 1-1-1"
date = 2024-01-09T20:41:56+01:00
aliases = ["tca-und-diese-1-1-1.html"]
+++

Jeder, der schon mal in der `ext_tables.php` einer jeweiligen Extension reingeschaut hat, wird diese Textpassagen kennen:

```php
'types' => Array ('0' => Array('showitem' => '--div--;Name;;;1-1-1'),
```

Aber was heißt das im Einzelnen?

Zu allererst bedeutet showitem welches Feld angezeigt werden soll. Alle nicht angegebenen Felder werden ausgeblendet. Die Felder, die angezeigt werden sollen, sind mit einem Komma zu trennen:

```php
'types' => Array ('0' => Array('showitem' => 'vorname,name,strasse,ort,tel'),
```

Jedes dieser Felder kann 5 Teilbereiche aufgesplittet werden. Der Trenner hier ist ein Semikolon. Hier die Bedeutung der einzelnen Teilbereiche:

Feldname: Hier muss der exakte Feldname angegeben werden, wie er auch in der jeweiligen Tabelle zu finden ist. Als Alternative kann hier auch der Wert --div-- eingetragen werden, was einen weiteren Tab in die Flexform einfügt. Der Name diesen Tabs muss dann in die alternative Feldbezeichnung eingetragen werden:

```php
'0' => Array('showitem' => '--div--;Name;;;1-1-1, naechstesFeld'
```

Damit das mit diesen Tabs auch funktioniert muss die dividers2tabs-Option aktiviert werden:

```php
$TCA["tx_mininews_news"] = Array (
    "ctrl" => Array (
        "dividers2tabs" => 1,
    ),
);
```

Mit "newline" im Bereich der Paletten kann definiert werden, das der angegebene Tab in einer 2ten/weiteren Zeile angezeigt wird.

```php
--div--;Your Label2;newline,field3,field4
```

Alternative Feldbezeichnung: Hier kann eine alternative Feldbezeichnung angegeben werden, als im TCA deklariert.

Paletten ID: Auf Paletten können für ein Feld weitere Subfelder angezeigt werden. So kann man z.B. ein Feld "Name" erstellen (Keine Trennung zwischen Vor- und Nachname). In diesem Feld gäbe es dann einen Button, um eine Palette anzuzeigen, in der ich dann Felder für Vorname, Nachname, Titel und Beruf definieren kann, um präzisere Angaben durchzuführen. Solche Paletten müssen in der ext_tables.php erstellt werden und besitzen eine eindeutige ID, die hier angegeben werden kann.

Spezielle Konfiguration: Mehrere Konfigurationen werden hier mit einem Doppelpunkt getrennt. Konfigurationen können z.B. sein, das Tabs in einer Textarea erlaubt sind, oder das Attribut "nowrap" hinzugefügt wird.

## Weitere Informationen

Formular CSS: Hier wird's bunt, denn hier können verschiedene Styles für Felder angegeben werden. Die 3 verschiedenen Styles werden mit einem Bindestrich getrennt.

Der erste Wert steht für Farbschemata. Hier können fünf verschiedene Farben für Hintergrund und Textfarbe hinterlegt werden.

Der zweite Wert steht für Style. Hier kann für jede "Art" (text, input, check, ...) eine eigene Styleangabe hinterlegt werden.

Der letzte Wert steht für Rahmenangaben (Umrandungen). Allerdings klappt das nur bei einer Gruppe von Feldern, wie in den Paletten, denn diese Einstellung benötigt eine Tabelle um die Styles umsetzen zu können. Eine Richtlinie sollte leidlich eingehalten werden, wobei die "0" für "default" steht.

## Erweitert: Für speziellen Inhalt

Beispiel:

Folgende Angaben müssen in der ext_tables.php einer jeweiligen Extension gemacht werden.

```php
$TBE_STYLES['colorschemes'][0] = 'red,yellow,blue,olive,green';
$TBE_STYLES['styleschemes'][1]['all'] = 'background-color:#226699;';
...
'types' => array(
'0' => array(
'showitem' => '--div--;Name;;;0-1-3'
),
)
...
```

Colorschemes benötigt fünf Farbangaben, die mit Kommas getrennt werden:

Hintergrund der kompletten Zeile
Hintergrund der Überschrift
Hintergrund der Palettenüberschrift
Schriftfarbe der Überschrift
Schriftfarbe der Paletten-Überschrift
Die "0" hinter colorschemes gibt an welchen Stil wir verändern wollen. Oben habe ich schon angegeben, dass es 6 verschiedene Stile gibt (0-5), wobei 0 für default steht.

Mit "all" bei styleschemes kann ich angeben, dass ich alle Felder, die im TCA möglich sind ansprechen kann. Somit bedeutet diese Zeile, dass alle Felder einen dunkleren Hintergrund erhalten werden.
