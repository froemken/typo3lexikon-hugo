+++
title = "Condition Syntax"
date = 2024-01-09T20:58:10+01:00
aliases = ["condition-syntax.html"]
+++

## Symfony Expression Syntax für TypoScript Conditions

Bei den Symfony Expressions handelt es sich um eine erweiterbare Abfragesprache mit der sich Strukturen wie WENN->DANN->SONST realisieren lassen. Sie sind also hervorragend geeignet für die TypoScript Conditions sowohl im Backend (TSconfig) als auch im Frontend. Beispiel gefällig?

```typo3_typoscript
page = PAGE
page.10 < styles.content.get

[page["uid"] == (12 / 4 / 3)]
page.10 >
page.10 = TEXT
page.10.value = Hallo
[END]
```

Allein dieses Beispiel zeigt, was früher nicht möglich war. Statt page|uid wird hier auf Arrays zugegriffen, wie wir es aus PHP her kennen. Außerdem seht ihr, dass nun sogar Berechnungen möglich sind. Von Haus aus. Der Wahnsinn. Die Ausgabe Hallo im oberen Beispiel erfolgt also nur, wenn wir uns auf der Seite mit der UID 1 befinden. Im Folgenden eine hoffentlich umfassende und vollständige Dokumentation aller Conditions, die ich finden konnte.

## Variablen

### applicationContext

Liefert üblicherweise einen dieser 3 Werte: Development, Production, Testing. Eher selten, aber trotzdem gültige Werte wären auch Development/Stage1

Beispiel: `[applicationContext == "Development"]`

### page

Zugriff auf alle Spalten der aktuellen Seite

Beispiel: `[page["pid"] == 2]`

## Zugriff auf TS-Konstanten

Beispiel, wenn Konstante eine Zahl ist: `[{$pid} == 12]`

Beispiel, wenn Konstante ein Text ist: `["{$style.color}" == "blue"]`

### tree

Zugriff auf den Seitenbaum und die Rootline

### tree.level

Wahr, wenn Seite sich auf Ebene 0 befindet

Beispiel: `[tree.level == 0]`

### tree.rootLine

Alle Seiten in der Rootline als Array

Beispiel: `[tree.rootLine[0]["uid"] == 1]`

### tree.rootLineIds

Enthält nur die Seiten-UIDs als Array

Wahr, wenn Seite mit UID 2 in den rootLineIds enthalten ist

Beispiel: `[2 in tree.rootLineIds]`

### backend

Bis TYPO3 10.4.0 bzw. TYPO3 9.5.15 sind diese Variablen nur dann gültig, wenn ihr euch im Backend befindet. Also nur in TSConfig gültig. Seit TYPO3 10.4.1 bzw. 9.5.16 können diese Variablen auch im Frontend Kontext verwendet werden:

#### backend.user

Hierbei handelt es sich nur um einen Container (stdClass) für die nachfolgenden Variablen

#### backend.user.isAdmin

Wahr, wenn aktuell angemeldeter BE-User ein Admin ist

Beispiel: `[backend.user.isAdmin]`

#### backend.user.isLoggedIn

Wahr, wenn ein BE-User angemeldet ist

Beispiel: `[backend.user.isLoggedIn]`

#### backend.user.userId

Wahr, wenn die UID des BE-Users der Vorgabe entspricht

Beispiel: `[backend.user.userId == 5]`

#### backend.user.userGroupList

Enthält alle Gruppen-UIDs, die dem BE-Benutzer zugeordnet sind als String. Als String! WHY!!!

Beispiel: `[like(","~backend.user.userGroupList~",", "*,1,*")]`

### frontend

#### frontend.user

Hierbei handelt es sich nur um einen Container (stdClass) für die nachfolgenden Variablen

#### frontend.user.isLoggedIn

Wahr, wenn ein FE-User angemeldet ist

Beispiel: `[frontend.user.isLoggedIn]`

#### frontend.user.userId

Wahr, wenn die UID des FE-Users der Vorgabe entspricht

Beispiel: `[frontend.user.userId == 5]`

#### frontend.user.userGroupList

Enthält alle Gruppen-UIDs, die dem FE-Benutzer zugeordnet sind als String. Als String! WHY!!!

