+++
title = "extbase"
date = 2024-01-09T20:20:45+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["extbase.html"]
+++

Da die TYPO3-Programmierung auf Basis von PiBase immer undurchsichtiger wird und keine klare Struktur bei der Programmierung zu erkennen ist, haben sich die TYPO3-Entwickler auf die Suche gemacht und überlegt welches System sie in Zukunft für TYPO3 einsetzen können. Sie haben sich viele Frameworks angeschaut, aber überall Defizite gefunden und entschieden ein eigenes Framework aufzubauen. So entstand das Flow-Framework.

Dieses Flow wird bzw. ist die Basis des neuen CMS Neos, welches damals unter dem Namen Phoenix geführt wurde. Da Flow eine völlig neue Basis und auch völlig anders aufgebaut ist, kann Flow nicht von jetzt auf gleich den derzeitigen TYPO3 4.3 Core ersetzten. Damit aber schon heute Extensions auf Basis des neuen Systems programmiert werden können, hat man Teile des Flow-Frameworks als Systemextension für TYPO3 4.3 bereitgestellt. So entstand die Extension `extbase`.

Extbase arbeitet nach dem MVC-Prinzip (Modell, View, Controller). Hier eine erste Einleitung in die einzelnen Begrifflichkeiten:

Das Modell ist die Basis einer jeden Extbaseextension und stellt eine einheitliche Sprache zwischen allen Projektbeteiligten dar. Wenn es z.B. um das Thema Haus geht und im Modell hat man sich in Zusammenarbeit mit dem Kunden auf das Wort Raum geeinigt, dann sollte auch immer dieses Wort verwendet werden und nicht auf einmal von Zimmer geredet werden. Da die deutsche Sprache allerdings geprägt ist mit ihren Sonderzeichen, die sich nur schwer in die Programmierung einbauen lassen, empfiehlt es sich ein Glossar aufzubauen, in dem alle Modelleigenschaften in die englische Sprache übersetzt werden. Wir werden später beim Erstellen unserer ersten Extbase-Extension noch näher auf das Thema Modell eingehen.

Das View kümmert sich um die Ausgabe von Daten. Das kann ein einfaches echo sein, ein Templatesystem wie Smarty oder das eigens entwickelte Fluid. Fluid hat den Vorteil, dass es komplett objektorientiert aufgebaut ist, eine sehr einfache Syntax mitbringt und Dank sogenannter ViewHelper Kontrollstrukturen enthalten kann, um so z.B. im Template selbst for-Schleifen und if-Anweisungen auszuführen.

Der Controller ist das Mittelteil von dem Modell und dem View. In dem Controller wird entschieden welche Daten aus dem Repository (z.B. Datenbank) geholt werden und an welchen View übergeben werden. Hier finden keine Feldüberprüfungen statt. Hier geht es nur ums Holen und Abgeben von Daten an den View oder wieder zurück ans Repository.
