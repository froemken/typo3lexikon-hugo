+++
title = "No template found"
date = 2024-01-09T20:44:56+01:00
aliases = ["no-template-found.html"]
+++

Ich habe jetzt schon mehrere Projekte mit TemplaVoila gemacht und auf einmal erscheint bei einem Projekt auf einem Hosteurope-Server die Meldung: no Template found.

Es ist aber ein Template definiert und wenn ich den Cache lösche, dann kann ich die Seite auch EINMAL aufrufen, aber wehe ich klicke auf einen Link der Navigation, dann erscheint sofort wieder "no Template found".

Ich hab ne ganze Weile suchen müssen:
Im Installtool verwende ich bei setDBinit immer folgenden 3-Zeiler:

```sql
SET NAMES utf8;
SET CHARACTER SET utf8;
SET SESSION character_set_server=utf8;
```

Das hat immer funktioniert, aber HostEurope verwendet wohl eine andere MySQL-Version als wie ich sie in meinen anderen Projekten verwendet habe. Denn hier muss die 2te Zeile entfernt werden. In setDBinit steht also:

```sql
SET NAMES utf8;
SET SESSION character_set_server=utf8;
```

Einmal Save und Clear Cache und alles ist wieder in Ordnung.