Beispiel: `[like(","~frontend.user.userGroupList~",", "*,1,*")]`

### typo3

Auch wenn diese Variable vielversprechend klingt, so beinhaltet sie doch recht wenig Informationen

#### typo3.version

Die vollständige TYPO3-Version

Beispiel: `[typo3.version == "9.5.5"]`

#### typo3.branch

Beispiel: `[typo3.branch == "9.5"]`

#### typo3.devIpMask

Hier empfehle ich jedoch eher die Funktion ip zu verwenden

Beispiel: `[typo3.devIpMask == "172.18.0.6"]`

## Funktionen für FE und BE

Funktionen werden im Gegensatz zu Variablen aufgerufen/ausgeführt und liefern die angeforderten Werte üblicherweise als Array zurück (Ja, es gibt auch Ausnahmen). Funktionen erkennt man daran, dass diese mit () enden.

### request

Funktionalität wird realisiert über die PHP-Klasse: TYPO3\CMS\Core\ExpressionLanguage\RequestWrapper

#### request.getQueryParams()

Mit dieser Funktion habt ihr Zugriff auf jeden einzelnen GET-Parameter eines Requests. Diese werden als Array zur Verfügung gestellt.

Aus dem QueryString:

`route=%2Fajax%2Fsystem-information%2Frender&token=5c53e9b715362e7b0c3275848068133b89bbed77&skipSessionUpdate=1`

wird somit folgendes Array:

```
Key: route
Value: /ajax/system-information/render
Key: token
Value: 5c53e9b715362e7b0c3275848068133b89bbed77
Key: skipSessionUpdate
Value: 1
```

Beispiel: `[request.getQueryParams()["no_cache"] == "1"]`

#### request.getParsedBody()

Wenn der Content-Type des Requests application/x-www-form-urlencoded oder multipart/form-data ist, dann erhaltet ihr hier die Werte aus $_POST, ansonsten bleibt dieser Wert schlicht null.

#### request.getHeaders()

Mit dieser Funktion kommt ihr an alle Headerdaten des Requests dran. Meiner Meinung nach etwas umständlich, weil die Werte selbst auch als Array zur Verfügung stehen. Statt accept='*/*' stehen die Werte somit als accept=['*/*'] zur Verfügung. Im Folgenden nur ein paar Beispiele, da die Headerdaten je nach Client sehr unterschiedlich sein können:

```
accept-language: ['de-DE,de;q=0.9']
accept-encoding: ['gzip, deflate']
origin: ['https://www.typo3lexikon.de']
x-forwarded-proto: ['https']
host: www.typo3lexikon.de
...
```

Beispiel: `[request.getHeaders()["host"][0] == "www.typo3lexikon.de"]`

Nicht die 0 vergessen, da die Werte alle Arrays sind.

#### request.getCookieParams()

Diese Funktion liefert Euch alle aktuellen Cookies als Array zurück. Im Folgenden wieder nur ein paar Beispiele:

## be_lastLoginProvider

Beispiel: 1433416747

## pmaCookieVer

Beispiel: 5

## PHPSESSID

Die aktuelle Session ID

## be_typo_user

Hash-Wert, um den aktuellen BE-User in der DB zu finden

Beispiel: `[request.getCookieParams()["pmaCookieVer"] == 5]`

## request.getNormalizedParams()

Diese Funktion liefert das NormalizedParams-Objekt zurück, das ganz bestimmte Server-Parameter in aufbereiteter Form enthält. Dazu zählen folgende Methoden:

### getHttpHost()

Beispiel: www.typo3lexikon.de

### isHttps()

Verbindung wurde über SSL hergestellt? Boolean: true/false

### getRequestHost()

Beispiel: www.typo3lexikon.de

### getRequestHostOnly()

Beispiel: www.typo3lexikon.de

### getRequestPort()

Port der Verbindung. Kann 0 oder auch 80 sein

### getScriptName()

Beispiel: /typo3/index.php

### getRequestUri()

Beispiel: /typo3/index.php?route=%2Fajax%2Fsystem-information%2Frender

### getRequestUrl()

Beispiel: https://www.typo3lexikon.de/typo3/index.php?route=%2Fajax%2Fsystem-information%2Frender

### getRequestScript()

Beispiel: https://www.typo3lexikon.de/typo3/index.php

