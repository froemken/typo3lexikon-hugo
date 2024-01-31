+++
title = "Mappings verschwinden"
date = 2024-01-09T20:53:23+01:00
aliases = ["mappings-verschwinden.html"]
+++

Gerade hat mich ein Anruf erreicht, dass bei einer TYPO3-Installation eines Kollegen sich die Mappings von TemplaVoila nach der Speicherung direkt wieder gelöst haben.

Ich hab eine ganze Weile suchen müssen und habe dann festgestellt, dass TemplaVoila noch vor der TYPO3-Umstellung auf UTF-8 installiert wurde. Da im Installtool auch der Haken für write-UTF-8-files gesetzt wurde, musste ich nur mit dem `t3quixplorer` das TemplateFile öffnen und es unverändert wieder speichern.

Dadurch dass das File nun im UTF-8-Format gespeichert wurde, klappte es anschließend auch wieder mit den Mappings.

Da muss man erstmal drauf kommen.
