+++
title = 'Das inline Element'
linkTitle = 'Inline Elemente'
date = 2024-01-09T20:43:21+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["inline-elemente.html"]
+++

Ich weiß es jetzt nicht genau, aber ich glaube es war 2007 als das Inline-Element in den Core mit aufgenommen wurde. Seit dem steht es neben group, select, text und Anderen zur Verfügung. Mit dem inline-Element können zwei Tabellen mit einander verbunden werden. "Dafür gibt es doch die database-relation?!" Da habt Ihr Recht, aber es gibt dabei ein Problem:

Stellt Euch vor Ihr müsst einen Haufen an Daten in TYPO3 eintragen. Nehmen wir mal Computer mit den ganzen Eigenschaften wie GHz, Festplatte, Hersteller, CPU, Nummer, Seriennummer, Büro, blablabla...da werden wir schnell mal auf ein paar Eigenschaften zusammen bekommen. Du hast die ersten 30 Eigenschaften gefüllt und kommst nun zu dem Feld "Büro". Hier hat Dir der Admin eine schöne Selectbox gemacht mit ein paar Bürobezeichnungen. Nun stellst Du fest, dass das Büro, dass Du benötigst nicht vorhanden ist. Wenn Du aus dem Fenster rausgehst, dann verschwinden Deine zuvor eingetragenen 30 Eigenschaften und du müsstest sie erneut eintragen. TYPO3 bietet auch die Möglichkeit an mit Hilfe von so einem fetten PLUS-Zeichen eigene Büros einzutragen, aber kaum draufgeklickt, erscheint eine Meldung, dass die zuvor eingetragenen Daten dann gelöscht werden.

Na super...einziger Ausweg. Du wählst einfach irgendein Büro aus, speicherst deinen Datensatz ab, wechselst in die andere Tabelle, erstellst dort das neue Büro, wechselst zurück in die computer Tabelle, suchst deinen computer und kannst nun endlich den Computer diesen neuen Büro zuweisen.

Es kann sogar noch schlimmer kommen: Denn wenn in der computer-Tabelle benötigte Felder (required) vorhanden sind, dann musst du all diese erst mal füllen bevor Du speichern kannst.

Um genau dieses Durcheinander zu lösen hat Oliver Hader die Möglichkeit von IRRE entwickelt.
