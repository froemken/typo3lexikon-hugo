+++
title = "formhandler"
date = 2024-01-09T20:51:29+01:00
aliases = ["formhandler.html"]
+++

## Das Schweizer Armeemesser

Zuallererst: Ich bin ein Fan von Powermail und ich denke, ich werde auch erstmal Fan von Powermail bleiben. Allerdings werde ich in Foren und Newsgroups immer wieder darauf hingewiesen für meine speziellen Fälle die Extension `formhandler` zu verwenden. Bisher habe ich Glück gehabt und konnte mein Anliegen dann doch irgendwie mit `powermail` lösen. Nun nagt mein Interesse am `formhandler` schon eine ganze Weile an mir, sodass ich euch jetzt nicht länger zutexten will.

Der `formhandler` ist eine vollständige Überarbeitung der Extension `th_mailformplus` und arbeitet nun nach dem MVC Konzept (Model, View, Controller). Laut Beschreibung bedeutet das für euch Flexibilität, Flexibilität, Flexibilität.

Diese Flexibilität wollen wir uns nun einmal anschauen und erstellen unser erstes mega super simpelstes Formular.

### Installation

Die Extension heißt `formhandler` und wird über den Extensionmanager installiert. Während der Installation wird noch eine kleine Logtabelle in der Datenbank erstellt, danach könnt ihr mit dem Anlegen einer neuen Seite beginnen und das Plugin einfügen. Bevor Ihr irgendwelche Einstellungen vornehmt erstellt für diese neue Seite noch ein Erweiterungstemplate, um das statische Template des formhandlers einzubinden.

### HTML-Template

Das ist der Grund, warum ich so begeistert von `powermail` bin. Klick dir ein paar Felder zusammen und kommt irgendwas hinten raus. Das ist bei `formhandler` nicht der Fall. Hier geht es um Individualität und Flexibitität und diese Beiden haben Ihren Preis. Also, setzen wir uns hin und können nun entscheiden, ob wir uns ein HTML-Template von den mitgebrachten Beispielen nehmen und entsprechend abändern, oder ob wir in der Dokumentation die Seite 36 `Available subparts to use in the template` aufschlagen.

Ich habe mich für Letzteres entschieden, einfach, weil ich die Stolpersteine kennenlernen will.
Subparts

In der Doku habe ich ein paar interesante Subparts gefunden:

```html
###TEMPLATE_FORM[1...x]###
```

Subpart für unser Formular. Wie man an den Nummern schon erkennen kann, sind auch multi-step-Formulare möglich. Auch wenn wir nur ein single-step-Formular realisieren wollen, müssen wir hier mit der 1 beginnen: `###TEMPLATE_FORM1###`

```html
###TEMPLATE_FORM[1...x][SUFFIX]###
```

Dieses Subpart wird nur für multi-step-Formulare benötigt. Mithilfe des SUFFIX könnt ihr dem User anhand von bestimmten Vorgaben andere Formulare im 2ten, 3ten Schritt anbieten. So kann der User auf der ersten Seite z.B. zwischen männlich/weiblich entscheiden und bekommt dann geschlechtsspezifische Fragen im 2ten Step.

###TEMPLATE_EMAIL_ADMIN_PLAIN###
> Hier kommt der reine Text, also nix HTML,  für den Admin rein

###TEMPLATE_EMAIL_ADMIN_HTML###
> Hier kommt der Text für den Admin im HTML-Format rein

###TEMPLATE_EMAIL_USER_PLAIN###
> Hier kommt der reine Text, also nix HTML,  für den User/Absender rein

###TEMPLATE_EMAIL_USER_HTML###
> Hier kommt der Text für den User/Absender im HTML-Format rein

###TEMPLATE_SUBMITTEDOK###
> Hier kommt der Text für eine Bestätigungsseite rein, in der der Absender seine Daten nochmals kontrollieren kann.

## Marker

###value_[Feldname]###
> GET/POST-Wert des angegebenen INPUT-Feldes. Hier ein Beispiel:

```html
<input type="text" name="email" value="###value_email###" />
```

###LLL:[langkey]###
> Mit diesem Marker können wir später auf die KEYs unserer angegebenen Sprachdatei zugreifen

###REL_URL###
> Verwendet diesen Marker für das action-Attribut des form-Tags.

###submit_nextStep###
> Dieser Marker wird für den Absendebutton benötigt. Lasst euch nicht von dem "nextStep" irritieren. Der heißt nun mal so und wird auch bei multi-step-Formularen verwendet. Hier ein Beispiel:

```html
<input type="submit" ###submit_nextStep### value="###LLL:next###" />
```

