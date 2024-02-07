+++
title = "SYS"
date = 2024-01-09T20:47:58+01:00
aliases = ["typo3-conf-vars-sys.html"]
+++

## $GLOBALS['TYPO3_CONF_VARS']['SYS']

### cookieDomain

Wenn Ihr Euch anmeldet, egal ob Frontend oder Backend, dann wird eine Session erzeugt. Mittels dieser Session bleibt Ihr für eine gewisse Zeit angemeldet. Diese Session wird mit Hilfe eines Cookies von Seite zu Seite übertragen und mit jedem Request überprüft.

Wenn cookieDomain nicht gesetzt ist bzw. leer ist, dann wird das Session Cookie an die aktuelle Domain gebunden und zwar nur an diese eine Domain. Für Subdomains ist das Cookie nicht gültig!

Wenn Ihr in Eurem TYPO3 System mehrere Subdomains erstellt habt wie z.B. test1.example.com und test2.example.com, dann könnt Ihr Euch zwar über beide Domains am TYPO3 Backend anmelden, aber die Anmeldung gilt nur pro Domain. Versucht Ihr das Backend der anderen Domain aufzurufen müsst Ihr Euch ebenfalls anmelden. In größeren TYPO3 Systemen kann dies enorm nerven. Z.B. Wenn Ihr als Redakteur deaktivierte Seiten des Seitenbaumes der anderen Domain im Frontend anzeigen lassen wollt. In Bezug auf unsere 2 Beispieldomains hilft es den Wert cookieDomain auf .example.com (inkl. dem Punkt am Anfang, um auch ältere Browser zu unterstützen) zu setzen. Damit ist das Session Cookie nun für example.com und alle seine Subdomains gültig. Habt Ihr Euch an einer Domain angemeldet, dann könnt Ihr sogar die URL auf die andere Domain ändern und bleibt trotzdem im Backend/Frontend angemeldet. Versucht es mal.

Nach einer Änderung der cookieDomain bitte den Systemcache leeren und alle Domain bezogenen Cookies im Browser löschen.

cookieDomain funktioniert nicht zwischen unterschiedlichen Domains wie obi.de und aldi.de. Cookies sind immer nur an eine Domain und Ihre Subdomains gebunden. Um mehrere Domains in cookieDomain zu hinterlegen, könnt Ihr reguläre Ausdrücke verwenden: /(obi\.de|aldi\.de)/ Der reguläre Ausdruck MUSS mit Slash beginnen! Wenn die aktuelle Domain zu einer dieser Domain passt, wird die aktuelle Domain für das Cookie verwendet. Nochmal: Auch durch die Angabe mehrerer Domains mittels regülärer Ausdrücke in cookieDomain wird immer nur die EINE aktuelle Domain für das Cookie verwendet!

Der Wert cookieDomain aus dem Abschnitt SYS kann mit den cookieDomain Werten aus den Abschnitten BE und/oder FE individuell überschrieben werden.

Ihr habt eine TYPO3 Instanz für test1.example.com und eine andere TYPO3 Instanz mit einer anderen Datenbank auf test2.example.com? Einfache Antwort: Hier hilft Euch cookieDomain nicht. Es ist nicht möglich. Schwierige Lösung: Schaut Euch mal mit Hilfe der Developertools Eures Browsers den Request an. In dem Session Cookie be_typo_user steht eine Session ID, die Ihr nach erfolgreicher Anmeldung in der Tabelle be_sessions wiederfinden solltet. Die Daten der be_session müssen auf der anderen TYPO3 Instanz importiert werden und das ist noch nicht alles. Ihr müsst auch sicherstellen, dass die Benutzer UIDs exakt die gleichen sind. Nur dann seid Ihr wieder automatisch mit einem Login für beide Instanzen gleichzeitig angemeldet.

### fileCreateMask Standard: 0664

Bei einigen Servern kann es vorkommen, dass die Berechtigungen von Dateien, die per FTP hochgeladen wurden eine andere Berechtigung aufweisen, die vom Webserver benötigt wird. So erhält z.B. der FTP-Benutzer Schreibrechte auf die Datei, wärend der Webserver dann nur noch Leseberechtigung auf die Datei hat.

