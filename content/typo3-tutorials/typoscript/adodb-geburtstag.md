+++
title = "Geburtstag berechnen"
date = 2024-01-09T20:58:10+01:00
aliases = ["adodb-geburtstag-berechnen.html"]
+++

Die Extension sr_feuser_register arbeitet mit einer Klasse "adodb_date". Dank dieser Klasse können auch Datumswerte kleiner 1970 berechnet werden. Problem bei der Sache: Es gibt keine TypoScript-Funktion, um aus diesem TimeStamp wieder ein Datum zu machen.

Ich hab mich 3 Stunden hingesetzt und hab folgende Lösung:

Ladet Euch folgende Datei herunter.Bindet diese Datei in Euer TypoScript ein:

```typo3_typoscript
page.includeLibs.adodb_time = fileadmin/adodb_time.php
10 = USER_INT
10.userFunc = user_adodb_time->adodb_date
10.datum.data = field:date_of_birth
10.format = Y
20 = USER_INT
20.userFunc = user_adodb_time->adodb_date
20.datum = 255375600
20.format = d.m.Y
```

datum.data klappt nur dank meiner Datei, da ich dort gesagt habe, dass die Variable datum als stdWrap interpretiert werden soll. format ist die Wiedergabeart, diese d m Y-Variablen halt. Derzeit klappt nur die Funktion adodb_date. Zum reinen Anzeigen sollte es aber reichen.
