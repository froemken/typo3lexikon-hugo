+++
title = "Sprachdateien Einbinden"
date = 2024-01-09T20:47:39+01:00
aliases = ["sprachdateien-einbinden.html"]
+++

Immer wieder habe ich Probleme mit dem Updaten der Sprachdateien, oder diese Dateien lassen sich erst gar nicht installieren oder der Balken knallt direkt von links nach rechts und irgendwie ist nix passiert.

## Hier also mal ein paar Lösungsansätze

Geh im TYPO3-Installtool auf phpinfo() und überprüfe erstmal ob die Eigenschaft allow_url_fopen auf "on" gesetzt ist. Bei den meisten Servern sollte es Standard sein, aber falls nicht, dann muss dieser Wert in der php.ini geändert werden, oder Du kannst im Installtool -> All Configuration nach "cURL" suchen und dort den Haken setzen.

Schon mal bricht der Update-Vorgang mit Error ab. In diesem Fall geht Ihr per FTP auf Euren Server und löscht im Verzeichnis typo3temp alle zip-Dateien, die ein l10n enthalten. Wenn selbst das nichts bringt, sichert bitte das Verzeichnis /typo3conf/de und löscht dann anschließen das de-Verzeichnis, wie oben beschrieben diese zip-Dateien und führt den Check bzw. Update-Vorgang erneut aus.

In der Hektik vergess ich schon mal die Berechtigungen der beiden Verzeichnisse typo3conf und typo3temp zu überprüfen. Das doofe an der Sache: Je nach Serverkonfiguration klappt die sichere Methode 700, aber bei anderen Hostern musste auch schon mal ein 777 herhalten.

## Seltene Spezialfälle

Es kann sein dass das UnZIP-Programm nicht dort vorhanden ist, wo TYPO3 es erwartet. In diesem Fall müsst ihr im Installtool -> All Configuration den unzip_path entsprechend setzen.

Eine Webseite von uns läuft auf nem Strato-Server. Dort ist zwar die Eigenschaft allow_url_fopen auf "on" gesetzt, aber das Update funktioniert mit aktivierter cURL-Eigenschaft im Installtool wesentlich stabiler.

Manchmal kann es auch sein, dass der Server einfach viel zu langsam ist, sodass die max_execution_time von 30 Sekunden nicht mehr ausreicht. In diesem Fall installiert ihr Euch am besten den xampp-Webserver mit TYPO3 und zieht Euch die Languagefiles dann über Eure DSL-Leitung. Anschließend schaufelt Ihr das /typo3conf/de-Verzeichnis per FTP auf Euren Server.
