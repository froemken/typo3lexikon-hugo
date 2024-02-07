+++
title = "TemplaVoila 1.4.1"
date = 2024-01-09T20:53:23+01:00
aliases = ["templavoila-141.html"]
+++

Ich war schlicht weg begeistert als ich heute die neue Version von TemplaVoila installiert habe. Hier also ein paar Bilder, wie das neue TemplaVoila ausschaut.

Zuallererst ist mir die neue rote Struktur auf der linken Seite eines jeden Elements aufgefallen. Unter `Configuration` sind alle Felder beibehalten worden, wie wir sie auch aus den früheren Versionen kennen. Allerdings haben wir nun eine Textarea für Beispieltext zur Verfügung gestellt bekommen, was das Editieren von längeren Texten extremst vereinfacht. Auch die Namen in der DropDown-Box von "Element preset" haben sich geändert, sind neu strukturiert worden und ein Preset für die Textarea (von ursprünglich 3) ist rausgeflogen.

Auf der rechten Seite wurden wir mit völlig überarbeiteten Icons für die HTML-Tags beschenkt. Sie sind größer, schicker und fallen sofort ins Auge.

## Data Processing

Während wir damals noch mit "local processing" arbeiten mussten, damit unser TypoScript im Haupt-XML nicht verloren geht, können wir nun das TypoScript im Haupt-XML selbst ändern. Local processing ist zwar immer noch vorhanden, aber wird wohl in Zukunft immer weniger gebraucht. Evtl. ist es sogar nur noch wegen Kompatibilitätsgründen vorhanden.

Hier nun ein Bild, wie ihr das data processing verwenden könnt. Die Konstanten werden nicht mit `KONSTANTE=Wert`, sondern als XML deklariert: `<KONSTANTE>Wert</KONSTANTE>`

## Extra

In meinem Beispiel habe ich mich für eine einfache Textbox entschieden. Die Entwickler haben anscheinend sehr viel auf Sicherheit gelegt, denn der Haken für HSC lässt sich zwar entfernen, aber nach einem Klick auf "Update" ist er wieder da. Auch wenn man den XML-Bereich für HSC löscht, so ist dieser nach dem Bearbeiten der Struktur wieder da.

Das Feld für `stdWrap` ist auch neu und selbst als Eigenschaft in der XML-Datenstruktur war mir das völlig neu. Auf jeden Fall könnt ihr Euren Content nun sehr einfach über diese Textbox, wie im Bild, anpassen.

## Form

Die meisten Felder werden in den Seiteneigenschaften einer jeden Seite gefüllt. Hier könnt ihr nun konfigurieren, wie und welche Eigenschaften das Feld haben soll. Mein einfaches Textfeld soll nicht gewrapped werden (siehe "Form extras"). Dieses Feld spiegelt den Befehl "defaultExtras" wieder. Ich hab auch schon mit den Eigenschaften für den RTE rumgespielt, aber diese Eigenschaften werden nicht sauber übernommen. Einen Bugreport habe ich schon online gestellt.
