+++
title = "BE"
date = 2024-01-09T20:47:58+01:00
aliases = ["typo3-conf-vars-be.html"]
+++

## $GLOBALS['TYPO3_CONF_VARS']['BE']

## languageDebug Seit TYPO3 8.7

Durch Aktivierung dieser Option wird im LanguageService ein Schalter umgelegt, der für jeden übersetzbaren Text, der aus xml oder noch besser xlf Dateien kommt, zusätzlich noch der LanguageKey und der Pfad an den Text hinten angefügt. Beispiel: Aus Your name in den User Settings wird:

```
Your name[beUser_realName][LLL:EXT:setup/Resources/Private/Language/locallang.xlf:beUser_realName]
```

## fileadminDir Standard: fileadmin/

Pfad zum fileadmin Verzeichnis. Ausgangspunkt ist das Web Root Verzeichnis aus PATH_site. Das fileadmin Verzeichnis ist der zentrale Ordner für alle Dateien, die im Frontend ausgegeben werden sollen, wie z.B. Bilder, PDFs und Downloads. Während früher auch noch HTML-Templates, TypoScript, CSS, JavaScripte und Co. enthalten waren, ist es heute Trend solche Dateien in SitePackage Extensions auszulagern. Da das fileadmin Verzeichnis sehr voll werden kann, liegt es nahe diese Dateien auf eine andere Festplatte, Partition oder NAS auszulagern. Man erstellt einen SymLink zu diesem Share und gibt hier den geänderten Pfad an.

Ab TYPO3 6.0 gibt es FAL und Ihr könnt Eure Storages und FileMounts selbst erstellen. Der Standard Storage wird mit diesem Pfad hier angelegt.

Für die Programmierer unter uns: Arbeitet in Eurer Programmierung bitte nicht mehr mit dieser Variable. Nutzt stattdessen: \TYPO3\CMS\Core\Resource\ResourceFactory::getDefaultStorage()

## RTE_imageStorageDir Standard: uploads/

Standard Pfad für Dateien, die über den RTE eingebunden wurden. Ausgangspunkt ist das Web Root Verzeichnis aus PATH_site.

## lockRootPath

Wenn `[BE][lockRootPath]` nicht gesetzt, leer oder ein leeres Array ist, dann kann TYPO3 aus Sicherheitsgründen nur auf Dateien innerhalb des Projektverzeichnisses (wo `vendor` und `config` liegt) und auf Dateien innerhalb vom Webroot (wo `fileadmin`, `index.php` und `_assets` liegen) zugreifen. Da `[BE][lockRootPath]` standardmäßig nicht gesetzt ist, müsst ihr bei Verwendung zusätzlicher Verzeichnisse außerhalb vom Projektverzeichnis und Webroot hier den absoluten Pfad zu diesem zusätzlich erlaubten Pfad angeben. Das kommt schonmal vor, wenn ihr 2 TYPO3 Installationen habt und ein Bilderordner ist 2 Ebenen oberhalb eurer TYPO3 Instanzen. Dann setzt den absoluten Pfad zu diesem Ordner hier und ihr könnt mittels eines Dateispeichers (Tabelle: `sys_file_storage`) darauf zugreifen.

{{% notice style="info" title="Hinweis" icon="exclamation" %}}
Angaben ausgehend vom Home-Verzeichnis `~/httpdocs/typo3/12.4/` und auch relative Angaben wie `../../` sind nicht erlaubt. Der absolute Pfad muss mit einem Slash `/` enden.
{{% /notice %}}

## userHomePath

Hierbei handelt es sich um einen Pfad zu dem Homeverzeichnis der TYPO3 Backendbenutzer. Der Pfad besteht aus Storage UID und Ordnerfad: [Storage UID]:[Ordnername]. Beispiel 2:users/. Das Homeverzeichnis für den Backendbenutzer mit der UID 5 resultiert somit in dieses Verzeichnis: 2:users/5/. Der abschließende Slash ist zwingend erforderlich!

Die Homeverzeichnis und das Verzeichnis mit der Benutzer UID werden nicht automatisch angelegt. Diese müsst Ihr selbst anlegen.

## groupHomePath
 Hierbei handelt es sich um einen Pfad zu dem Homeverzeichnis der TYPO3 Backendbenutzergruppen. Der Pfad besteht aus Storage UID und Ordnerfad: [Storage UID]:[Ordnername]. Beispiel 2:groups/. Das Homeverzeichnis für die Backendbenutzergruppe mit der UID 3 resultiert somit in dieses Verzeichnis: 2:groups/3/. Der abschließende Slash ist zwingend erforderlich!