### getRequestDir()

Beispiel: https://www.typo3lexikon.de/typo3/

### isBehindReverseProxy()

Boolean: true/false

### getRemoteAddress()

IP-Adresse des Clients. Beispiel Docker: 172.18.0.6

### getScripFileName()

Beispiel: /var/www/html/public/typo3/index.php

### getDocumentRoot()

Beispiel: /var/www/html/public

### getSiteUrl()

Beispiel: www.typo3lexikon.de

### getSitePath()

Beispiel: /

### getSiteScript()

Beispiel: typo3/index.php?route=%2Fajax%2Fsystem-information%2Frender

### getPathInfo()

Ist bei mir leer gewesen

### getHttpReferer()

Zuvor besuchte Seite. Beispiel: typo395.ddev.local/typo3/index.php

### getHttpUserAgent()

Beispiel: Mozilla/5.0 (X11; Linux x86_64) Chrome/73.0.3683.86 Safari/537.36

### getHttpAcceptEncoding()

Beispiel: gzip, deflate

### getHttpAcceptLanguage()

Beispiel: de-DE,de;q=0.9

### getRemoteHost()

Name des Client-Rechners. Wird wohl meist leer sein.

### getQueryString()

Beispiel: route=%2Fajax%2Fsystem-information%2Frender

Beispiel: `[request.getNormalizedParams().getHttpHost() == "typo395.ddev.local"]`

## date

Im Hintergrund wird hier PHP-seitig "grob" new \DateTime('now')->format('xxx') aufgerufen. Ihr könnt für xxx alle date-Formate verwenden.

Beispiel: `[date("d.m.Y") == "05.04.2019"]`

Gültig, wenn das aktuelle Datum auf den 05.04.2019 trifft.

## like

Diese Funktion ruft im Hintergrund TYPO3\CMS\Core\Utility\StringUtility::searchStringWildcard() auf. Somit habt ihr folgende Möglichkeiten:

Teilwortsuche mit Sternchen:

`[like("fooBarBaz", "*Bar*")]`

Auslassen vereinzelter Buchstaben:

`[like("fooBarBaz", "f?oBa?Baz")]`

Reguläre Ausdrücke sind nur mit startendem und endendem / möglich:

`[like("fooBarBaz", "/f[o]{2,2}[aBrz]+/")]`

Nicht möglich sind Vergleiche ohne * oder ?

`[like("fooBarBaz", "Bar")] => Ergibt false`

## ip

Überprüft, ob die Client-IP-Adresse (REMOTE_ADDR) vollständig oder zumindest zu Teilen der Vorgabe entspricht:

`[ip("172.18.*")]`

Alternativ ist die Angabe "devIP" erlaubt, um die Client-IP-Adresse gegen den Wert devIPMask aus dem Installtool zu prüfen:

`[ip("devIP")]`

## compatVersion

Diese Funktion prüft, ob der aktuelle TYPO3 Branch (ja Branch (Konstante: TYPO3_branch) z.B. 9.5, nicht Version z.B. 9.5.4) größer oder gleich Eurer Vorgabe entspricht.

`[compatVersion("9.5")] entspricht auch [compatVersion("9.5.0")]`

Wie schon gesagt: Hier wird auf den Branch geprüft. Angaben wie folgt sind nicht möglich:

`[compatVersion("9.5.1")]`

Die Angabe 9.5.0 funktioniert nur, weil diese intern in 9005000 umgewandelt wird und somit den gleichen Wert wie 9.5 enthält.

## loginUser

Diese Funktion ist Kontext abhängig. Bedeutet, wenn die Bedingung sich in TSConfig befindet, wird auf einen bestimmten angemeldeten BE-User geprüft. Im TypoScript wird auf angemeldete FE-User geprüft. Es ist somit nicht möglich im TypoScript (Setup) zu prüfen, ob ein BE-User angemeldet ist.

Wahr, wenn irgendein Benutzer angemeldet ist:

`[loginUser("*")]`

Wahr, wenn Benutzer mit der UID 3 angemeldet ist:

`[loginUser("3")]`

Wahr, für die 3 Benutzer mit der UID 3, 7 und 9

`[loginUser("3,7,9")]`

Wahr, wenn kein Benutzer angemeldet ist:

