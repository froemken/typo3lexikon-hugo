+++
title = "Aggregate Root"
date = 2024-01-09T20:21:11+01:00
aliases = ["aggregate-root.html"]
+++

In den Extbase-Büchern werden die Aggregate Roots immer als Einheit von Objekten beschrieben. Soll heißen: Wenn ich ein Auto habe, dann hat dieses Auto natürlich auch Reifen, ein Lenkrad und Türen. Alles zusammen ergibt das Objekt Auto, das als Aggregate Root definiert werden kann. Von der Theorie her bedeutet das, dass man die Objekte Tür, Lenkrad und Reifen nur noch über das Objekt Auto erreichen kann.

Auch der extbase_kickstarter kann Objekte mit dem Flag Aggregate Root kennzeichnen. Hier erkennt man allerdings sehr schnell, dass der extbase_kickstarter für diese Objekte auch gleich einen Controller und einen View einrichtet im Gegensatz zu Objekten ohne Aggregate Root.

Ich persönlich kann mir unter dieser Theorie nur wenig vorstellen, deshalb eine etwas einfachere Beschreibung von mir: Jedes Objekt, dass Ihr DIREKT anzeigen lassen wollt, bei dem sollte das Flag Aggregate Root gesetzt werden, denn ohne Controller und View ist das schlicht nicht bzw. nur schwer möglich.

Nehmen wir noch mal unser Auto: Ich will das Auto XY anzeigen lassen. Die Räder, das Lenkrad und die Türen werden mit auf der gleichen Seite dargestellt auf der Ihr auch das Auto darstellt. Für diese Vorgehensweise wird kein zusätzlicher Controller und View benötigt. Nur wenn Ihr z.B. eine Liste aller Türen auf einer eigenen Seite (ohne Verbindung zum Auto) anzeigen lassen wollt, erst dann benötigt Ihr das Flag Aggregate Root damit auch ein Controller und ein View erstellt wird.