Damit die Berechtigungen sowohl für den Webserver als auch für den SSH/FTP Benutzer gleich sind, gibt es die Option fileCreateMask. Nach dem Hochladen oder neu Erstellen von Dateien aus TYPO3 heraus, werden seitens TYPO3 die Berechtigungen direkt an diesen Wert hier angepasst.

### folderCreateMask Standard: 2775

Das Gleiche wie bei fileCreateMask mit dem einzigen Unterschied, dass sich dieser Wert hier auf Verzeichnisse bezieht.

### createGroup

In der Option fileCreateMask habe ich die Problematik bereits erklärt. Wenn über TYPO3 eine Datei hochgeladen oder neu erstellt wird, dann könnt Ihr mit createGroup der neuen Datei direkt einer anderen Systemgruppe zugewiesen. Somit kann wieder gewährleistet werden, dass Webserver und SSH/FTP-Benutzer wieder einen einheitlichen Zugriff erhalten.

### sitename

Hierbei handelt es sich nicht um einen Seitennamen bzw. Seitentitel eine Webseite, sondern der kompletten TYPO3 Instanz. Unabhängig davon wie viele Webseiten Ihr mit dieser Instanz realisiert habt. TYPO3 greift an verschiedenen Stellen immer mal wieder auf diesen Seitennamen zurück. Zum Beispiel im Seitenbaum, ganz oben: Die Seite mit der PID:0 enthält den hier konfigurierten Seitennamen.

### encryptionKey

Der hier eingetragene Text wird für alle Arten von Verschlüsselungen in TYPO3 verwendet. Dazu gehört z.B. der cHash aus den URLs oder auch authCodes, die über GeneralUtility erzeugt werden können. Wenn Ihr diesen Text ändert, werden sämtliche Verschlüsselungen ungültig. Wenn Ihr diesen Wert ändert, müsst Ihr alle Caches zuvor löschen.

### cookieDomain

Gebt hier die (Sub)Domain an, für die das Frontend Session Cookie gültig sein soll.

Siehe $GLOBALS['TYPO3_CONF_VARS']['SYS']['cookieDomain'] für weitere Informationen.

### cookieSecure Standard: 0

Im Groben geht es darum, ob Cookies nur bei einer sicheren Verbindung versendet werden dürfen oder nicht. Schaut Euch dazu auch den 6ten Parameter von PHP:setcookie an.

0: Völlig egal, ob die Webseite über HTTP oder HTTPS ausgeliefert wird, das Cookie wird immer gesendet, da hier der 6te Parameter von PHP:setcookie deaktiviert bleibt.

1: Cookies werden nur dann gesendet, wenn die Webseite über HTTPS übertragen wird. Ein Aufruf der Webseite über http resultiert in folgende Fehlermeldung:

Cookie was not set since HTTPS was forced in $TYPO3_CONF_VARS[SYS][cookieSecure]
2: Bei HTTP Aufrufen bleibt der Security-Parameter von setcookie deaktiviert. Erst bei HTTPS Übertragungen wird dieser 6te Parameter aktiviert.

### doNotCheckReferer Standard: deaktiviert

Dazu muss man erstmal wissen, was der Referer ist. Geht dazu mal auf irgendeine Webseite und öffnet die Developer Tools Eures Browsers. Wählt die HTML oder PHP Datei in den Netzwerktools auf und schaut mal in die Anfrage Kopfzeilen. Beim Erstaufruf der Seite wird der Referer mit der aktuell aufgerufenen Seite befüllt. Navigiert nun ein bisschen über die Webseite. Mit jedem Aufruf einer Seite wird der Referer mit der zuvor besuchten Webseite befüllt.

Je nach Netzwerkkonfiguration oder Proxyverwendung kann es passieren, dass dieser Referer aus den Anfrage-Kopfzeilen entfernt wird. Gerade beim Login ins und beim Arbeiten im Backend prüft TYPO3 durchgängig, ob der Host der vorherigen Webseite mit dem Host der aktuellen Webseite übereinstimmt. Wenn dann auf einmal der Referer fehlt, dann wird der Benutzer sofort ausgeloggt und diese Fehlermeldung erscheint:

TYPO3 Fatal Error: Error: This host address HOST and the referer host REF_HOST mismatches! It is possible that the environment variable HTTP_REFERER is not passed to the script because of a proxy. The site administrator can disable this check in the "All Configuration" section of the Install Tool (flag: TYPO3_CONF_VARS[SYS][doNotCheckReferer]).
In diesem Fall müsst Ihr dieses Sicherheitsmerkmal deaktivieren und doNotCheckReferer aktivieren.

### recursiveDomainSearch Standard: deaktiviert

Wenn Ihr mit Eurer TYPO3 Seite mehrere Webseiten mit unterschiedlichen Domains erstellt habt, dann müsst Ihr der Startseite einer jeden Webseite einen sys_domain Datensatz zuordnet, damit TYPO3 beim Webseitenaufruf weiß auf welche Startseite mit welcher UID er springen muss.

Es kommt durchaus vor, dass eine dieser Webseiten mit mehreren Domains aufrufbar sein soll. Im deaktivierten Zustand dieser Option müsst Ihr jede Domain und jede Subdomain als sys_domain Datensatz anlegen. Das kann bei größeren Webseiten schon mal viel Arbeit bedeuten.

Mit Aktivierung dieser Option reicht es eine Domain wie example.com anzulegen. Wenn dann eine Anfrage auf jeans.klamotten.example.com kommt, dann sucht TYPO3 rekursiv erst nach jeans.klamotten.example.com. Gibt es dafür keinen sys_domain Eintrag, wird nach klamotten.example.com gesucht. Gibt es auch hierfür keinen Eintrag, erfolgt eine Prüfung auf example.com. Dieser sys_domain Eintrag ist vorhanden und TYPO3 kann die UID der Startseite herausfinden und weiß somit, welcher Seitenbaum für diese Domain zuständig ist.

### trustedHostsPattern Standard: SERVER_NAME

Ich bin kein Sicherheitsexperte, aber ich versuch es dennoch zu erklären.

Öffnet mal einen neuen Browser Tab inkl. Developer Tools (Tab: Netzwerk) und geht dann auf die Seite www.obi.de. Wenn Ihr nun den Eintrag / wählt und Euch die Anfrage Kopfzeilen anschaut, dann seht Ihr unter anderem den Eintrag

Host: www.obi.de
Dieser Eintrag, der von Eurem Client kommt, wurde bis TYPO3 6.2.2 für das Generieren von Links mit absoluten Pfaden (also URL inkl. ...) verwendet. Es gibt Browser Plugins mit denen sich Anfrage Kopfzeilen wie der Host-Eintrag modifizieren lassen. Auch über den Shell Befehl curl kann dieser Host-Eintrag verändert werden. Eine solche Änderung hätte sich damals sofort auf die Linkgenerierung von TYPO3 ausgewirkt und man könnte Webseitenbesucher damit auf eine infizierte Webseite weiterleiten, um z.B. irgendwelche Passwörter oder sowas abzufragen.

Seit TYPO3 6.2.3 gibt es nun diese Option trustedHostsPattern und der Standartwert ist SERVER_NAME. Mit diesem Wert wird nun der Host aus den Servervariablen mit dem Host aus den Client Kopfzeilen verglichen. Damit das funktioniert, muss ein Name für den Virtual Host in der Serverkonfiguration hinterlegt sein. Ich sag mal vorsichtig, dass das bei den meisten Hostern wohl der Fall sein wird.

In den seltenen Fällen, wo dieser Name nicht gesetzt ist oder es anderweitig nicht funktioniert, könnt Ihr einen regulären Ausdruck in diese Einstellung einbauen, der dann diese Überprüfung übernimmt.

Ich habe es zwar geschafft den Host in den Anfrage Kopfzeilen zu verändern, aber die angefragten Server gehen auf solche Änderungen überhaupt nicht ein. Da muss im Hintergrund noch eine ganze Ecke mehr passieren wie zuvoriges SYN Flooding oder bei Varnish Caches das Senden von mehreren Host Headern hintereinander.

Es gibt noch die Möglichkeit diesen Wert auf .* (Punkt und Sternchen) zu setzen. Damit sind wie früher wieder alle Hosts erlaubt, was ein erhebliches Sicherheitsrisiko darstellt. Ich rate entschieden davon ab, diese Einstellung auf .* zu setzen.
