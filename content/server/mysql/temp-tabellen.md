+++
title = "Temporäre Tabellen"
date = 2024-01-09T21:01:26+01:00
slug = "temporaere-tabellen"
aliases = ["temporaere-tabellen.html"]
+++

Ich hoffe, ihr habt meinen Beitrag Indizes durchgelesen, denn dort beschreibe ich wie Ihr die Erstellung von temporären Tabellen grundsätzlich vermeiden könnt. Das ist erstes schneller und zweitens braucht Ihr dann den Wert für temporäre Tabellen nicht so hoch anzusetzen. Daraus folgt: Mehr Arbeitsspeicher, den Ihr für wichtigere Optionen verwenden könnt.

Wenn Ihr dennoch ein GROUP BY oder ähnliches ausführen müsst, das temporäre Tabellen erzeugt, dann werden diese Tabellen zuallererst im Arbeitsspeicher erstellt. Erst wenn der kleinere Wert der Optionen tmp_tables_size oder max_heap_table_size erreicht wurde, dann erst wird die Tabelle auf die Festplatte kopiert. Ab dann wirds richtig langsam. Um herauszufinden, wie viele temporären Tabellen MySQL bereits erstellt hat, gibt es zwei Statuswerte, die Ihr abfragen könnt:

**Created_tmp_tables**: Anzahl erstellter temporärer Tabellen seit Start des Servers. Die tatsächliche Anzahl temporärer Tabellen im Arbeitsspeicher ist natürlich Created_tmp_tables - Created_tmp_disk_tables.

**Created_tmp_disk_tables**: Anzahl erstellter temporärer Tabellen seit Start des Servers, die den Wert von tmp_tables_size oder max_heap_table_size überschritten haben und somit auf der Festplatte ausgelagert werden mussten. Ich denke, es sollte klar sein, diesen Wert möglichst bei 0 zu halten. Es ist bestimmt kein Problem, wenn der Wert mal auf 5 oder sogar 10 steigt, aber ein Wert von über 50 würde mir dann doch schon zu denken geben.

Der Unterschied zwischen tmp_tables_size und max_heap_table_size liegt darin, dass max_heap_table_size zusätzlich das Anwachsen von Benutzern erstellte temporäre Tabellen beschränken kann.