### Ein minimalistisches Template

```html
<!-- ###TEMPLATE_FORM1### begin -->
<form action="###REL_URL###" method="post">
  Deine E-Mail: <input type="text" name="email" value="###value_email###" /><br />
  Irgendein Text: <input type="text" name="irgendwas" value="###value_irgendwas###" /><br />
  <input type="submit" ###submit_nextStep### value="und tschüss" />
</form>
<!-- ###TEMPLATE_FORM1### end -->

<!-- ###TEMPLATE_EMAIL_ADMIN_PLAIN### begin -->
Plaintext zum Admin
<!-- ###TEMPLATE_EMAIL_ADMIN_PLAIN### end -->

<!-- ###TEMPLATE_EMAIL_ADMIN_HTML### begin -->
<html><body><ul>
  <li>HTML</li>
  <li>Mail</li>
  <li>zum</li>
  <li>Admin</li>
</ul></body></html>
<!-- ###TEMPLATE_EMAIL_ADMIN_HTML### end -->

<!-- ###TEMPLATE_EMAIL_USER_PLAIN### begin -->
Plaintext zum User
<!-- ###TEMPLATE_EMAIL_USER_PLAIN### end -->

<!-- ###TEMPLATE_EMAIL_USER_HTML### begin -->
<html><body><ul>
  <li>HTML</li>
  <li>Mail</li>
  <li>zum</li>
  <li>User</li>
</ul></body></html>
<!-- ###TEMPLATE_EMAIL_USER_HTML### end -->

<!--  ###TEMPLATE_SUBMITTEDOK### begin -->
Check mal ob alles OK ist lieber User
<!-- ###TEMPLATE_SUBMITTEDOK### begin -->
```

### Der erste Test

Speichert dieses Template im fileadmin-Verzeichnis ab und verweist von dem Plugin aus auf diese Datei. Achtet dabei darauf, dass ihr sonst keine weiteren Einstellungen wie Email, Absender, Empfänger und Validation im Plugin eingestellt habt. Auch dürfen noch keine TS-Einträge für dieses Plugin vorhanden sein.

Speichert nun das Plugin ab und schaut euch das Formular im Frontend an. OK, es ist noch nicht funktionstüchtig, aber ich bin immer ganz happy, wenn wenigstens überhaupt irgendwas angezeigt wird.

### Das Formular mit Leben füllen

Die Extension ist sehr penibel in dem, was sie kann. Sie befeuert euch sofort mit Fehlermeldungen, solltet ihr in dem Plugin mehr eintragen haben als die Angabe zum Template. Also, eins nach dem anderen.

Ein Formular macht natürlich nur dann Sinn, wenn auch irgendjemand über die eingetragenen Daten informiert wird. Also tragen wir nun im Plugin im Reiter `Admin Email Einstellungen` eine Empfängeradresse ein. Speichern und das Frontend wieder aufrufen:

```
Caught exception: Missing finishers! The Flexform contains 'meine-Emailadresse' but typoscript configuration is incomplete. Please add : Tx_Formhandler_Finisher_Mail
File: /pfadZumTYPO3Verzeichnis/test/typo3conf/ext/formhandler/Classes/Utils/Tx_Formhandler_StaticFuncs.php(515)
```

Das meine ich mit penibel. Ohne TS-Code läuft hier also gar nichts. Hier kommt eine kurze Erklärung:

Ganz zu Anfang habe ich euch gesagt, dass diese Extension auf dem MVC-Konzept beruht und damit extremst flexibel sein soll. Schaut euch in der Doku mal auf Seite 5 den Screenshot an. Hier seht ihr, welche Schritte der Formhandler durchläuft. So viel sei gesagt: Finisher kann mit "Ende" bzw. "Am Ende" übersetzt werden. Soll heißen: Hier kann entschieden werden, was passieren soll, wenn das Formular erfolgreich ausgefüllt wurde. Ihr seht auf dem Screenshot aber auch, dass der "Finisher" mehrere Lagen hat. Diese Information ist sehr wichtig für uns, denn am Ende einer Formularausfüllung wird ja nicht nur einfach eine Mail versendet. Evtl. wollt ihr den User ja auch noch auf eine andere Seite weiterleiten. Vielleicht wollt Ihr die eingetragenen Daten auch noch irgendwo in der Datenbank abspeichern. Der Formhandler bringt noch ein paar mehr Funktionen mit, die am Ende einer Formularausfüllung noch abgearbeitet werden können. All diese Funktionen werden von Formhandler in einer sogenannten Klasse mitgeliefert. Dieser Klassenaufbau hat den Vorteil, dass Ihr die Extension auch mit eigenen Klassen aufwerten könnt, aber da will ich jetzt noch nicht weiter darauf eingehen. Fakt ist erstmal, dass wir eine Mail versenden müssen und formhandler uns dafür die Klasse Tx_Formhandler_Finisher_Mail bereitstellt. Diese Klasse beinhaltet, wo schon erwähnt alle Funktionen, die zum Versenden von Mails nötig sind.

