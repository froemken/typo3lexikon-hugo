+++
title = "Composer"
linkTitle = "Composer"
date = 2025-01-26T21:23:24+01:00
alwaysopen = false
aliases = ["composer.html"]
+++

[Composer](https://getcomposer.org/) ist ein CLI Command basierend auf der Programmiersprache PHP zum Installieren und Verwalten von PHP Bibliotheken. Im Englischen wird von `packages` gesprochen, warum Composer auch als `packagemanager` bezeichnet wird.

## Warum Composer?

Damals wurde für PHP Projekte oftmals eine eigene Implementation von Datenbankabfragen, Logging oder auch Mailversand programmiert. Mit jedem neuen Projekt wurden Teile dieser vorherigen Implementation von einem anderen Projekt in das aktuelle Projekt rüberkopiert und den neuen Gegebenheiten angepasst.

Was aber, wenn z.B. Server neue Richtlinien für den Empfang von Mails einführen? Damals konnte man Mails aus PHP heraus noch einfach mittels `mail` versenden. Dann irgendwann war eine Absender-E-Mail-Adresse zwingend erforderlich. Manche erinnern sich evtl. noch an `sendmail` mit dem `-f` Parameter. Später wurde empfohlen Mails über das SMTP-Protokoll zu versenden und selbst dort kommen immer mehr Restriktionen hinzu. So z.B. das Hinzufügen einer SMTP-Domain.

Das Internet lebt und entwickelt sich. Diese Entwicklungen müssen in allen Projekten angepasst werden, damit sie auch in Zukunft noch immer funktionieren. Viel Spaß jede einzelne `mail` Zeile zu aktualisieren, alle `mysql` Aufrufe durch `mysqli` zu ersetzen und sich mit jeder neuen Richtlinie und Anforderung zu beschäftigen und diese im Quellcode zu berücksichtigen.

Abhilfe schaffen hier PHP Bibliotheken (packages), die exakt diese Aufgaben abnehmen und von einer Community, einem Team möglichst aktuell gehalten werden. Als Programmierer braucht man sich um die neuen Richtlinien und Anforderungen keine Gedanken mehr zu machen und kann die gewonnene Zeit für die Kernaufgaben des eigenen Projektes einsetzen. Sollte es nun neue Richtlinien für den Mailversand geben, reicht es meist das verwendete Composer-Package zu aktualisieren.

Für viele Aufgaben, die in einem PHP-Projekt auftauchen, sind bereits tausende fertige Packages auf [Packagist](https://packagist.org/) (dem Standard-Repository von Composer) verfügbar. Mit jeder Implementation eures Quellcodes, für den Ihr ein passendes Package als Ersatz gefunden habt, kann euer Projekt platzsparender abgespeichert (in einem GIT-Repo oder Datenträger) oder auch transportiert (Mail, SCP) werden. Denn die verwendeten PHP Bibliotheken müssen nicht im Projekt mitgeführt werden, da diese PHP Bibliotheken nach Belieben aus dem Internet jederzeit wieder nachinstalliert werden können.

Oftmals haben Packages selbst auch wieder Abhängigkeiten zu anderen Packages oder erwarten Software auf eurem Server in einer vorgegebenen Version. So lassen sich manche Packages nur auf einem Server mit einer entsprechenden PHP Version installieren oder erwarten das zwingende Vorhandensein von einer PHP Extension wie `ext-json`. Wenn viele Packages verwendet werden kann es vorkommen, dass einige Packages Abhängigkeiten zu ein und demselben Package haben, jedoch unterschiedliche Versionen erfordern. So kann Package A zum Beispiel `symfony/mailer` ab Version 6.1.2 und Package B den `symfony/mailer` ab Version `6.3.4` erfordern. Hier kommt Composer ins Spiel und kann genau solche Abhängigkeiten auflösen und würde die Version `6.4.8` installieren, weil dies eine Version ist, die den Kriterien beider Packages entspricht.

Ein Beispiel aus TYPO3: Ohne Composer haben Extension-Entwickler solche PHP Bibliotheken in ihrer Extension entweder selber mitgeliefert oder eine zusätzliche Extension programmiert oder eine bereits existierende verwendet, die eben diese PHP Bibliothek mitgeliefert hat. Dies führte oft zu dem Problem, dass mehrere Extensions ein und dieselbe PHP Bibliothek mitlieferten, was dann zu Problemen im AutoLoader führte. Oder eine Extension erfordert eine Zusatzextension in einer anderen Version als von einer zweiten Extension gefordert. Mithilfe von Composer hört das Mitliefern von diesen PHP Bibliotheken innerhalb einer Extension auf und auch das Auslagern in eine Zusatzextension ist nicht mehr vonnöten. Die Extensions werden schlanker und Bibliotheken, die von mehreren Extensions benötigt werden, müssen nur noch einmal installiert werden.