Die Gruppenverzeichnis und das Verzeichnis mit der Gruppen UID werden nicht automatisch angelegt. Diese müsst Ihr selbst anlegen.

## userUploadDir

Da in einem Homeverzeichnis eines Benutzers überwiegend benutzereigene Dateien liegen, die im TYPO3 nichts zu suchen haben, ist es angebracht die Dateien, die TYPO3 bezogenen Dateien in einem entsprechenden Unterordner des Homeverzeichnisses anzulegen.

Anders als bei userHomePath und groupHomePath muss dieser Pfad hier mit einem Slash anfangen. Eine Angabe bei userHomePath wie 2:users/ und hier bei userUploadDir mit /typo3 resultiert dann in: 2:users/[Benutzer UID]/typo3

Auch hier gilt wieder: Die Pfade müssen von Euch bereits angelegt worden sein. Nur dann erscheinen diese automatisch als FileMount im Modul Filelist.

## warning_email_addr

Tragt hier eine Emailadresse ein, an die immer dann eine Benachrichtigung gesendet werden soll, wenn sich jemand am Installtool anmeldet. Zusätzlich erfolgt eine Benachrichtigung, wenn 3 Anmeldeversuche unabhängig des Benutzernamens binnen einer Stunde am TYPO3 Backend fehlgeschlagen sind.

## warning_mode

Wenn dieser Wert leer ist, gelten die Bedingungen aus warning_email_addr. Setzt den Wert auf 1, wenn die Emailadresse aus warning_email_addr bei jedem Anmeldeversuch am Backend benachrichtigt werden soll. Mit dem Wert 2 erfolgt die Benachrichtigung nur noch dann, wenn sich ein Admin am Backend anmeldet.

## lockIP Standard: 4

Wenn ein Benutzer sich am TYPO3 Backend anmeldet wird eine Session angelegt, die von Seite zu Seite mitgeschleppt wird. Dank solcher Sessions bleiben Benutzer am System angemeldet und werden nicht mit jedem Seitenwechsel wieder ausgeloggt. TYPO3 speichert die aktuelle IP-Adresse des Clients in dieser Session und prüft mit jedem neuen Seitenaufruf im Backend, ob die IP-Adresse noch mit der IP-Adresse aus der Session übereinstimmt. Wenn die nicht mehr übereinstimmt findet eine sofortige automatische Abmeldung statt.

Mit einem Wert von 4 werden alle 4 Oktetten einer IP-Adresse in der Session abgelegt und ist sicherheitstechnisch der bester Wert. In seltenen Fällen bzw. je nach Netzwerkkonfiguration einiger Firmen könnte der Internetzugang mittels mehrerer  Proxyserver realisiert sein. Es ist nicht sichergestellt, dass jeder Request im TYPO3 Backend von dem gleichen Proxyserver verarbeitet wird und somit die Anfrage mit einer anderen IP-Adressen erfolgen könnte. Ein Wechsel der Proxyserver (andere IP) wird somit mit einem automatischen Ausloggen des Benutzers quittiert.

Da die Proxyserver meist im gleichen Netzwerk sind, macht es Sinn den Wert lockIP auf die Anzahl der Oktetten, die für die Netzwerkmaske zuständig sind herunter zu setzen. Ein Wert von 0 deaktiviert die IP Überprüfung komplett.

Im Backendbenutzer Datensatz be_users gibt es ebenfalls die Möglichkeit mittels dem Feld disableIPlock die IP Überprüfung komplett abzuschalten.

## sessionTimeout Standard: 3600

Gebt hier die Sekunden an, nach denen bei Nichtbenutzung des TYPO3 Backends eine automatische Abmeldung erfolgen soll. Da einige Prozesse innerhalb von TYPO3 schonmal länger brauchen, sollte ein Wert von 180 Sekunden nicht unterschritten werden.

## IPmaskList

Hier könnt Ihr eine IP-Adresse angeben, mit der der Zugriff auf das TYPO3 Backend erlaubt wird. Alle anderen Zugriffe werden mit folgender Meldung quittiert:

```
#1389265900: TYPO3 Backend access denied: The IP address of your client does not match the list of allowed IP addresses.
```