`[loginUser("*") == false]`

## getTSFE

Eine schier unglaubliche Funktion, die Euch Zugriff auf das TypoScriptFrontendController-Objekt gibt. Ich habe früher mal ein var_dump() auf dieses Objekt gemacht und ausgedruckt. Ich glaub es waren um die 24 Din A4-Seiten. Also bitte vergebt mir, wenn ich Euch hier nicht alles dazu erkläre. Hier ein paar Beispiele:

Wahr, wenn wir uns auf Seite mit der UID 1 befinden:

`[getTSFE().page["uid"] == 1]` Besser wäre jedoch die page-Variable zu verwenden.

Wahr, wenn der aktuelle Benutzer froemken heißt. Wenn kein Benutzer angemeldet ist, ist fe_user null, womit die eigentliche Abfrage fe_user.user einen Error werfen würde.

`[getTSFE().fe_user && getTSFE.fe_user.user["username"] == "froemken"]`

Wahr, wenn ein Backend-User angemeldet ist. Das klappt auch im Frontend. Ist doch der Hammer: Ihr könnt nicht nur die Eigenschaften eines Objektes aufrufen, sondern auch die enthaltenen public Methoden.

`[getTSFE() && getTSFE().isBackendUserLoggedIn()]`

## getenv

Mit dieser Funktion könnt ihr auf die Umgebungsvariablen des Servers zugreifen. Da dies von Server zu Server sehr stark variieren kann, kann ich Euch hier nicht alle Umgebungsvariablen aufführen. Baut Euch eine env.php-Datei mit einem var_dump(getenv()), um alle Umgebungsvariablen aufführen zu lassen. Im Folgenden wieder ein Beispiel:

Wenn PHP:getenv() ohne Parameter aufgerufen wird, dann liefert es alle Umgebungsvariablen als Array zurück, weshalb Ihr in diesem Beispiel auch als Array an den Wert kommt:

`[getenv()["VIRTUAL_HOST"] == "www.typo3lexikon.de"]`

Etwas besser wäre es den gesuchten Wert direkt an getenv() zu übergeben:

`[getenv("VIRTUAL_HOST") == "www.typo3lexikon.de"]`

## usergroup

Mit dieser Funktion könnt ihr prüfen, ob ein aktuell angemeldeter Benutzer (Kontext beachten, kann BE oder FE sein) in der Liste von euch angegebenen Benutzergruppen UIDs ist. Stark finde ich, dass hier auch alle Sub- und Sub-Sub-Gruppen gleich mit überprüft werden.

Alle Benutzergruppen sind erlaubt:

`[usergroup("*")]`

Benutzer der Benutzergruppe 12 sind erlaubt:

`[usergroup("12")]`

Benutzer in den Benutzergruppen 12, 15 und 18 sind erlaubt:

`[usergroup("12,15,18")]`

## Funktionen nur für das Frontend

Folgende Funktionen sind nur für das Frontend gedacht

### session

Das hat ne Weile gedauert, bis ich das begriffen habe. Also: Im TSFE gibt es das Objekt FrontendUserAuthentication, das sowohl für angemeldete Benutzer als auch für einfache Webseitenbesucher immer gefüllt ist. Mit Hilfe diesem Authentication-Objekt können Session-Daten hinterlegt und auch wieder abgerufen werden. Da es dem Entwickler überlassen ist, ob er in solch eine Session nun ein Array, einen String oder gleich ein ganzes Objekt ablegt, benötigt es eine besondere Schreibweise, um Datentyp unabhängig auf einen Wert zugreifen zu können. Beispiel: Wir haben mit setSessionData('tsfe', $TSFE) das komplett TSFE in die Session mit dem Key tsfe geschrieben. Das ist ein Beispiel! Bitte nicht nachmachen!

`[session("tsfe|fe_user|user|username") == "froemken"]`

Die Pipe ist hier der besondere Trenner, der Datentyp unabhängig agieren kann. Der erste Wert ist der Session-Key. Die restlichen Keys werden nun dazu verwendet den immer tiefer verschachtelten Wert username auszulesen.

### site

