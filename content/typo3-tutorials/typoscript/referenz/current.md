+++
title = "current"
date = 2024-01-09T20:58:10+01:00
aliases = ["current.html"]
+++

Auf vielen Webseiten findet Ihr oftmals folgendes Snippet

```typo3_typoscript
10 = TEXT
10.current = 1
```

und schon wird irgendein Text ausgegeben oder manchmal auch einfach gar nichts.

## Was hat es mit dem current auf sich?

Ich habe mir dazu mal in der Datei ContentObjectRenderer.php die Eigenschaft current etwas näher angeschaut und folgende Methode gefunden:

```php
public function stdWrap_current($content = '', $conf = []) {
    return $this->data[$this->currentValKey];
}
```

Das $this->data Array wird meistens mit den Felder des aktuellen Kontextes gefüllt. Soll heißen: Wenn sich das TypoScript auf einer Seite befindet (Modul: Template), dann enthält $this->data alle Felder der aktuellen Seite (Tabelle: pages). Wird das TypoScript innerhalb eines Inhaltselementes ausgeführt, wird $this->data mit den Feldern aus der Tabelle tt_content gefüllt. Mit der stdWrap-Eigenschaft field könnt Ihr diese kontextbezogenen Felder direkt auslesen. field=title im TypoScript würde z.B. $this->data['title'] ausführen und somit den Seitentitel ausgeben.

Innerhalb von diesem $this->data Array gibt es jedoch einen ganz besonderen Wert, der unabhängig von den kontextbezogenen Feldern der anderen Tabellen ist. Damit sich dieser vom Namen her nicht mit den Feldern aus den anderen Tabellen beißt und möglicherweise irgendwas überschreibt, erhält dieses Spezielfeld den eigenartigen Namen currentValue_kidjls9dksoje. Kein Mensch muss sich diesen Namen merken. Wichtig ist nur, es gibt da ein spezielles Feld, wo, wer auch immer, etwas reinschreiben kann. Das kann eine Extension sein, das kann verschachteltes TypoScript sein, das können andere TS-Eigenschaften wie split sein, das kannst auch Du selbst gewesen sein mit setCurrent. Ihr merkt schon, es kommt ein bisschen auf das Umfeld und den Extension-Entwickler an, ob dieses current gefüllt ist oder nicht.

Ich habe für Euch im Folgenden mal ein paar Beispiele auf Basis der split-Eigenschaft aufgebaut. Ihr könnt die Beispiele in Eurem Haupttemplate nachbauen:

```typo3_typoscript
page.123 = TEXT
page.123 {
  value = blau,grün,weiß,lila
  split {
    token = ,
    cObjNum = 1
    1.10 = TEXT
    1.10.current = 1
    1.20 = TEXT
    1.20.current = 1
    1.wrap = <b>|</b><br />
  }
}
```

Split geht in diesem Beispiel her und ließt jedes einzelne Element aus dem value aus und stellt es Dank diesem $this->data[] Array allen untergeordneten Objekten (1.10 = TEXT und 1.20 = TEXT) zur Verfügung. Ihr könnt in diesen untergeordneten Objekte nun selbst entscheiden, ob diese mit Ihrer value-Eigenschaft einen eigenen Wert erhalten sollen, oder ob sie mittels current=1 auf den Wert des Elternelements zugreifen sollen. Probierts mal aus und Ihr werdet folgendes Ergebnis erhalten:

```html
blaublau
grüngrün
weißweiß
lilalila
```

## Value statt current

```typo3_typoscript
page.123 = TEXT
page.123 {
  value = blau,grün,weiß,lila
  split {
    token = ,
    cObjNum = 1
    1.10 = TEXT
    1.10.current = 1
    1.20 = TEXT
    1.20.value = Hallo zusammen
    1.wrap = <b>|</b><br />
  }
}
```

ergibt:

```html
blauHallo zusammen
grünHallo zusammen
weißHallo zusammen
lilaHallo zusammen
```

## Current und Value gehören nicht zusammen

```typo3_typoscript
page.123 = TEXT
page.123 {
  value = blau,grün,weiß,lila
  split {
    token = ,
    cObjNum = 1
    1.10 = TEXT
    1.10.current = 1
    1.20 = TEXT
    1.20.value = Hallo zusammen
    1.20.current = 1
    1.wrap = <b>|</b><br />
  }
}
```

Sobald current=1 gesetzt ist, wird der per value übergebene Wert mit dem Wert vom Elternelement überschrieben. Auch wenn das Elternelement nichts zurück gibt, wird value überschrieben. In solchen Fällen bitte die TS-Eigenschaft override verwenden.

Das Ergebnis dieser Funktion:

```html
blaublau
grüngrün
weißweiß
lilalila
```

## Current und setContentToCurrent

```typo3_typoscript
page.123 = TEXT
page.123 {
  value = blau,grün,weiß,lila
  split {
    token = ,
    cObjNum = 1
    1.10 = TEXT
    1.10.value = Trallala
    1.10.setContentToCurrent = 1
    1.10.current = 1
    1.20 = TEXT
    1.20.current = 1
    1.wrap = <b>|</b><br />
  }
}
```

Mit setContentToCurrent können wir nun den vom Elternelement zur Verfügung gestellten Wert überschreiben. Diese Änderung wirkt sich dann wiederum auf alle anderen untergeordneten Elemente aus. Deshalb ergibt dieses TS-Konstrukt folgende Ausgabe:

```html
TrallalaTrallala
TrallalaTrallala
TrallalaTrallala
TrallalaTrallala
```

## setCurrent als Abkürzung

```typo3_typoscript
page.123 = TEXT
page.123 {
  value = blau,grün,weiß,lila
  split {
    token = ,
    cObjNum = 1
    1.10 = TEXT
    1.10.setCurrent = Trallala
    1.10.current = 1
    1.20 = TEXT
    1.20.current = 1
    1.wrap = <b>|</b><br />
  }
}
```

Hier das gleiche Beispiel wie oben, nur ohne value und statt setContentToCurrent habe ich diesmal setCurrent verwendet. Das Ergebnis ist das Gleiche. Schön ist auch das setCurrent vom Typ stdWrap ist und somit weitere Modifikationen zur Verfügung stehen.