Mit Hilfe von * als Platzhalter können auch IP-Bereiche definiert werden: 123.123.*.*

## lockBeUserToDBmounts Standard: aktiviert

Bei diesem Wert handelt es sich um ein Sicherheitsfeature und sollte immer aktiviert bleiben und ist nur für nicht Admins relevant. Bei jedem Backend Modul, das auch den Seitenbaum beinhaltet, wird bei Klick auf eine Seite die UID an den rechten Frame übergeben. Wenn lockBeUserToDBmounts aktiv ist, wird geprüft, ob diese übergebene UID sich innerhalb der Backend Benutzer zugewiesenen DB Mounts befindet. Wenn nicht, dann erscheint die Fehlermeldung:

```
#1289917924: You don't have access to this page
```

Um diese Fehlermeldung zu provozieren muss schon einiges passieren. Erstens muss mindestens die Checkbox Show Page für Everybody im Modul Access gesetzt sein. Dabei darf es sich ruhig um eine Seite handeln, die sich außerhalb der erlaubten DB Mounts für den Benutzer befindet. Zweitens: Als Nicht-Admin anmelden. Drittens: Eine beliebige Seite im rechten Frame in einem neuen Tab öffnen. Jetzt sieht man den Parameter in die Adressleiste des Browsers. Diese könnt Ihr auf die Seiten UID ändern, die sich außerhalb Eures DB Mounts befindet. Ihr werdet sehen: Ihr habt darauf Zugriff. Im Seitenbaum erscheint diese jedoch nicht.

Eine sinnvollere Anwendung findet Ihr nach Installation der EXT:sys_action. Der Administrator kann den Redakteuren über das Modul UserSettings sogenannte Tasks zuweisen, mit denen Redakteure z.B. Datensätze einer bestimmten Seiten UID anzeigen lassen könnt. Auch hier gilt wieder, ist lockBeUserToDBmounts gesetzt, können Redakteure die Action nicht ausführen. Fehlermeldung:

```
You don't have sufficient rights to see the records. Please ask the administrator to set those for you.
```

## lockSSL Standard: deaktiviert

Wenn Ihr ein SSL-Zertifikat oder zumindest ein selbst signierte Zertifikat für Eure Domain habt, dann könnt Ihr lockSSL aktivieren, damit das komplett TYPO3 Backend nur noch über https:// erreichbar ist und alle Eure Änderungen verschlüsselt vom Browser zum Server übertragen werden. Alle http:// Anfragen werden automatisch auf https:// weitergeleitet. Nach Aktivierung ist ein Zugriff per http:// demnach nicht mehr möglich.

Ich habe auf den TYPO3 Developer Days 2017 mit dem linken Ohr eine Information aufgegriffen, dass dieser Wert möglicherweise mit TYPO3 9 oder auch 10 standardmäßig aktiviert wird. Zu Zeiten von kostenlosen SSL-Zertifikaten wie Let's Encrypt nachvollziehbar, aber noch bietet nicht jeder Hoster diese kostenfreien Zertifikate an.

## lockSSLPort Standard: 0

Wenn dieser Wert 0 ist, dann wird der Standard Port 443 für SSL verwendet. Falls Euer Server jedoch für https:// Verbindungen auf einen anderen Port lauscht, dann müsst Ihr diesen Wert hier entsprechend ändern. Natürlich nur, sofern Ihr die Einstellung lockSSL aktiviert habt.

## enabledBeUserIPLock Standard: aktiviert

Mittels IPmaskList konnte schon eine IP Adresse oder Teile einer IP Adresse angegeben werden mit der der Zugriff auf das TYPO3 Backend kontrolliert wird. Mit enableBeUserIPLock geht es noch einen Schritt weiter. Damit wird die UserTsConfig  option.lockToIP für Backend Benutzer und Gruppen aktiviert. So kann eine IP Sperre pro Benutzer/Gruppe erzeugt werden. Evtl. sinnvoll wenn ein Redakteuer per Homeoffice auf die Webseite zugreifen soll, aber nicht von einem Internet-Café oder ähnlichem.

Auch hier sind wieder Platzhalter erlaubt: 123.123.*.*

## cookieDomain

Gebt hier die (Sub)Domain an, für die das Backend Session Cookie gültig sein soll.

Siehe $GLOBALS['TYPO3_CONF_VARS']['SYS']['cookieDomain'] für weitere Informationen.

## cookieName be_typo_user