Seit TYPO3 9 gibt es das Site-Modul. Um an die dortige Konfiguration dranzukommen, benötigt ihr diese Funktion. Im Gegensatz zu anderen Objekten, könnt ihr keine public Eigenschaften aufrufen. Alle Eigenschaften werden über Getter ausgeliefert. Aus identifier wird demnach intern getIdentifier().

- site("identifier")
  - Beispiel: `[site("identifier") == "typo395"]`
- site("base")
  - Die komplette Base-URI wird in ihre Einzelteile aufgesplittet. TYPO3\CMS\Core\Http\Uri
- site("base").getAuthority()
  - site("base").getUserInfo()
  - site("base").getHost()
    - Beispiel: `[site("base").getHost() == "www.typo3lexikon.de"]`
  - site("base").getPort()
    - Wenn nicht explizit angegeben, dann null
  - site("base").getPath()
    - Beispiel: `[site("base").getPath() == "/"]`
  - site("base").getQuery()
  - site("base").getFragment()
- site("rootPageId")
  - Beispiel: `[site("rootPageId") == 1]`
- site("languages") ==> Nur aktive Sprachen
  - Zugriff auf alle konfigurierten Sprachen. Rückgabetyp ist Array. Jedes Array-Element ist vom Typ TYPO3\CMS\Core\Site\Entity\SiteLanguage
  - Alternativ kann auch mit site("languages")[0].toArray()["direction"] auf die Eigenschaften zugegriffen werden.
  - site("languages")[0].getLanguageId()
  - site("languages")[0].getLocale()
  - site("languages")[0].getBase()
    - Siehe oben
  - site("languages")[0].getTitle()
  - site("languages")[0].getNavigationTitle()
  - site("languages")[0].getFlagIdentifier()
  - site("languages")[0].getTypo3Language()
  - site("languages")[0].getTwoLetterIsoCode()
  - site("languages")[0].getHreflang()
  - site("languages")[0].getDirection()
    - Beispiel: [site("languages")[0].getDirection() == "ltr"]
  - site("languages")[0].enabled()
  - site("languages")[0].getFallbackType()
  - site("languages")[0].getFallbackLanguageIds()
- site("allLanguages") ==> Zugriff auf alle Sprachen
  - Eigenschaften siehe oben
- site("defaultLanguage")
  - Eigenschaften siehe oben
- site("configuration") Datentyp Array
  - site("configuration")["base"]
    - Beispiel: [site("configuration")["base"] == "/"]
  - site("configuration")["direction"]
    - Beispiel: [site("configuration")["direction"] == "ltr"]
  - site("configuration")["enabled"]
    - Beispiel: [site("configuration")["enabled"] == true]
  - site("configuration")["flag"]
    - Beispiel: [site("configuration")["flag"] == "flags-en-us-gb"]
  - site("configuration")["hreflang"]
    - Beispiel: [site("configuration")["hreflang"] == "en-US"]
  - site("configuration")["iso-639-1"]
    - Beispiel: [site("configuration")["iso-639-1"] == "en"]
  - site("configuration")["languageId"]
    - Beispiel: [site("configuration")["languageId"] == "0"]
  - site("configuration")["locale"]
    - Beispiel: [site("configuration")["locale"] == "en_US.UTF-8"]
  - site("configuration")["navigationTitle"]
    - Beispiel: [site("configuration")["navigationTitle"] == "English"]
  - site("configuration")["title"]
    - Beispiel: [site("configuration")["title"] == "English"]
  - site("configuration")["typo3Language"]
    - Beispiel: [site("configuration")["typo3Language"] == "default"]

## siteLanguage

Diese Funktion analysiert den aktuellen Request und liefert Euch die entsprechend gültige SiteLanguage zurück. Alle Methoden innerhalb von dem SiteLanguage Objekte, die mit get beginnen können abgefragt werden. Das "get" müsst ihr jedoch weglassen. Statt dem Methodennamen getTitle müsst ihr nur title angeben.

- siteLanguage("languageId")
- siteLanguage("locale")
- siteLanguage("base")
- siteLanguage("title")
- siteLanguage("navigationTitle")
- siteLanguage("flagIdentifier")
- siteLanguage("typo3Language")
- siteLanguage("twoLetterIsoCode")
- siteLanguage("hreflang")
- siteLanguage("direction")
- siteLanguage("fallbackType")
- siteLanguage("fallbackLanguageIds")
