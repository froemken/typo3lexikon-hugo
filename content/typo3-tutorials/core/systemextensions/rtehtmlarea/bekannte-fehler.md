+++
title = "Bekannte Fehler"
date = 2024-01-09T20:32:36+01:00
aliases = ["bekannte-fehler.html"]
+++

## Der a-Tag wird zerhackt

Dieser Fehler liegt an den deutschen Sonderzeichen wie äöüß. Damit diese nicht standartmäßig in HTML-Entities umgewandelt werden, müsst Ihr im HTMLparser die Eigenschaft htmlspecialchars auf 0 setzen.
