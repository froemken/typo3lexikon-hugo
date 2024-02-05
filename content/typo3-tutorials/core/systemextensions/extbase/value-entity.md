+++
title = "Value und Entity"
date = 2024-01-09T20:21:56+01:00
aliases = ["value-object-und-entity.html"]
+++

Evtl. kann ich hier sagen "Willkommen im Club". Ich habe den Unterschied selbst eine halbe Ewigkeit nicht begriffen und kann nur hoffen, dass ich das jetzt richtig rüberbringe.

## Value Object

Value Objects sind Objekte, die keine eindeutige Identifizierung haben. Farben um Beispiel. Sie werden nicht eindeutig indentifiziert. Wenn ich mal eine neue Farbe brauche oder eine Farbe ändere, wird einfach eine neue Farbe im Repository angelegt. Um das mal datenbanktechnisch auszudrücken: Denkt mal an die Schlagworte/Stichwörter, die man z.B. in einem Blog oder Forum zuweisen kann. Es ist äußerst unwahrscheinlich, dass ihr eine Detailansicht für eine Farbe oder halt diesen Schlagwörtern im Frontend erstellt.

In dem Buch von Jochen Rau und Sebastian Kurfürst steht allerdings sowas wie: "Es kommt immer auf den Anwendungsfall an." Soll z.B. heißen: Wenn ihr mit Farben arbeitet bzw. verkauft, dann kann eine eindeutige Identifizierung allerdings sinnvoll sein.

## Entity

Entities sind Objekte mit einer eindeutigen Identifizierung. Sowas kennt ihr von Eurem Personalausweis. Dort gibt es eine Nummer, über die ihr eindeutig identifiziert werden könnt. Unternehmen (Umsatzsteuer-ID), PCs (Seriennummer), Getränke (Datum + Werk + Maschinennummer). Um Euch etwas zu beruhigen: In den meisten Fällen werdet ihr bei der Extbaseprogrammierung mit Entities arbeiten.

## Services als Assoziation

Services sind Aktionen zwischen Value Object und/oder Entities. Ich versuche mich mal an ein selbst ausgedachtes Beispiel:

Wir haben ein Modell "Absender", der einen Brief schreiben, eine Briefmarke draufkleben und den Brief in den Briefumschlag stecken kann. Parallel dazu haben wir ein Modell "Empfänger", der den Brief wieder öffnen und lesen kann. Allerdings fehlt uns noch der eigentliche Transport des Briefes, der durch das Objekt "Post" realisiert wird. Die Post gehört zu unserem Domänenmodell aber sie gehört weder zu dem "Empfänger" noch zum "Absender". Klar kann man programmtechnisch einfach eine Methode sendeAnEmpfaenger($empfaenger) in das Objekt "Absender" einbauen, aber diese Methode gilt als unsauber. Für solche Problemfälle wurden die Services eingeführt.