Der Name, der für das Backend Session Cookie verwendet werden soll. Ich glaub Ihr müsst schon sehr abenteuerliche Cookie Konfigurationen und TYPO3 Instanzen haben, dass es evtl. Sinn machen könnte, diesen Wert zu ändern.

## loginSecurityLevel Standard: rsa

Sofern die EXT:rsaauth aktiviert ist, wird im Frontend für EXT:felogin als auch für den Backend Login ein zusätzliches JavaScript geladen, dass das Passwort browserseitig verschlüsseln kann. Ihr habt das bestimmt schonmal gesehen: Ihr tragt Euer Passwort ein und klickt auf anmelden. Für einen Bruchteil einer Sekunde vermehren sich die Sternchen aus dem Passwortfeld um ein Vielfaches.

Wenn dieser Wert auf normal gesetzt ist, wird das Passwort in Klartext an den Server übertragen und könnte von Hackern ausgespäht werden.

## showRefreshLoginPopup Standard: deaktiviert

Wenn die Lebenszeit des Backend Session Cookies abgelaufen ist, dann erscheint ein Dialogfenster, wie Ihr es evtl. vom CSS Framework Bootstrap kennt, und könnt dort Euer Passwort erneut eintragen, um Euch wieder am Backend anzumelden.

TYPO3 besitzt eine API für die Anmeldung am Backend. Heißt: TYPO3 kann so konfiguriert werden, dass die Anmeldung über einen LDAP-Server oder ähnlichem erfolgen kann. Einige solcher Dienste kommen mit diesem Dialogfenster nicht klar, da sie individuelle Überprüfungen für das Passwort implementieren müssen. Hier hilft es dieses Anmeldefenster nicht in einem Dialogfenster, sondern in einem Browser PopUp Fenster anzeigen zu lassen. Dazu muss dieser Wert hier aktiviert werden.

## adminOnly

Manchmal ist es erforderlich alle Redkteure vom TYPO3 Backend auszuschließen, damit diese z.B. keine Datenänderungen mehr vornehmen können. adminOnly versteht derzeit folgende Konfigurationsmöglichkeiten:

-1: Komplettes Abschalten des TYPO3 Backends für z.B. Wartungsarbeiten
0: Normalbetrieb. Jeder kann sich am Backend anmelden. Standard
1: Nur Administratoren können sich am Backend anmelden. CLI Benutzer ausgeschlossen
2: Administratoren und CLI Benutzer können sich am Backend anmelden

## disable_exec_function

Diese Option ist nur für die CommandUtility Klasse von TYPO3 gültig. Wenn disable_exec_function deaktiviert ist, dann könnt Ihr sämtliche Linuxbefehle mittels CommandUtility::getCommand() generieren und mit CommandUtility::exec() ausführen lassen. Auf Windows Servern stehen die Linuxbefehle nicht zur Verfügung, weshalb es sinnvoll sein kann die Kommandogenerierung abzuschalten.

Auch, wenn diese Option aktiviert ist, können mittels CommandUtility::exec() auch weiterhin Kommandos auf der Shell ausgeführt werden. Davon machen z.B. die diversen ImageMagick Methoden aus TYPO3 Gebrauch.

Wenn ich doch weiterhin Kommandos mit CommandUtility::exec() ausführen kann, auch wenn diese Option aktiviert wurde, wozu brauche ich diese Option dann? In TYPO3 können sogenannte Services registriert werden wie z.B. Services für die Authentifizierung am Backend. Bei der Registrierung solcher Services kann angegeben werden, ob diese exec Funktionalität benötigen oder nicht. Die Überprüfung findet mit CommandUtility::checkCommand() statt. Diese Methode prüft, ob disable_exec_function aktiviert ist. Wenn ja, dann ist exec nicht erlaubt und der Service wird nicht registriert.

## compressionLevel Standard: 0

Wenn Euer Server es unterstützt, könnt Ihr mit dieser Einstellung die CSS und JavaScript Dateien fürs Backend komprimieren und somit den Traffic zum Client erheblich verkleinern und erhaltet somit ein spührbar schnelleres Backend.

Im Apacheserver muss das Modul mod_deflate installiert und in PHP muss die Extension zlib geladen sein. Außerdem benötigt Ihr folgendes Snippet in Eurer .htaccess:

```shell
<FilesMatch "\.js\.gzip$">
  AddType "text/javascript" .gzip
</FilesMatch> 
<FilesMatch "\.css\.gzip$">
  AddType "text/css" .gzip
</FilesMatch>
AddEncoding gzip .gzip
```

Danach empfehle ich das Löschen sämtlicher TYPO3 Caches und das Leeren des Browsercaches. Schaut auch mal mittels der Developertools Eures Browsers in die Anfragekopfzeilen. Gerade dieser Abschnitt ist wichtig:

Accept-Encoding: gzip, deflate, br

Wenn alles geklappt hat, dann sollten alle CSS und JavaScript Dateien nun css.gzip und js.gzip heißen. In den Antwort-Kopfzeilen sollte dieser Header enthalten sein:

## Content-Encoding: gzip

Für compressionLevel ist ein Wert zwischen 0 und 9 erlaubt, wobei 0 keine Komprimierung bedeutet. Das Problem, um hier den richtigen Wert zu finden schaut mal hier vorbei: PHP zlib Kompression. Schaut Euch dort die Kurve weiter unten an. Je höher Ihr die Kompression setzt, desto mehr muss die CPU arbeiten, sprich, desto länger dauert es bis die Datei komprimiert wurde und an den Browser versendet werden kann. TYPO3 empfiehlt hier einen Wert von 5. In der Beispiel .htaccess von TYPO3 wird sogar ein Wert von 9 empfohlen. In diversen Foren, wo es richtig um Last auf den Server geht, werden Werte wie 2 oder 3 empfohlen. Ich sag mal so: Die Dateien werden nur bei Erstaufruf komprimiert, danach liegen die Dateien in typo3temp/assets/compressed/ und stehen somit für alle weiteren Aufrufe aller anderen Clients direkt zum Download zur Verfügung. Allein deshalb empfehle ich einen Wert von 8 oder 9. Den Erstaufruf werdet, mit ein bisschen Glück, eh Ihr machen, alle nachfolgenden Webseitenbesucher erhalten dann schon die komprimierten Dateien.

PHP:zlib.output_compression_level = -1 bedeutet, dass der Server entscheidet, was die Standard Komprimierung ist. Blöd nur, dass im Apache Modul mod_deflate der Wert DeflateCompressionLevel besagt, dass Sie sich auf den Standardwert der PHP:zlib Extension beziehen. Und siehe da, der Standardwert scheint 6 zu sein. Wird im Apache der Wert DeflateCompressionLevel z.B. auf 8 geändert, wirkt sich das sofort auf zlib.output_compression_level aus, der auf Grund des Wertes -1, dann auch 8 ist. Erst ein explizites Setzen von zlib.output_compression_level auf einen Wert zwischen 1-9 würde die Apache Vorgabe komplett ignorieren.

## installToolPassword

Für das Installtool, also den Abschnitt, den ich hier gerade erkläre, benötigt Ihr ein individuelles Passwort, welches Ihr hier nicht ändern könnt. Ja, richtig gelesen: nicht ändern könnt.

Das Passwort, was Ihr hier eintragt, wird exakt so, 1zu1, in die LocalConfiguration.php geschrieben. Ein Passwort wie "hallo" steht so, unverschlüsselt, in der Konfigurationsdatei. Da die Anmeldung am Installtool jedoch eine Verschlüsselung benötigt, werdet Ihr Euch mit einer Passwortänderung hier im Bereich "All Configuration" nicht mehr am Installtool anmelden können.

Bitte, verwendet niemals dieses Feld, um das Installtool zu ändern. Verwendet stattdessen immer nur die Felder im Abschnitt Important actions -> Change install tool password.

## checkStoredRecords Standard: aktiviert

Wenn Ihr Datensätze im Backend speichert, z.B. über das Listen- oder Seitenmodul, dann werden diese Daten mit Hilfe der DataHandler Klasse von TYPO3 gespeichert. Ist checkStoredRecords aktiviert, dann wird nach der Speicherung der soeben gespeicherte Datensatz wieder von der Datenbank geholt und mit den Werten, die zu speichern waren abgeglichen. Falls es Unterschiede gibt, dann wird dies im sys_log protokolliert und im Backend als Fehler ausgegeben:

These fields of record X in table "Y" have not been saved correctly: Z! The values might have changed due to type casting of the database.

