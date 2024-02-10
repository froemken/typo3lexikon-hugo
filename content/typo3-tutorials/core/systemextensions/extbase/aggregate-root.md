+++
title = "Aggregate Root"
description = "Bei einem Aggregate Root handelt es sich um einen Zusammenschluss mehrerer Objekte, die fest zusammen gehören."
keywords = "typo3 stefan frömken lindlar oberberg oberbergischer kreis tutorial aggragate, root, extbase"
date = 2024-02-10T22:16:11+01:00
aliases = ["aggregate-root.html"]
+++

In den Extbase-Büchern werden die Aggregate Roots immer als Einheit von Objekten beschrieben. Soll heißen: Wenn ich ein Auto habe, dann hat dieses Auto natürlich auch Reifen, ein Lenkrad und Türen. Alles zusammen ergibt das Objekt Auto, das als Aggregate Root definiert werden kann. Von der Theorie her bedeutet das, dass man die Objekte Tür, Lenkrad und Reifen nur noch über das Objekt Auto erreichen kann.

Auch der `extension_builder` kann Objekte als `Aggregate Root` kennzeichnen. Hier erkennt man allerdings sehr schnell, dass der `extension_builder` für diese Objekte auch gleich einen Controller und eine View einrichtet im Gegensatz zu Objekten ohne Aggregate Root.

Ich persönlich kann mir unter dieser Theorie nur wenig vorstellen, deshalb eine etwas einfachere Beschreibung von mir:

Nehmen wir noch mal unser Auto von oben: Ich will das Auto XY anzeigen lassen. Die Räder, das Lenkrad und die Türen werden mit auf der gleichen Seite dargestellt, auf der ihr auch das Auto darstellt. Für diese Vorgehensweise wird kein zusätzlicher Controller und View benötigt. Nur wenn ihr z.B. eine Liste aller Türen auf einer eigenen Seite (ohne Verbindung zum Auto) anzeigen lassen wollt, erst dann benötigt ihr das Flag `Aggregate Root`.