Also los, sagen wir dem Formhandler mal, welche Klasse er verwenden soll, um Mails zu versenden:

```typo3_typoscript
plugin.Tx_Formhandler.settings.finishers.1.class = Tx_Formhandler_Finisher_Mail
```

Die 1 ist eine fortlaufende Zahl, um z.B. noch weitere Klassen für den Finisher anzugeben. Da wir hier nur eine Klasse angeben bleibt's erstmal bei der 1

Nun versuchen wir erneut unser Formular im Frontend zu öffnen. Diesmal sollte keine Fehlermeldung erscheinen. Ihr könnt das Formular auch abschicken, ob mit oder ohne Daten, völlig egal. Also bei mir kommt eine Email an.

Wenn Ihr möchtet, könnt ihr nun die fehlenden E-Mail-Daten wie Betreff, Absendername und Empfängername im Plugin nachholen.

### Validatoren

Momentan schickt unser Formular unsere Texte, die wir im Template definiert haben, einfach raus, egal ob wir in unser Formular nun etwas eingetragen haben oder nicht. In diesem Schritt wollen wir das Formular mit einem Validotar versehen, damit die E-Mail erst dann versendet wird, wenn wir mindestens das Feld `email` ausgefüllt haben.

Dazu geht ihr in die Plugin-Konfiguration, letzter Tab `Andere Einstellungen`, ganz unten. Hier sollte sowas wie "Pflichtfelder" stehen. Tragt dort die Felder ein, die ihr überprüfen wollt. In unserem Falle: `email`. Speichern und das Formular im Frontend neu laden:

```
Caught exception: Missing validators! The Flexform contains 'email' but typoscript configuration is incomplete. Please add: Tx_Formhandler_Validator_Default
File: /kunden/226943_70794/rp-hosting/8928/9928/typo3cms/test/typo3conf/ext/formhandler/Classes/Utils/Tx_Formhandler_StaticFuncs.php(515)
```

Und schon wieder eine Fehlermeldung. Wieder müssen wir `formhandler` bescheid sagen, welche Klasse er verwenden soll, damit er weiß, wo er die Funktionen zur Überprüfung von Felder finden kann. Wir haben Glück, die Fehlermeldung sagt uns sogar schon welche Klasse formhandler gerne hätte. Also, los geht's

```typo3_typoscript
plugin.Tx_Formhandler.settings.validators.1.class = Tx_Formhandler_Validator_Default
```

Auch hier gibt es wieder die 1. Solltet ihr noch mal einen Blick auf den Screenshot von vorhin werfen, dann werdet ihr bestimmt gesehen haben, das ihr im formhandler auch mehrere Validatoren definieren könnt. Als Alternative bringt Formhandler z.B. einen Ajax-Validator mit.

Probiert euer Formular mal im Frontend aus. Evtl. werdet ihr bemerkt haben, dass sich das Formular nun nicht mehr verschicken lässt. Dies ist zwar ein erster Erfolg, aber auf was überprüft der Formhandler denn nun? Ganz einfach: Auf nix! Wenn wir dem Fornhandler sagen er soll für das Feld `email` einen Validator verwenden, wir aber keinen solchen definieren, dann verschickt die Extension auch keine Mails.

Wir kommen nun an einen Punkt, wo wir immer mehr TS schreiben müssen. Zur Information: Formhandler lässt sich nahezu komplett über TS steuern. Alle Einstellungen bzgl. E-Mail-Adresse, Absendernamen und Betreff werden "normalerweise" direkt im TS definiert. Das hat den Vorteil, dass ihr die komplette Konfiguration als Dateien vorliegen habt und auf andere TYPO3-Instanzen kopieren könnt, ohne ständig diese lästigen Formulare ausfüllen zu müssen. Später erkläre ich euch auch noch wie ihr mit TS vorgefertigte Formulare erstellen und dann in der Pluginkonfiguration einfach per Selectbox darauf zugreifen könnt. Kurzum: Jede unserer angegebenen Klassen (z.B. Tx_Formhandler_Validator_Default) lassen sich individuell konfigurieren. Schaut euch dazu mal unser gewachsenes TS an:

```typo3_typoscript
plugin.Tx_Formhandler.settings {
  finishers {
    1.class = Tx_Formhandler_Finisher_Mail
  }
    
  validators {
    1.class = Tx_Formhandler_Validator_Default
    1.config {
      fieldConf {
        email.errorCheck.1 = required
      }
    }
  }
}
```

Hier seht ihr einen Validator der das Feld email daraufhin überprüft, ob es ein Pflichtfeld ist. Auch hier gibt es wieder eine 1. Soll heißen ihr könnt mehrere Überprüfungen pro Feld einrichten. Formhandler bringt dazu über eine Din A4 Seite an weiteren Überprüfungstools mit, wie etwa max. und min. Länge, Ganzzahl, Float, email, captcha, und vielen Weiteren.
Fehlermeldungen

Im letzten Abschnitt hatten wir die Validatoren, aber was hilft uns ein Validator, wenn keine Fehlermeldung ausgegeben wird? Formhandler bringt für Fehlermeldungen gleich zwei Marker mit:

###ERROR###
> Sollten mehrere Validatoren anschlagen, dann werden mit diesem Marker ALLE Fehlermeldungen, die im kompletten Formular aufgetreten sind, hier ausgegeben.

###ERROR_[Feldname]###
> Mit diesen Markern wird nur der Fehler für das angegebene Feld ausgegeben. Diese Marker sollten üblicherweise in der Nähe des Feldes untergebracht werden :-)

Hier der neue obere Teil unseres Templates:

```html
<!-- ###TEMPLATE_FORM1### begin -->
###ERROR###
<form action="###REL_URL###" method="post">
  Deine E-Mail: <input type="text" name="email" value="###value_email###" /><br />
  Irgendein Text: <input type="text" name="irgendwas" value="###value_irgendwas###" /><br />
  <input type="submit" ###submit_nextStep### value="und tschüss" />
</form>
<!-- ###TEMPLATE_FORM1### end -->
```

### Die Sprachdatei

Formhandler bringt keine eigenen Fehlermeldungen mit. Wenn ihr Fehlermeldungen haben wollt, dann müsst ihr diese schon selbst definieren. Legt in dem Verzeichnis, wo sich auch unser Template befindet, eine XML-Datei an. Hier der Inhalt meiner lang-sf.xml:

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes" ?>
<T3locallang>
  <data type="array">
    <languageKey index="default" type="array">
      <label index="error_email_required">Enter your E-mail address!</label>
    </languageKey>
    <languageKey index="de" type="array">
      <label index="error_email_required">Tragen Sie eine E-mail-Adresse ein!</label>
    </languageKey>
  </data>
</T3locallang>
```

Fehlermeldungen fangen immer mit `error` an. Dann kommt der Feldname und dann der Validierungsname. Ergebnis: `error_email_required`. Wenn ihr euch an diese Struktur haltet, dann findet der Formhandler Eure Fehlermeldungen völlig automatisch.

Bindet diese XML-Datei nun in Eurer Pluginkonfiguration ein und schaut euch das Formular im Frontend erneut an.

Wunderbar, ihr habt es geschafft. Die Fehlermeldung wird nun angezeigt und sobald ihr irgendwas in dieses Feld eingetragen habt, kommt auch die Mail an.

### Schick die Daten nach Hause

In unserem letzten Schritt wollen wir die vom User eingetragenen Daten per Mail an unsere E-Mail-Adresse erhalten. Derzeit erhalten wir als Admin noch diese komische Liste, die wir in unserem Template definiert haben. Ersetzen wir den Bereich für die Adminmail mal mit diesem Inhalt:

```html
<!-- ###TEMPLATE_EMAIL_ADMIN_HTML### begin -->
<html>
  <body>
    <table>
      <tr>
    <td>email</td>
        <td>###value_email###</td>
      </tr>
      <tr>
        <td>irgendwas</td>
        <td>###value_irgendwas###</td>
      </tr>
    </table>
  </body>
</html>
<!-- ###TEMPLATE_EMAIL_ADMIN_HTML### end -->
```

Innerhalb dieses Subparts können wir auf alle Werte unserer Formulardaten mit `###value_[Feldname]###` zugreifen. Probiert's mal aus und ihr erhaltet die vom User ausgefüllten Daten per E-Mail. Diese Marker lassen sich natürlich auch für alle anderen Mailtemplates verwenden.

Fürs Erste sollte dieses "kleine" Tutorial erstmal genügen, um mit dem Formhandler klarzukommen. Da dies aber nur der Anfang war, könnt ihr euch in Zukunft auf noch ein paar weitere Tutorials, Tipps und Tricks zum Formhandler freuen.
