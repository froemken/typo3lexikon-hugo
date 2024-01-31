+++
title = "TemplaVoila Crashkurs"
date = 2024-01-09T20:53:23+01:00
aliases = ["templvoila-crashkurs.html"]
+++

Immer wieder passiert es mir, dass ich bei der Einrichtung von TemplaVoila etwas vergessen habe. Sei es die Einbindung des `css-styled-content` Template oder der templates-Ordner im `fileadmin`. Damit ist jetzt Schluss. Ich hab hier für mich und natürlich auch für euch einen Crashkurs ala quick and dirty erstellt. Vielleicht hilft es dem ein oder anderen.

## TV importieren/installieren

TemplaVoila wird über den Extension-Manager installiert. Wenn der Hinweis kommt, dass auch die static_info_tables installiert werden müssen, dann bitte den Hinweisen folgen.

Bei einem Neuaufbau einer Webseite ist das "Classic Page Modul" nicht zwingend erforderlich. Dies wird nur bei "Konvertierungen" von einem anderen Templatesystem nach TemplaVoila benötigt.

## Sysfolder anlegen

Im Seitenbaum muss ein Ordner erstellt werden, in dem später die TemplaVoila-Einstellungen und-Templates abgelegt werden können. Ich bennene den immer "TemplaVoila". Dieser Ordner muss in den Seiteneigenschaften der Startseite als "Allgemeine Datensatzsammlung" hinterlegt werden.

## Templatevorbereitungen

Über Template -> Startseite erstellen wir ein neues Template "Create Template for a new site" und lassen dabei die DropDown-Liste leer. Im folgenden DropDown wählen wir oben "Info/Modify" und können nun im Bereich Setup mit folgendem Code TemplaVoila dazu bewegen, den zu erstellenden Inhalt auf der Webseite ausgeben zu lassen:

```typo3_typoscript
page = PAGE
page.10 = USER
page.10.userFunc = tx_templavoila_pi1->main_page
```

### Wichtig

Nach dem Speichern auf "Click here to edit whole template record" klicken und im Tab "Enthält" das Statische Template aus Erweiterungen namens "CSS Styled Content" auszuwählen, weil sonst der Text aus den "Content Elementen" nicht angezeigt wird.

## Fileadmin

Über "Dateiliste" muss im Verzeichnis "fileadmin" ein Ordner mit dem Namen "templates" erstellt werden. Dieser ist für TemplaVoila zwingend erforderlich und muss genau so heißen!

In diesem Verzeichnis sucht TemplaVoila nach Templatedateien. Die Templatedatei für die Startseite bzw. für die gesamte Homepage benenne ich "template.html".

## Templatedatei einbinden

Nachdem ihr die zuvor angelegte Datei "template.html" mit Inhalt gefüllt habt klickt ihr nun auf das Icon dieser Datei und wählt im Kontextmenü den Punkt "TemplaVoila". Ihr landet darauf hin im Mapping von TemplaVoila. Wenn ROOT nicht automatisch auf den body-Tag gemapped wurde, empfehle ich das jetzt zu machen. Ihr könnt nun noch weitere Felder des Templates mappen:

### Beispielablauf

Neues Element -> Feldname: field_content -> Element -> Bezeichnung: Inhalt -> Hinweistext: Füll mich mit Inhalt -> Marker: [CONTENT] -> Art: Content Element -> Letztes Feld leer lassen es sei denn ihr wisst genau welcher Tag im Template durch den neuen Content ersetzt werden soll.

Nach dem "Save as" verwende ich immer einen Namen wie "TV::Main". "Page" auswählen und darauf achten, dass evtl. mehrere Sysfolder angezeigt werden könnten. Wenn JA, dann den zuvor erstellten "TemplaVoila"-Ordner auswählen.

## Startseite bearbeiten

Nach dem Speichern des Mappings müsst ihr nun in die Seiteneigenschaften Eurer Startseite gehen und auf den Tab "Erweitert" klicken. In den beiden DropDown-Felder wählt ihr nun die neue TemplaVoila-Datenstruktur aus.

Seiteneigenschaften speichern -> Neues Content Element anlegen -> z.B. Text -> Überschrift und Text ausfüllen und nach der Speicherung ist der Content auf der Page.
