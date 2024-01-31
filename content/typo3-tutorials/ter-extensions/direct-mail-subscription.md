+++
title = "direct_mail_subscription"
date = 2024-01-09T20:51:04+01:00
aliases = ["direct-mail-subscription.html"]
+++

## direct_mail Newsletteranmeldung

Die Extension ist seit 2006 nicht mehr aktualisiert worden und beinhaltet keine Dokumentation bzgl. Installation und Konfiguration.

### Installation

Die Installation benötigt, wie der Name schon andeutet, eine zuvor installierte Version von `direct_mail`. Die Tabelle `tt_address`, in der die Daten für die Newsletteranmeldung abgelegt werden, sollte schon während der Installation von `direct_mail` automatisch mit installiert worden sein.

Nach der Installation könnt ihr das Plugin in eure Seite einbinden. Damit die Extension weiß, wo die Benutzer abzulegen sind, müsst Ihr noch das Feld Ausgangspunkt füllen. Nach dem Abspeichern und einem Clear-Cache erscheinen die Felder zur Newsletteranmeldung auf der Webseite.

### Konfiguration

Tragt in eurem Haupttemplate folgende Zeile in den Setup-Bereich ein:

```typo3_typoscript
config.notification_email_urlmode = 76
```

Damit werden alle Links, die länger sind als 76 Zeichen (also einen Umbruch in die nächste Zeile enthalten) in einen Hashwert umgewandelt. Damit sollte dann auch jedes E-Mail-Programm mit klarkommen.

Die folgenden Einstellungen würde ich als +extTemplate anlegen:

### Konstanten

Da diese Extension nicht dokumentiert ist, musste ich mich selbst durch die Einstellungen wühlen und hab folgende Konstanten gefunden, die auch ihr füllen solltet. `email` und `emailName` sollten klar sein. Die `pid` ist die Seite bzw. der Sysfolder in dem die registrierten Benutzer abgelegt werden sollen. Ihr könnt also selbst entscheiden, ob ihr diese Angabe hier über die Konstanten realisiert oder über das Feld Ausgangspunkt in dem Plugin selbst. Damit Euer Template nach einem Update der Extension nicht überschrieben wird, empfehle ich Euch das Originaltemplate der Extension in das `fileadmin` Verzeichnis zu kopieren oder gleich meine deutsche Vorlage dort hochzuladen und mit der Eigenschaft file.

### templateFile zuzuweisen.

```typo3_typoscript
plugin.feadmin.dmailsubscription.email = E-Mail-Adresse des Newsletter
plugin.feadmin.dmailsubscription.emailName = Absendername des Newsletter z.B. TYPO3-Newsletter
plugin.feadmin.dmailsubscription.pid = 57
plugin.feadmin.dmailsubscription.file.templateFile = fileadmin/templates/fe_admin_dmailsubscrip_de.tmpl
```

### Setup

Im Template selbst sind nicht alle Texte vorhanden. So sind einige englischen Texte im Setup versteckt und müssen noch ins Deutsche übersetzt werden (`email.uniqueLocal` und `email.email`). Die Pluginüberschriften werden noch mit font-Tags realisiert. Da font-Tags veraltet sind, habe ich mich hier (`wrap1.wrap`) für eine etwas kleinere Überschrift `h2` entschieden. Den zweiten `wrap` habe ich mit einer powermail-Klasse versehen, da ich dann ohne weiteres meine vorhandene Powermail-CSS auch für diese Extension verwenden kann. Dieser zweite Wrap "ummantelt" das komplette Formular.

```typo3_typoscript
plugin.feadmin.dmailsubscription.evalErrors.email.uniqueLocal = Anscheinend sind Sie bereits mit dieser E-Mail registriert!
plugin.feadmin.dmailsubscription.evalErrors.email.email = Dies ist keine gültige E-Mail-Adresse!
plugin.feadmin.dmailsubscription.wrap1.wrap = <h2> | </h2>
plugin.feadmin.dmailsubscription.wrap2.wrap = <div class="tx-powermail-pi1"> | </div>
```

### Mehrsprachigkeit

`direct_mail_subscription` ist nicht mehrsprachenfähig! Ihr müsst also für jede Sprache ein eigenes Template erstellen und dieses mithilfe von `[GLOBAL]` Bedingungen individuell setzen:

```typo3_typoscript
[globalVar = GP:L = 0]
plugin.feadmin.dmailsubscription.file.templateFile = fileadmin/templates/fe_admin_dmailsubscrip.tmpl
[globalVar = GP:L = 1]
plugin.feadmin.dmailsubscription.file.templateFile = fileadmin/templates/fe_admin_dmailsubscrip_de.tmpl
[globalVar = GP:L = 2]
plugin.feadmin.dmailsubscription.file.templateFile = fileadmin/templates/fe_admin_dmailsubscrip_dk.tmpl
[global]
```
