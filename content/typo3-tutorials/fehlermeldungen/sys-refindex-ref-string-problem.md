+++
title = "Problem mit sys_refindex"
date = 2024-01-09T20:45:40+01:00
aliases = ["sys-refindex-ref-string-problem.html"]
+++

Diesen Fehler schleppe ich nun schon seit Anfang 2008 mit mir rum und entweder verstehe ich die Lösungsansätze nicht oder der Ratschlag funktioniert einfach nicht. Ich habe mich heute mit diesem Problem beschäftigt und habe endlich eine Lösung gefunden.

TYPO3 empfiehlt immer wieder alles auf UTF-8-Basis zu erstellen. Ich verstehe zwar nicht warum es soviele verschiedene Zeichensätze gibt, wenn UTF-8 doch alles abdeckt?! Auf jeden Fall spiegelt ein Buchstabe auf UTF-8-Basis auch einen Buchstaben in der Datenbank wieder. Sobald wir aber auf Sonderzeichen umschwenken wie ä, ö, ü dann werden diese Zeichen bei Latin-codierten Datenbanken 2-stellig dargestellt (Ã¤ Ã¶ Ã¼ Ã„ Ã– Ãœ ÃŸ Ã¨ Ã©). Hat bestimmt jeder schon mal gesehen. Bei chinesischen Zeichensätzen sind es sogar 3 Stellen.

Genau für dieses Problem bietet TYPO3 im Installtool -> All Configuration das Feld multiplyDBfieldSize an. Soll heißen, wenn Ihr auf einer Latin-Basierten Datenbank arbeitet, dann müsst Ihr hier die Stellen angeben, die für einen Buchstaben in der DB reserviert werden sollen. Englische Sprache (keine Sonderzeichen) = 1, europäische Sprachen = 2 und Ostländer (China) = 3.

Ich glaube:
Wenn diese ganzen Einstellungen im Installtool nicht hundertprozentig auf UTF-8 getrimmt sind, dann verdoppelt bzw. verdreifacht sich der Wert von 400 (unsere Fehlermeldung) auf 1200 und übersteigt damit die DB-technische Grenze von 1000 und es kommt bei phpMyAdmin/mysqldumper folgende Fehlermeldung:

```
Specified key was too long; max key length is 1000 bytes
```

TYPO3 auf UTF-8 trimmen:
Diesen Schritt solltet Ihr machen BEVOR Eure Webseite Seiteninhalt erhält, ansonsten müsst Ihr Euch mit entsprechenden UTF-8 Konvertierungstools helfen oder jeden Seiteninhalt eigenhändig durchgehen und die Sonderzeichen wieder per Hand umstellen (PS: Ich hab händisch 3 Webseiten entsprechend umgestellt).
Im Installtool -> All Configuration stellt Ihr bitte folgende Werte ein:

```php
multiplyDBfieldSize = 1
setDBinit = SET NAMES utf8;
SET CHARACTER SET utf8;
SET SESSION character_set_server = utf8;
SET character_set_connection = utf8;
UTF8filesystem = true (Haken rein)
forceCharset = utf-8 (Der Wert muss kleingeschrieben werden)
```

So, diese Daten übernehmen wir erstmal und klicken dann auf Database Analyzer und drücken dort auf COMPARE. Normalerweise sollte hier Einiges zu tun sein.

{{% notice style="info" title="Hinweis" icon="info" %}}
Es kann sinnvoll sein, alle Indexe der Tabelle `sys_ref_index` zunächst zu entfernen z.B. mit PhpMyAdmin und erst danach die Indexe über das Installtool wieder anlegen lassen.
{{% /notice %}}

Nachdem wir hier jetzt erstmal die gesunde Basis für TYPO3 geschaffen haben, könnte es dennoch sein, dass der Fehler auftritt. Dazu habe ich folgenden Hinweis gefunden:

http://bugs.mysql.com/bug.php?id=4541

Wir haben jetzt zwar TYPO3 auf UTF-8 getrimmt, aber die Datenbankfelder selbst könnten noch auf Latin stehen. Um das zu überprüfen habe ich eine Extension programmiert, die nur die COLLATION der Datenbankfelder auf das gewünschte UTF-8-Format ändert NICHT aber die Daten selbst:

sfdbutf8

Nach der Installation findet Ihr links im Menü einen Punkt DB UTF-8. Nach einem Klick auf DB Check werden nun die COLLATIONs ausgelesen und entsprechend farbig (rot/grün) angezeigt. Im nächsten Step empfehle ich utf8_general_ci zu wählen und die DB upzudaten. Danach emfehle ich es beim DB Analyzer nochmals den COMPARE-Button zu klicken.

Ich habe dieses Problem nun schon mehrfach gehabt und konnte es nun nach dieser Anleitung beseitigen.
