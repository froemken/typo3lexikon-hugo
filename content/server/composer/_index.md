+++
title = "Composer"
linkTitle = "Composer"
date = 2025-01-26T21:23:24+01:00
alwaysopen = false
aliases = ["composer.html"]
+++

[Composer](https://getcomposer.org/) ist ein CLI Command basierend auf der Programmiersprache PHP zum Installieren und Verwalten von PHP Bibliotheken. Im Englischen wird von `packages` gesprochen, warum Composer auch als `packagemanager` bezeichnet wird.

## Warum Composer?

Damals wurde für PHP-Projekte oft eine eigene Implementierung für Datenbankabfragen, Logging oder auch Mailversand programmiert. Mit jedem neuen Projekt wurden Teile dieser früheren Implementierung aus einem anderen Projekt in das aktuelle Projekt kopiert und an die neuen Gegebenheiten angepasst.

Was aber, wenn z.B. Server neue Richtlinien für den Empfang von Mails einführen? Damals war es noch einfach, Mails aus PHP heraus per `mail` zu versenden. Dann wurde irgendwann eine Absender-E-Mail-Adresse zwingend erforderlich. Einige erinnern sich vielleicht noch an `sendmail` mit dem Parameter `-f`. Später wurde das Versenden von Mails über das SMTP-Protokoll empfohlen und selbst dort kamen immer mehr Einschränkungen hinzu. Zum Beispiel das Hinzufügen einer SMTP-Domain.

Das Internet ist lebendig und entwickelt sich weiter. Diese Entwicklungen müssen in allen Projekten angepasst werden, damit sie auch in Zukunft noch funktionieren. Viel Spaß dabei, jede `mail`-Zeile zu aktualisieren, alle `mysql`-Aufrufe durch `mysqli` zu ersetzen und sich mit jeder neuen Richtlinie und Anforderung auseinanderzusetzen und diese im Quellcode zu berücksichtigen.

Abhilfe schaffen hier PHP-Bibliotheken (Packages), die genau diese Aufgaben übernehmen und von einer Community, einem Team, möglichst aktuell gehalten werden. Als Programmierer muss man sich nicht mehr um neue Richtlinien und Anforderungen kümmern und kann die gewonnene Zeit für die Kernaufgaben des eigenen Projekts nutzen. Wenn es neue Richtlinien für den Mailversand gibt, reicht es in der Regel aus, das verwendete Composer-Package zu aktualisieren.

Für viele Aufgaben, die in einem PHP-Projekt anfallen, gibt es bereits tausende fertige Packages auf [Packagist](https://packagist.org/) (dem Standard-Repository von Composer). Mit jeder Implementierung des eigenen Sourcecodes, für die man ein passendes Package als Ersatz gefunden hat, kann das eigene Projekt platzsparender gespeichert (in einem GIT-Repo oder auf einem Datenträger) oder auch transportiert (Mail, SCP) werden. Denn die verwendeten PHP-Bibliotheken müssen nicht im Projekt mitgeführt werden, da diese PHP-Bibliotheken jederzeit beliebig aus dem Internet nachinstalliert werden können.

Oft haben Packages selbst wieder Abhängigkeiten zu anderen Packages oder erwarten Software auf dem Server in einer bestimmten Version. So können einige Packages nur auf einem Server mit einer entsprechenden PHP-Version installiert werden oder erwarten die zwingende Anwesenheit einer PHP-Erweiterung wie `ext-json`. Wenn viele Packages verwendet werden, kann es vorkommen, dass einige Packages Abhängigkeiten zu ein und demselben Package haben, aber unterschiedliche Versionen benötigen. Zum Beispiel kann Package A `symfony/mailer` ab Version `6.1.2` benötigen und Package B `symfony/mailer` ab Version `6.3.4`. Hier kommt der Composer ins Spiel, der genau solche Abhängigkeiten auflösen kann und die Version `6.4.8` installieren würde, da diese die Kriterien beider Packages erfüllt.

Ein Beispiel aus TYPO3: Ohne Composer haben Extension-Entwickler solche PHP-Bibliotheken entweder selbst in ihrer Extension mitgeliefert, eine zusätzliche Extension programmiert oder eine bereits existierende Extension verwendet, die genau diese PHP-Bibliothek mitgeliefert hat. Dies führte oft zu dem Problem, dass mehrere Extensions ein und dieselbe PHP-Bibliothek auslieferten, was dann zu Problemen im AutoLoader führte. Oder eine Extension benötigte eine weitere Extension in einer anderen Version als eine zweite Extension. Mithilfe des Composers entfällt die Auslieferung dieser PHP-Bibliotheken innerhalb einer Extension und auch die Auslagerung in eine Zusatzextension ist nicht mehr notwendig. Die Extensions werden schlanker und Bibliotheken, die von mehreren Extensions benötigt werden, müssen nur einmal installiert werden.
