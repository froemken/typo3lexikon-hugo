+++
title = "Domain Driven Design"
description = "Extbase besitzt durchgehend eine ganz bestimmte Verzeichnisstruktur, die sämtliche Bereiche in einzelne Ordner verfrachtet. So sind Templates im Ordner Resources-&gt;Private zu finden und auch für Flexform und TypoScript gibt es eigene Ordner. Nichts fliegt mehr einfach nur rum oder teilt sich einen gemeinsamen Ordner. Dieser Trennung liegt das Prinzip von DDD (Domain Driven Design) zu grunde."
keywords = "typo3 stefan frömken lindlar oberberg oberbergischer kreis tutorial domain driven design ddd"
date = 2024-02-10T22:26:27+01:00
aliases = ["domain-driven-design.html"]
+++

Ich muss zugeben, dass ich das Buch `Domain-driven-Design` von Eric Evans aus 2003 nie gelesen habe, darum hier meine ganz persönliche Interpretation von Domain Driven Design, wie ich es aus der TYPO3 Extension-Programmierung kennengelernt habe.

Bei Domain Driven Design geht es darum eine komplexe Programmierung in kleinere miteinander interagierende Prozesse zu überführen. Weg von Spaghetti-Code, weg von Alles-in-einer-Datei, hin zu einer sauberen Struktur, hin zu selbsterklärenden PHP Klassen, in denen nur das geschieht, was der Klassen-Name bereits vorgibt.

Ihr wollt nur eine Liste von Mitarbeitern anzeigen lassen? Sorry, dafür braucht ihr kein Domain Driven Design. Nehmt euch nen QueryBuilder, holt euch die Daten, baut ne Schleife in Fluid und fertig. Bei Domain Driven Design geht es darum, in einer komplexen Programmierung den Überblick zu behalten. Eine strikte Struktur und einheitliche Begriffe ermöglichen die schnellere Einarbeitung von Programmierern in die Extension.

Das Wort "Konvention" ist im Rahmen von Domain Driven Design allgegenwärtig. Modelle haben im Rahmen von Extbase in dem Ordner `Classes/Domain/Model/` zu liegen, Repositories in `Classes/Domain/Repository` und Controller in `Classes/Controller`. Die Benennung `ProductController` spiegelt sich im Template-Pfad `Resources/Private/Templates/Product/` wider, ein `CarRepository` erwartet zwingend ein existierendes `Car` Model und ein `Car` Model eine zugrundeliegende Tabelle `tx_myext_domain_model_car`.

Wenn ein Kunde also die Farbe seines Autos geändert haben will, wisst ihr sofort, dass sich diese Eigenschaft in dem Model `Karosserie` befindet, welches Repository für die Datenabfragen zuständig ist, in welcher Tabelle die zugrundeliegende Spalte zu finden ist und in welchem Verzeichnis das Template für die Ausgabe liegt.

Modelle helfen euch dabei den Prozess des Kunden abzubilden. Natürlich kann man ein Model `Schiff` alle möglichen Eigenschaften eines Schiffes reinpacken, aber macht das Sinn? Wohl kaum. Macht eher Sinn mehrere Modelle zu erstellen. Eins für das Schiff an sich, eines für die Container, die es laden kann, eines für den Kapitän, eines für die Route und vielleicht auch Modelle für die Kombüse, Räume, Verträge, anlaufende Häfen und Bordpersonal. Und dann fangt ihr an diese ganzen Modelle zu verknüpfen und es entwickelt sich eine Struktur. Eine Struktur, die euch verdeutlicht, warum das Beladen von 1200 Containern so lange dauert, warum sich nur 3 Personen um das Auswerfen des Ankers kümmern können, warum es 2 Kombüsen gibt und wer dort der Chefkoch ist.

Domain Driven Design erfordert eine einheitliche Sprache (`ubiquitous language`) zwischen Agentur und Kunde. Ich hatte einen Kunden, der sprach mal von Medienpartnern, dann von Kooperationspartnern und dann wieder von "den Zeitungen". Da wirst du irre in der Birne, da er mit allen 3 Begriffen das Gleiche meinte. DDD fordert hier von allen Parteien sich auf eine Sprache; eine spezifische Benennung von Eigenschaften zu einigen. Und genau diese Benennung ist in die Modelle zu überführen. So würde in Bezug auf unser Schiff der Koch als `Smutje` bezeichnet werden.

Die Verknüpfung der Modelle untereinander hilft dabei, Eigenschaften von anderen Modellen abzufragen. Habt ihr gerade das Model `Schiff` zu Hand, dann könnt ihr mit `$schiff->getKombüse()->getSmutjes()` eine Liste von allen angestellten Smutjes abfragen.

Diese Verknüpfungen helfen auch dabei Eigenschaften zu vererben. In einer Extension von mir hatte ich eine Veranstaltung auf versteckt gesetzt. Dadurch, dass die in Relation stehenden Tagesdatensätze jedoch nicht versteckt waren, wurden diese noch angezeigt. Abhilfe schaffen hier die Getter und Setter Methoden in den Modellen. Hier ein Beispiel:

```php
public function setHidden(bool $hidden): void
{
    $this->hidden = $hidden;
    foreach ($this->days as $day) {
        $day->setHidden($hidden);
    }
}
```

Mit diesen Verknüpfungen sowie Gettern und Settern könnt ihr eure Modelle immer in einem konsistenten Status halten. Stellt euch vor mit `setDeleted(true)` würde ich das Model `Schiff` und all seine Verknüpfungen löschen. Fatal. Denn die Verträge und das Personal sollten davon ausgeschlossen bleiben.

Splittet eure Programmierung in kleinere Teilbereiche und schaut, wie ihr diese in die Techniken von Domain Driven Design überführen könnt:

| Technik | Erklärung |
|---------|-----------|
| Factories | Wenn es mal nicht reicht Daten aus der DB einfach 1 zu 1 in die Modelle zu überführen, dann nutzt Factory Klassen. Um ein Flugzeug zu starten braucht es eben nicht nur ein Flugzeug. Es gibt Ablaufpläne wie das Flugzeug vorzubereiten ist. |
| Entities | Modelle, die über eine ID eindeutig zu identifizieren sind: Fahrgestellnummer, Artikelnummer, IBAN, ISBN |
| Value Objects | Modelle, die über all ihre Eigenschaften eindeutig sind. Farbe, Adresse (Straße, Hausnummer, PLZ, Ort) |
| Repository | Stellt euch eine Bibliothek vor, um an Daten dranzukommen. Dabei kann es euch völlig egal sein, woher diese Daten kommen: RestAPI, Datenbank, CSV, XML, JSON |
| Aggregate | Der Einstiegspunkt einer Struktur, um an beliebige Daten innerhalb der Struktur dranzukommen. |
| Services | Denkt an Mailversand, Prozesse protokollieren, ZIP Datei erstellen, Download vorbereiten |

Und das sind nur die Techniken, die mir durch meine Arbeit mit Extbase bekannt sind. Auf [Wikipedia](https://de.wikipedia.org/wiki/Domain-driven_Design) habe ich noch eine ganze Menge weiterer Techniken gefunden, in die ihr eure Programmierung und Klassen einkategorisieren könnt. Aber rechnet nicht damit, dass alle diese Techniken in Extbase adaptiert wurden.
