+++
title = "ttnews_youtube"
date = 2024-01-09T20:53:33+01:00
aliases = ["ttnews-youtube.html"]
+++

Mit der Extension `ttnews_youtube` habt ihr die Möglichkeit eure News um ein YouTube Video zu erweitern. Die Extension liefert euch dafür drei weitere Eingabemöglichkeiten in den News selbst:

1. YouTube-Link
2. Videobreite
3. Videohöhe

Das Video selbst bindet ihr mit dem neuen Marker `###YOUTUBEVIDEO###` in das `tt_news` Template ein.

Die Extension ist leider schon ein bisschen in die Jahre gekommen, da z.B. die neue Einbindungsmöglichkeit von YouTube Videos per iFrame auf die Webseite zu setzen nicht unterstützt wird. Außerdem funktioniert auch der mitgelieferte HTML-Code nicht mehr, da die Link für die YouTube-Videos mitlerweile anders einzubauen sind.

Da ich diese Extension aber für einen Kunden benötigte habe ich dem Extensionentwickler meine Hilfe zur Weiterentwicklung angeboten. Da ich aber noch keine Antwort erhalten habe, habe ich schonmal angefangen die Extension aufzubohren.

In meiner überarbeiteten Version könnt ihr zwischen der neuen und der etwas verbesserten oldschool-Variante wählen. Selbst wenn sich das Template wiedererwartend ändern sollte, so könnt ihr das Template nun selbst abändern und den neuen Pfad in den Extensionkonstanten angeben.

In den News wird nicht mehr der komplette Pfad zu den Videos angegeben, sondern nur noch die reine Video-ID, die ihr der YouTube-URL entnehmen könnt. Der Rest der URL wird auch wieder in den Extensionkonstanten vorgenommen.

Also: Egal was sich jetzt noch bei YouTube ändern sollte, mit meiner überarbeiteten Version sollte es nun klappen und wenn der Extensionentwickler sich bei mir melden sollte oder diese Seite findet, dann darf er sich diese abgeänderte Version gerne nehmen und im TER der Öffentlichkeit zugänglich machen.