Beispiel: Ein Zahlenfeld ist im TCA nicht korrekt als Zahlenfeld konfiguriert worden. Der Kunde trägt 123,56 € ein. Der DataHandler speichert diesen Wert in die Datenbank. Die Datenbank sagt: Hey ich bin vom Typ INT(11) und schneidet alles ab dem Komma ab. Gespeichert wird nur 123. Der DataHandler ließt den Datensatz wieder aus und stellt nun diese Differenz fest: 123,56 € <==> 123 und quittiert dies mit einer Fehlermeldung.

Empfehlung vom Core: Nur wenn die Datenbank im strict Modus läuft könnt Ihr diese Option abschalten.

Meine Empfehlung: Schaltet es ab. Der Datensatz ist zum Zeitpunkt der Fehlermeldung eh schon gespeichert. Dadurch spar Ihr Euch ein SELECT und die Überprüfung beim Speichern.

## checkStoredRecordsLoose Standard: aktiviert

Dieser Wert hat nur Relevant solange auch checkStoredRecords aktiv ist. Bei der Gegenprüfung der zu speichernden Daten, gegen die Daten, wie sie wieder aus der Datenbank kommen könnt Ihr nun mittels checkStoredRecordsLoose entscheiden, ob bei dieser Prüfung ein leerer Text gleichzusetzen ist mit einer 0. Wenn Ihr eine exakte typgleiche Prüfung haben wollt, dann müsst Ihr diesen Wert deaktivieren.

fileDenyPattern \.(php[3-7]?|phpsh|phtml)(\..*)?$|^\.htaccess$
Es gibt Dateien, die sollten aus Sicherheitsgründen nicht im Backend editiert, hochgeladen oder gelöscht werden. Dafür gibt es das fileDenyPattern, ein regulärer Ausdruck, der die Dateiendung einer jeden Datei auf Gültigkeit prüft. Gerade im Bereich filelist und beim Einbinden von TypoScript Dateien mittels INCLUDE_TYPOSCRIPT findet diese Prüfung Einsatz.

## interfaces Standard: backend

Im Prinzip geht es nur um eine Art Weiterleitung nach dem Backend Login. Der Standardwert ist backend und bedeutet, dass Ihr nach erfolgreichem Backend Login ins Backend weitergeleitet werdet. Joa...mehr nicht. Da es sich hierbei um eine kommaseparierte Liste handelt, könnt Ihr auch backend,frontend eintragen, was beim Backend Login zu einer zusätzlichen Selectbox führt, mit der Ihr zwischen einer Weiterleitung, nach erfolgreichem Login, zum Backend oder Frontend wählen könnt.

Die Erklärung im Installtool ist völlig irrsinnig und hat NULL mit dem zu tun, was da steht. "Ihr könnt die Reihenfolge festlegen" ha ha. geil. Ich hab mir die Programmierung angeschaut. Da steht grob: Wenn da ein Wort drin steht, dann mach automatisch eine Weiterleitung ins Backend und wenn da mehr Wörter kommasepariert drin stehen, dann zeige die oben genannte Selectbox an. Ihr könnt da hallo reinschreiben und landet im Backend. Schreibt Ihr da trallala,blabla,juhuu rein, dann erhaltet Ihr die Selectbox. Da schaut Ihr, wah?!?! :-)

## explicitADmode Standard: explicitAllow

In den Einstellungen der Backendgruppen gibt es im Tab Access lists ganz unten den Abschnitt Explicitly allow/deny field values. Wenn explicitADmode auf explizitAllow steht, dann sind grundsätzlich alle Inhaltstypen als auch alle Plugins verboten und müssen explizit erlaubt werden. Ich empfehle diesen Wert auf explizitAllow beizubehalten. Mit dem Wert explizitDeny könnt Ihr das zuvor beschriebene Verhalten tauschen. Es ist dann grundsätzlich alles erlaubt und Ihr müsst explizit jedes Inhaltselement und Plugin deaktivieren, um den Zugriff darauf für die Gruppe zu verbieten. Ich finde explizitDeny deshalb schlecht, denn wenn eine Extension ein neues Element hinzufügt, dann haben sofort alle Benutzer Zugriff darauf und ob das ein gewünschtes Verhalten ist, kann ich mir nur sehr schwer vorstellen.

## flexformForceCDATA Standard: deaktiviert

Es gibt wohl einen Bug in einigen Versionen der PHP Extension libxml, der HTML-Tags in XML entfernt. Wenn Ihr flexformForceCDATA aktiviert, dann werden HTML-Tags bei der Speicherung von XML in CDATA eingewickelt. Alle anderen Werte wie Texte und Zahlen bleiben davon unberührt. Ist der Wert deaktiviert, dann werden HTML-Tags vor der Speicherung nur durch htmlspecialchars gejagt.

