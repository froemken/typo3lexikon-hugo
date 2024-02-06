+++
title = "HTMLParser"
date = 2024-01-09T20:32:19+01:00
aliases = ["htmlparser.html"]
+++

HTMLparser
> Der HTMLparser kommt immer dann in Frage, wenn ein HTML-Tag oder ein Attribut eines HTML-Tags geändert, gelöscht oder hinzugefügt werden soll. Solche Änderungen finden auch innerhalb der Transformations auf Basis des HTMLparsers statt.

Das proc-Objekt bietet auch Euch die Verwendung des HTMLparsers an, um VOR und NACH einer Transformation noch ein paar Änderungen am Quellcode vorzunehmen.

Folgende 4 HTMLparser sind für Euch relevant:

entryHTMLparser_db
> Ist  für den Weg vom RTE zur Transformation zuständig. Soll der Inhalt des RTEs also VOR der Verarbeitung durch eine Transformation bearbeitet werden, dann seit Ihr mit diesem HTMLparser an der richtigen Adresse.

entryHTMLparser_rte
> Ist für den Weg von der Datenbank zu einer Transformation zuständig. Soll der Inhalt von der Datenbank VOR der Verarbeitung durch eine Transformation bearbeitet werden, dann seit Ihr mit diesem HTMLparser an der richtigen Adresse.

exitHTMLparser_db
> Ist für den Weg von einer Transformation zur Datenbank zuständig. Soll der Inhalt des RTEs also NACH der Verarbeitung durch eine Transformation bearbeitet werden, dann seit Ihr mit diesem HTMLparser an der richtigen Adresse.

exitHTMLparser_rte
> Ist für den Weg von einer Transformation zum RTE zuständig. Soll der Inhalt von der Datenbank NACH der Verarbeitung durch eine Transformation bearbeitet werden, dann seit Ihr mit diesem HTMLparser an der richtigen Adresse.

## 2 weitere HTMLparser

Wer das proc-Objekt noch weiter studiert hat, wird wissen, dass es noch zwei weitere HTMLparser gibt. Bevor ich diese jedoch aufliste ein paar Hintergrundinformationen dazu:

Die Dokumentation bezüglich dieser beiden HTMLparser hält sich auch im Internet sehr bedeckt. Auf der einen Seite heißt es, diese Eigenschaft ist eine Referenz auf das HTMLparser-Objekt, also können auch alle Eigenschaften des Objektes genutzt werden und auf der anderen Seite wundert Ihr Euch, dass nur die Hälfte der HTMLparser-Eigenschaften nicht funktionieren.

Um diese beiden HTMLparser zu verstehen musste ich mich stundenlang mit dem Quellcode beschäftigen. Die folgende Erklärung wie diese beiden HTMLparser arbeiten ist im Internet nicht dokumentiert und nur eine ganz persönliche Schlussfolgerung meinerseits:

Persönliche Erfahrung über die Funktionsweise von HTMLparser_db/rte

Die beiden HTMLparser werden NICHT wie die anderen 4 HTMLparser VOR oder NACH der Transformation, sondern noch WÄHREND der Transformation ausgeführt.

Wichtiger Hinweis: Die beiden HTMLparser können innerhalb der Transformation nur so viel ändern, wie die eingestellte Transformation (überwiegend: ts_css) zu lässt.

Wie Ihr mit Sicherheit schon wisst, ist ts_css eine Gruppierung von mehreren Transformations. Unteranderem gehört auch die Transformation css_transform dazu. Schau Euch mal auf meiner Seite an, was diese Transformation macht und Ihr werden feststellen, dass sämtliche Blockstile unberührt in die Datenbank portiert werden.

Fazit: Da die Transformation css_transform keinen Zugriff auf Blockstile hat, hat auch der entsprechende HTMLparser keinen Zugriff darauf.

Folgende Blockstile sind von der Bearbeitung ausgeschlossen:

```
PRE,UL,OL,H1,H2,H3,H4,H5,H6,HR,ADDRESS,DL,DD
```

Auf dem Weg zur Datenbank kommen noch diese hinzu:

`TABLE,BLOCKQUOTE`

und wenn Ihr die Eigenschaft preserveDIVSections nutzt, wird die Liste um das DIV-Tag vergrößert.

Folgende zwei HTMLparser stehen im proc-Objekt derzeit zusätzlich zur Verfügung:

HTMLparser_db
> Dieser HTMLparser wird während der Transformation ausgeführt und bereitet den Inhalt des RTEs für die Datenbank vor. Wenn dieses Objekt verwendet wird, dann meistens mit der Eigenschaft xhtml_cleaning=1.

HTMLparser_rte
> Dieser HTMLparser wird während der Transformation ausgeführt und bereitet den Inhalt der Datenbank für den RTE vor. Es stehen nicht alle Eigenschaften des HTMLparser-Objektes zur Verfügung.

