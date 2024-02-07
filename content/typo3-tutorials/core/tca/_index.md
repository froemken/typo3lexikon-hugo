+++
title = "TCA"
date = 2024-01-09T20:11:17+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["tca.html"]
+++

## Die unendlichen Weiten der TCA

class.t3lib_tcemain.php

Diese Datei beinhaltet die TYPO3 Core Engine (Kurz: TCE). Zum größten Teil kümmern sich die Methoden in dieser Datei, um das Schreiben und Lesen von Daten in und aus Datenbanktabellen, die in der TCA definiert sind. Hinzu kommen Methoden, die sich um das Kopieren, Löschen, Verschieben und die Versionierung von Daten kümmern.

## TCA

Da ich gerade die TCA erwähnt habe: Bei der TCA handelt es sich um das Table Configuration Array. Ich sage immer, die TCA ist ein Abbild der Datenbank. Das ist vielleicht nicht ganz richtig, aber jede Tabelle, die in der Datenbank vorhanden ist aber nicht in der TCA definiert wurde erscheint auch nicht im Listenmodul von TYPO3. Eine Tabellenspalte, die nicht in der TCA definiert wurde, kann im TYPO3-Backend nicht gefüllt bzw. editiert werden. Vielleicht ist die TCA auch eine Schnittstelle zwischen TYPO3 und der Datenbank? Wohl eher nicht, denn Daten können damit nicht transportiert werden. Ich denke, wenn ich sage, dass die TCA eine für TYPO3 verständliche Beschreibung der Datenbank ist, dann wird das wohl schon eher zutreffen. Denn mit der TCA kann man TYPO3 z.B. anweisen eine Tabelle nicht anzeigen zu lassen. Ich kann TYPO3 auch auffordern ein Textfeld in der Datenbank als RTE, Textarea, Dateipfad oder Eingabefeld ausgeben zu lassen.