## explicitConfirmationOfTranslation Standard: deaktiviert

Welche Speicherbuttons aus TYPO3 kennt Ihr? Speichern, Speichern und schließen und Speichern und Anzeigen. Richtig? Naja...da fehlen noch ein paar.

Folgende Vorraussetzung:

Ihr habt eine Standardsprache, sagen wir Englisch, mit der sys_language_uid 0 und eine Übersetzung, sagen wir Deutsch, mit der sys_language_uid 1. Auf der englischen Seite gibt es ein englisches Inhaltselement und auf der deutschen Seite ein deutsches Inhaltselement, das über den Tab Language mit dem englischen Inhaltselement verknüpft ist. Wenn Ihr jetzt die Überschrift im englischen Inhaltselement verändert, speichert und dann das deutsche Inhaltselement öffnet, dann erscheint unter der Überschrift ein roter Kasten, mit einen Diff was im Englischen, von Text (rot), nach Text (grün) verändert wurde. Ihr könnt in der deutschen Übersetzung ohne eine Änderung getätigt zu haben einfach auf Speichern klicken und die Diffs verschwinden wieder.

Nachdem Ihr nun explicitConfirmationOfTranslation auf 1 gesetzt habt erhaltet Ihr 2 weitere Speicherbuttons: Translation finished, save and close und Translation not finished, save. Ihr könnt zwar auch weiterhin auf Save drücken, jedoch verschwinden nun die roten Diffs nicht mehr wie bei explicitConfirmationOfTranslation = 0. Jedem Übersetzer sollte nun klar sein: Hier muss er nochmal ran. Je nachdem, ob er die Übersetzung teilweise oder vollständig abgeschlossen hat drückt er einen der neuen Buttons. Erst mit Klick auf Translation finished, save and close verschwinden die roten Diffs und die beiden Sprachen sind wieder synchron.

## versionNumberInFilename Standard: deaktiviert

Im deaktivierten Zustand wird allen CSS und JavaScript Dateien des Backends bei dem Versand an den Browser der Zeitstempel der Dateierzeugung als Query-String an den Dateinamen hinten angefügt: bla.css?1234567890. Das hat den Vorteil, dass wenn sich die Datei ändert, sich somit auch der Zeitstempel ändert und dem Browser suggeriert wird: Hey, schmeiß Deine alte Datei mal aus dem Cache und zieh Dir meine neue frische Version.

Soviel zur Theorie. Je nach Netzwerkkonfiguration oder ob Ihr Euch mit Eurem Browser hinter einem Proxy befindet kann es passieren, dass Eurem Netzwerk und/oder Proxy der angefügt Timestamp völlig wurscht ist und somit trotzdem noch die alten CSS und JavaScript Dateien auf dem Cache geladen werden, obwohl neue Dateien auf dem Server zur Verfügung stehen.

Die Lösung sieht so aus, dass TYPO3 diesen Zeitstempel in den Dateinamen reinbauen kann. Die Dateiverlinkung im Quelltext heißt dann: bla.1234567890.css, während die Datei selbst weiterhin bla.css heißt. Dem Browser muss also eine Weiterleitung mitgeteilt werden zu einer Datei ohne diesen Zeitstempel im Namen. Dazu fügt Ihr folgende Zeilen in die .htaccess ein:

```shell
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.+)\.(\d+)\.(php|js|css|png|jpg|gif|gzip)$ $1.$3 [L]
```

Alternativ nehmt Ihr einfach die Original .htaccess vom TYPO3 Source und kommentiert die entsprechenden drei Zeilen dort aus.

## debug Standard: deaktiviert

Wenn debug aktiviert wird, findet keine Zusammenführung aller CSS und JavaScript Dateien in eine große CSS und JS Datei statt. Jede Datei wird einzeln ausgeliefert, was das Debugging mit Hilfe der Browser Developer Tools erheblich vereinfacht. Laut Dokumentation soll damit wohl auch der loginRefresh abgeschaltet werden. Normalerweise würde man ja nach einer Stunde des Nichtstun automatisch vom Backend abgemeldet werden. Mit dieser Option soll das wohl verhindert werden. Ich persönlich kann das jedoch nicht bestätigen.
