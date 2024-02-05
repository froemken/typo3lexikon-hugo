+++
title = "Extbase Repository"
date = 2024-01-09T20:20:53+01:00
aliases = ["extbase-repository.html", "das-repository.html"]
+++

Während es in der alten PiBase-Programmierweise noch ein Datenbankobjekt gab, will man sich bei der Extbaseprogrammierung von dem Datenbankgedanken lösen. So wurde bei Extbase das Repository eingeführt, das man einfach als einen Ort bezeichnet, an dem man etwas dauerhaft ablegen kann. Das muss keine Datenbank sein, das kann auch eine TXT-Datei sein oder XML.

Vom Kopf her könnt Ihr Euch nun frei machen von komplizierten SQL-Queries. Extbase nimmt dem Programmierer diese Arbeit ab und bietet entsprechende Methoden an, um bestimmte, alle oder nur eine Auswahl an Objekten (Datensätze) zurückzuliefern. Auch für das Speichern und Bearbeiten von Objekten (Datensätzen) bietet Extbase entsprechende Methoden an.

Aufgrund von Performance werden alle bearbeiteten und gespeicherten Objekte nicht sofort dauerhaft abgelegt, sondern werden nur temporär im Arbeitsspeicher abgelegt. Erst wenn die Methode zum Speichern komplett durchgelaufen ist, erst dann werden alle temporären Objekte zusammengetragen und in einem Rutsch im Repository (Datenbank) abgelegt.

Für alle, die nicht warten können, bis eine Methode durchgelaufen ist, gibt es die Methode: persistAll() in der Klasse \TYPO3\CMS\Extbase\Persistence\Manager. Diese Methode durchsucht den Arbeitsspeicher nach neuen bzw. bearbeiteten Objekten und schreibt sie dauerhaft ins Repository.
