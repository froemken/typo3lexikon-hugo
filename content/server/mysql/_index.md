+++
title = "MySQL Performance in TYPO3"
linkTitle = "MySQL"
date = 2024-01-09T21:01:02+01:00
alwaysopen = false
aliases = ["mysql.html"]
+++

Was ein schönes und gleichzeitig leidiges Thema. Zum einen schön, weil man nahezu alles selbst konfigurieren und sich über jeden noch so kleinen Erfolg freuen kann und zum Leidigen, weil es nirgends eine Doku gibt, die sagt: "Mach das und MySQL rennt wie die Sau". Klar, wir alle kennen die Option key_buffer. "Setz den Wert auf 25 % des Arbeitsspeichers oder höher, aber nicht über 50 %, weil sonst das Betriebssystem Probleme macht." heißt es. Aber was ist das eigentlich? Was ist ein guter Wert für query_cache_size? Im Internet findet man Zahlen zwischen 1 und 128M. Toller Standardwert, aber was soll ich denn jetzt nehmen? 1M, 16M, 128M? und schon fängt man wieder von vorne an.

Auch ich werde Euch keine Standardwerte nennen können, da diese ganz individuell für jeden Server und Anwendung angepasst werden müssen. Warum? Hier ein paar Beispiele bzw. Fragen, die Ihr Euch stellen solltet: Habt Ihr mehr lesende (SELECT) oder schreibende (INSERT/UPDATE) Abfragen. Ist die Ergebnismenge Eurer SELECT-Abfrage eher klein oder groß? Habt Ihr viele Abfragen oder nur ein paar vereinzelte? Wenn Ihr viele Abfragen habt, wie viele davon sind sich sehr ähnlich? Arbeitet Ihr viel mit GROUP BY und ORDER BY?

Für all die Antworten dieser Fragen gibt es völlig unterschiedliche Möglichkeiten den Server zu konfigurieren. Bei einer Webseite mit nur ein paar Abfragen macht es keinen Sinn den Querycache auf 128M zu setzen und genauso macht es keinen Sinn den key_buffer bei 8 GB Arbeitsspeicher auf 25 % zu setzen, wenn sich auf der abzufragenden Tabelle kein Index befindet oder die Datenmenge noch überschaubar ist.
