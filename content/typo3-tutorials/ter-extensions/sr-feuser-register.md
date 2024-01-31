+++
title = "sr_feuser_register"
date = 2024-01-09T20:53:05+01:00
aliases = ["sr-feuser-register.html"]
+++

Hier findet ihr derzeit nur mein überarbeitetes Template der `sr_feuser_register` in Version 2.6.0:

Meine Änderungen:

- Basiert auf dem Originaltemplate der 2.6.0er Version
- Fehlermeldungen erscheinen nicht mehr ÜBER, sondern UNTER den Inputfeldern. In meinem Fall stand links das Label, bündig rechts daneben die Fehlermeldung und dann dadrunter das Inputfeld. Sah einfach nicht aus.
- Das Passwortfeld habe ich überarbeitet. Das Passwort-Wiederholen-Feld hat nun einen eigenen dd/dt-Abschnitt erhalten. Hat den Vorteil, dass man besser mit margin-left arbeiten kann OHNE dass sich alles verschiebt.
- Der Legende-Tag enthält nun immer den gleichen Text und nicht mehr bis zu dreispaltige Anweisungen, wie das Formular auszufüllen ist.
- Direkt unter der Legende habe ich einen Bereich für die Anweisungen des Formulars erstellt. Gewrappt mit p-Tags und der Klasse "notice"
- Alle "sr_feuser_register-error"-Klassen habe ich durch einfache "error"-Klassen ersetzt. Das Blueprint-Framework bietet zum Beispiel 3 Klassen an, um alle Fehler (error), Warnungen (notice) und Bestätigungsmeldungen (success) einheitlich darzustellen.
