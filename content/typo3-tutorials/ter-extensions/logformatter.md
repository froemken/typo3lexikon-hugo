+++
title = "logformatter"
date = 2025-10-19T13:03:22+01:00
aliases = ["logformatter.html"]
+++

Habt ihr euch schon mal auf der Shell mittels `less`, `cat`, `vim` oder `nano` durch die Log-Dateien von TYPO3 gequält? Euch interessieren nur die Fehler und diese sind zwischen den tausenden Info- und Deprecation-Nachrichten nahezu versteckt? Hinzu kommen die ganzen Meta-Informationen, die zwar wichtig für den individuellen Fehler sind, aber für eine gute Übersicht einfach nur hinderlich sind? Abhilfe schafft hier die Extension [logformatter](https://extensions.typo3.org/extension/logformatter) von [Frank Berger](https://github.com/foppelfb) aus dem Hause [Sudhaus7](https://sudhaus7.de/).

## Installation

{{% badge style="green" icon="angle-double-up" %}}TYPO3 9.5{{% /badge %}}

```shell
composer require sudhaus7/logformatter
```

## Erstkontakt

Im Gegensatz zu anderen Extensions registriert sich diese Extension hier direkt auf Root-Ebene. Also nix mit Doppelpunkten wie `log:format` oder so. Folgender Befehl geht alle Log-Dateien von TYPO3 durch und liefert jede Meldung, jedoch ohne Meta-Informationen, zurück.

```shell
vendor/bin/typo3 logformatter
```

Hier mal eine selbst provozierte Fehlermeldung aus der Log-Datei. Ich habe sie hier in vollständiger Länge reinkopiert, damit ihr zumindest ein bisschen ein Gefühl dafür bekommt, mit was für Informationen wir Integratoren und Admins konfrontiert werden. Ich denke, es zeigt ausgezeichnet, wie unübersichtlich so eine Log-Datei sein kann:

```shell
Sat, 11 Oct 2025 02:08:02 +0200 [CRITICAL] request="eeadd85234d82" component="Networkteam.SentryClient.DebugExceptionHandler": Core: Exception handler (WEB: FE): TypeError, code #0, file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php, line 16: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506- TypeError: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506, in file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php:16 - {"mode":"WEB","application_mode":"FE","exception_class":"TypeError","exception_code":0,"file":"/var/www/html/vendor/networkteam/sentry-client/Classes/Client.php","line":16,"message":"Networkteam\\SentryClient\\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506","request_url":"https://typo3134.ddev.site/","exception":"TypeError: Networkteam\\SentryClient\\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506 and defined in /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php:16\nStack trace:\n#0 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(4506): Networkteam\\SentryClient\\Client::captureException()\n#1 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/UserContentObject.php(44): TYPO3\\CMS\\Frontend\\ContentObject\\ContentObjectRenderer->callUserFunction()\n#2 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(657): TYPO3\\CMS\\Frontend\\ContentObject\\UserContentObject->render()\n#3 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(604): TYPO3\\CMS\\Frontend\\ContentObject\\ContentObjectRenderer->render()\n#4 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(573): TYPO3\\CMS\\Frontend\\ContentObject\\ContentObjectRenderer->cObjGetSingle()\n#5 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(553): TYPO3\\CMS\\Frontend\\ContentObject\\ContentObjectRenderer->cObjGetSeparated()\n#6 /var/www/html/vendor/typo3/cms-frontend/Classes/Http/RequestHandler.php(227): TYPO3\\CMS\\Frontend\\ContentObject\\ContentObjectRenderer->cObjGet()\n#7 /var/www/html/vendor/typo3/cms-frontend/Classes/Http/RequestHandler.php(196): TYPO3\\CMS\\Frontend\\Http\\RequestHandler->generatePageBodyContent()\n#8 /var/www/html/vendor/typo3/cms-frontend/Classes/Http/RequestHandler.php(137): TYPO3\\CMS\\Frontend\\Http\\RequestHandler->generatePageContent()\n#9 /var/www/html/vendor/sudhaus7/logformatter/Classes/MiddleWares/LogrequesturlMiddleWare.php(40): TYPO3\\CMS\\Frontend\\Http\\RequestHandler->handle()\n#10 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): Sudhaus7\\Logformatter\\MiddleWares\\LogrequesturlMiddleWare->process()\n#11 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/ContentLengthResponseHeader.php(41): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#12 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\ContentLengthResponseHeader->process()\n#13 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/ShortcutAndMountPointRedirect.php(94): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#14 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\ShortcutAndMountPointRedirect->process()\n#15 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/ResponsePropagation.php(34): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#16 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Core\\Middleware\\ResponsePropagation->process()\n#17 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/OutputCompression.php(44): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#18 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\OutputCompression->process()\n#19 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/ContentSecurityPolicyHeaders.php(58): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#20 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\ContentSecurityPolicyHeaders->process()\n#21 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/PrepareTypoScriptFrontendRendering.php(186): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#22 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\PrepareTypoScriptFrontendRendering->process()\n#23 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/CacheTimeout.php(34): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#24 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\CacheTimeout->process()\n#25 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/TypoScriptFrontendInitialization.php(126): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#26 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\TypoScriptFrontendInitialization->process()\n#27 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/PageArgumentValidator.php(105): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#28 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\PageArgumentValidator->process()\n#29 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/PreviewSimulator.php(87): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#30 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\PreviewSimulator->process()\n#31 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/PageResolver.php(101): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#32 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\PageResolver->process()\n#33 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/StaticRouteResolver.php(71): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#34 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\StaticRouteResolver->process()\n#35 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/ContentSecurityPolicyReporter.php(57): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#36 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\ContentSecurityPolicyReporter->process()\n#37 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/SiteBaseRedirectResolver.php(90): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#38 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\SiteBaseRedirectResolver->process()\n#39 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/FrontendUserAuthenticator.php(76): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#40 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\FrontendUserAuthenticator->process()\n#41 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/BackendUserAuthenticator.php(82): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#42 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\BackendUserAuthenticator->process()\n#43 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/RequestTokenMiddleware.php(71): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#44 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Core\\Middleware\\RequestTokenMiddleware->process()\n#45 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/MaintenanceMode.php(51): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#46 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\MaintenanceMode->process()\n#47 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/EidHandler.php(48): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#48 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\EidHandler->process()\n#49 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/SiteResolver.php(80): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#50 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\SiteResolver->process()\n#51 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/CacheDataCollectorAttribute.php(61): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#52 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Core\\Middleware\\CacheDataCollectorAttribute->process()\n#53 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/NormalizedParamsAttribute.php(44): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#54 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Core\\Middleware\\NormalizedParamsAttribute->process()\n#55 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/TimeTrackerInitialization.php(49): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#56 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Frontend\\Middleware\\TimeTrackerInitialization->process()\n#57 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(70): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#58 /var/www/html/vendor/typo3/cms-core/Classes/Http/AbstractApplication.php(87): TYPO3\\CMS\\Core\\Http\\MiddlewareDispatcher->handle()\n#59 /var/www/html/vendor/typo3/cms-frontend/Classes/Http/Application.php(50): TYPO3\\CMS\\Core\\Http\\AbstractApplication->handle()\n#60 /var/www/html/vendor/typo3/cms-core/Classes/Http/RequestHandler.php(42): TYPO3\\CMS\\Frontend\\Http\\Application->handle()\n#61 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/ResponsePropagation.php(34): TYPO3\\CMS\\Core\\Http\\RequestHandler->handle()\n#62 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Core\\Middleware\\ResponsePropagation->process()\n#63 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/NormalizedParamsAttribute.php(44): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#64 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Core\\Middleware\\NormalizedParamsAttribute->process()\n#65 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/VerifyHostHeader.php(55): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#66 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\\CMS\\Core\\Middleware\\VerifyHostHeader->process()\n#67 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(70): Psr\\Http\\Server\\RequestHandlerInterface@anonymous->handle()\n#68 /var/www/html/vendor/typo3/cms-core/Classes/Http/AbstractApplication.php(87): TYPO3\\CMS\\Core\\Http\\MiddlewareDispatcher->handle()\n#69 /var/www/html/vendor/typo3/cms-core/Classes/Http/Application.php(44): TYPO3\\CMS\\Core\\Http\\AbstractApplication->handle()\n#70 /var/www/html/vendor/typo3/cms-core/Classes/Http/AbstractApplication.php(110): TYPO3\\CMS\\Core\\Http\\Application->handle()\n#71 /var/www/html/public/index.php(20): TYPO3\\CMS\\Core\\Http\\AbstractApplication->run()\n#72 /var/www/html/public/index.php(21): {closure}()\n#73 {main}"}
```

und hier die gleiche Zeile als Ausgabe vom Logformatter:

```shell
typo3_9f3926ad20.log: Sat, 11 Oct 2025 02:08:02 +0200 CRITICAL request=eeadd85234d82 component=Networkteam.SentryClient.DebugExceptionHandler
Core: Exception handler (WEB: FE): TypeError, code #0, file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php, line 16: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506- TypeError: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506, in file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php:16
```

Eine solche Ausgabe steigert die Lesbarkeit erheblich.

## Filterung

Natürlich wäre es schön, wenn "jemand" sein TYPO3 System so konfiguriert hätte, dass nur wichtige Fehler geloggt werden. Aber bei Fremdsystemen hat man oft wenig Möglichkeiten und muss mit dem leben, was da ist. Auch hier hilft der Logformatter mit diversen Filteroptionen.

### Nach Log-Level

Ihr könnt die Log-Einträge nach folgenden Log-Level filtern:

* EMERGENCY
* ALERT
* CRITICAL
* ERROR
* WARNING
* NOTICE
* INFO
* DEBUG

Achtet bitte darauf, dass ihr den Log-Level exakt so schreibt, wie oben angegeben. In Großbuchstaben!

```shell
vendor/bin/typo3 logformatter --level=WARNING
```

### Nach Request-ID

Kennt ihr die Fehlermeldung [Oops, an error occurred](/typo3-tutorials/fehlermeldungen/oops-an-error-occurred/)? Hier wird auch immer eine Request-ID, die letzten 8 Zeichen aus dem "Code", mitgegeben. Auch dafür hat der Logformatter eine Lösung und zieht euch die entsprechende Stelle direkt aus den Log-Dateien heraus:

```shell
vendor/bin/typo3 logformatter --request=bf53d592193f2
```

### Nach Komponente

Bei der Komponente handelt es sich um eine Art Namespace-Pfad zu der PHP-Klasse, innerhalb der der Log-Eintrag erstellt wurde. Im Namespace `\TYPO3\CMS\Core\Resource` werden alle Backslashes durch Punkte ersetzt: `TYPO3.CMS.Core.Resource` und dann kommt noch der Klassenname, getrennt durch einen Punkt, hinten dran. Diese Komponente wird bei TYPO3 immer mitgeloggt und kann somit auch als Filterkriterium genutzt werden:

```shell
vendor/bin/typo3 logformatter --component=TYPO3.CMS.Core.Resource.StorageRepository
```

### Suche

Der Logformatter bietet auch die Möglichkeit der Suche an:

```shell
vendor/bin/typo3 logformatter --search=Dropbox
```

Seid hier aber etwas vorsichtig mit Leerzeichen! Diese werden auf der Shell als Trennzeichen interpretiert und müssen daher escaped werden:

```shell
vendor/bin/typo3 logformatter --search=Could\ not\ instantiate\ storage
```

```shell
vendor/bin/typo3 logformatter --search="Could not instantiate storage"
```

### Meta Informationen

Die wichtigsten Meta Informationen aus jedem einzelnen Log-Eintrag können mit dem `-m` Parameter extrahiert und tabellarisch dargestellt werden:

```shell
vendor/bin/typo3 logformatter --request=aafc201a4a648 -m
```

Es folgt die normale Log-Ausgabe:

```shell
typo3_9f3926ad20.log: Sat, 18 Oct 2025 22:41:41 +0200 CRITICAL request=aafc201a4a648 component=Networkteam.SentryClient.DebugExceptionHandler
Core: Exception handler (WEB: FE): TypeError, code #0, file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php, line 16: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506- TypeError: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506, in file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php:16
```

gefolgt von der Tabelle mit den extrahierten Meta Informationen:

| Key | Value |
|-----|-------|
| mode | WEB |
| application_mode | FE |
| exception_class | TypeError |
| exception_code | 0 |
| file | /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php |
| line | 16 |
| message | Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506 |
| request_url | https://typo3134.ddev.site/user-content |

### Stacktrace

Standardmäßig wird der Stacktrace immer von den Log-Einträgen entfernt. Mit dem `-s` Parameter könnt ihr euch aber jeden Eintrag aus dem Stacktrace pro Zeile ausgeben lassen:

```shell
vendor/bin/typo3 logformatter --request=aafc201a4a648 -s
```

Er erscheint erstmal wieder der Log-Eintrag, gefolgt von dem Stacktrace, wo jede Position im Quellcode als eine Zeile aufgeführt wird:

```shell
typo3_9f3926ad20.log: Sat, 11 Oct 2025 02:08:02 +0200 CRITICAL request=bf53d592193f2 component=Networkteam.SentryClient.DebugExceptionHandler
Core: Exception handler (WEB: FE): TypeError, code #0, file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php, line 16: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506- TypeError: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506, in file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php:16
TypeError: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506 and defined in /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php:16

Stack trace:
#0 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(4506): Networkteam\SentryClient\Client::captureException()
#1 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/UserContentObject.php(44): TYPO3\CMS\Frontend\ContentObject\ContentObjectRenderer->callUserFunction()
#2 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(657): TYPO3\CMS\Frontend\ContentObject\UserContentObject->render()
#3 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(604): TYPO3\CMS\Frontend\ContentObject\ContentObjectRenderer->render()
#4 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(573): TYPO3\CMS\Frontend\ContentObject\ContentObjectRenderer->cObjGetSingle()
#5 /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php(553): TYPO3\CMS\Frontend\ContentObject\ContentObjectRenderer->cObjGetSeparated()
#6 /var/www/html/vendor/typo3/cms-frontend/Classes/Http/RequestHandler.php(227): TYPO3\CMS\Frontend\ContentObject\ContentObjectRenderer->cObjGet()
#7 /var/www/html/vendor/typo3/cms-frontend/Classes/Http/RequestHandler.php(196): TYPO3\CMS\Frontend\Http\RequestHandler->generatePageBodyContent()
#8 /var/www/html/vendor/typo3/cms-frontend/Classes/Http/RequestHandler.php(137): TYPO3\CMS\Frontend\Http\RequestHandler->generatePageContent()
#9 /var/www/html/vendor/sudhaus7/logformatter/Classes/MiddleWares/LogrequesturlMiddleWare.php(40): TYPO3\CMS\Frontend\Http\RequestHandler->handle()
#10 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): Sudhaus7\Logformatter\MiddleWares\LogrequesturlMiddleWare->process()
#11 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/ContentLengthResponseHeader.php(41): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#12 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\ContentLengthResponseHeader->process()
#13 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/ShortcutAndMountPointRedirect.php(94): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#14 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\ShortcutAndMountPointRedirect->process()
#15 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/ResponsePropagation.php(34): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#16 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Core\Middleware\ResponsePropagation->process()
#17 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/OutputCompression.php(44): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#18 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\OutputCompression->process()
#19 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/ContentSecurityPolicyHeaders.php(58): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#20 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\ContentSecurityPolicyHeaders->process()
#21 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/PrepareTypoScriptFrontendRendering.php(186): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#22 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\PrepareTypoScriptFrontendRendering->process()
#23 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/CacheTimeout.php(34): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#24 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\CacheTimeout->process()
#25 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/TypoScriptFrontendInitialization.php(126): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#26 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\TypoScriptFrontendInitialization->process()
#27 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/PageArgumentValidator.php(105): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#28 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\PageArgumentValidator->process()
#29 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/PreviewSimulator.php(87): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#30 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\PreviewSimulator->process()
#31 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/PageResolver.php(101): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#32 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\PageResolver->process()
#33 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/StaticRouteResolver.php(71): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#34 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\StaticRouteResolver->process()
#35 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/ContentSecurityPolicyReporter.php(57): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#36 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\ContentSecurityPolicyReporter->process()
#37 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/SiteBaseRedirectResolver.php(90): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#38 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\SiteBaseRedirectResolver->process()
#39 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/FrontendUserAuthenticator.php(76): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#40 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\FrontendUserAuthenticator->process()
#41 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/BackendUserAuthenticator.php(82): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#42 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\BackendUserAuthenticator->process()
#43 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/RequestTokenMiddleware.php(71): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#44 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Core\Middleware\RequestTokenMiddleware->process()
#45 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/MaintenanceMode.php(51): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#46 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\MaintenanceMode->process()
#47 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/EidHandler.php(48): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#48 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\EidHandler->process()
#49 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/SiteResolver.php(80): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#50 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\SiteResolver->process()
#51 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/CacheDataCollectorAttribute.php(61): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#52 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Core\Middleware\CacheDataCollectorAttribute->process()
#53 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/NormalizedParamsAttribute.php(44): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#54 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Core\Middleware\NormalizedParamsAttribute->process()
#55 /var/www/html/vendor/typo3/cms-frontend/Classes/Middleware/TimeTrackerInitialization.php(49): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#56 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Frontend\Middleware\TimeTrackerInitialization->process()
#57 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(70): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#58 /var/www/html/vendor/typo3/cms-core/Classes/Http/AbstractApplication.php(87): TYPO3\CMS\Core\Http\MiddlewareDispatcher->handle()
#59 /var/www/html/vendor/typo3/cms-frontend/Classes/Http/Application.php(50): TYPO3\CMS\Core\Http\AbstractApplication->handle()
#60 /var/www/html/vendor/typo3/cms-core/Classes/Http/RequestHandler.php(42): TYPO3\CMS\Frontend\Http\Application->handle()
#61 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/ResponsePropagation.php(34): TYPO3\CMS\Core\Http\RequestHandler->handle()
#62 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Core\Middleware\ResponsePropagation->process()
#63 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/NormalizedParamsAttribute.php(44): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#64 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Core\Middleware\NormalizedParamsAttribute->process()
#65 /var/www/html/vendor/typo3/cms-core/Classes/Middleware/VerifyHostHeader.php(55): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#66 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(162): TYPO3\CMS\Core\Middleware\VerifyHostHeader->process()
#67 /var/www/html/vendor/typo3/cms-core/Classes/Http/MiddlewareDispatcher.php(70): Psr\Http\Server\RequestHandlerInterface@anonymous->handle()
#68 /var/www/html/vendor/typo3/cms-core/Classes/Http/AbstractApplication.php(87): TYPO3\CMS\Core\Http\MiddlewareDispatcher->handle()
#69 /var/www/html/vendor/typo3/cms-core/Classes/Http/Application.php(44): TYPO3\CMS\Core\Http\AbstractApplication->handle()
#70 /var/www/html/vendor/typo3/cms-core/Classes/Http/AbstractApplication.php(110): TYPO3\CMS\Core\Http\Application->handle()
#71 /var/www/html/public/index.php(20): TYPO3\CMS\Core\Http\AbstractApplication->run()
#72 /var/www/html/public/index.php(21): {closure}()
```

### Vendor Pfade ausblenden

Wie ihr oben sehen könnt, kann so ein Log-Eintrag mit Stacktrace arg lang werden. Mit dem Zusatzparameter `--hide-vendor` können aus dem Stacktrace alle Einträge entfernt werden, bei denen sich die PHP-Klasse irgendwo im "vendor" Verzeichnis befindet.

Wenn sich eure eigene Extension auch im "vendor" Verzeichnis befindet, ergibt diese Option wenig Sinn. Ich denke `--hide-vendor` ist mehr für Site Package Extensions gedacht, die sich in einem "Source" oder "packages" Verzeichnis einer Composer Installationen befinden.

```shell
vendor/bin/typo3 logformatter --request=aafc201a4a648 -s --hide-vendor
```

Hier seht ihr die gleiche Ausgabe von oben. Jedoch sind alle Stacktrace Zeilen mit PHP-Klassen, die sich im "vendor" Verzeichnis befinden, verschwunden:

```shell
➜  typo3134 ddev typo3 logformatter --request=bf53d592193f2 --hide-vendor -s
typo3_9f3926ad20.log: Sat, 11 Oct 2025 02:08:02 +0200 CRITICAL request=bf53d592193f2 component=Networkteam.SentryClient.DebugExceptionHandler
Core: Exception handler (WEB: FE): TypeError, code #0, file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php, line 16: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506- TypeError: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506, in file /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php:16
TypeError: Networkteam\SentryClient\Client::captureException(): Argument #1 ($exception) must be of type Throwable, string given, called in /var/www/html/vendor/typo3/cms-frontend/Classes/ContentObject/ContentObjectRenderer.php on line 4506 and defined in /var/www/html/vendor/networkteam/sentry-client/Classes/Client.php:16
Stack trace:
#71 /var/www/html/public/index.php(20): TYPO3\CMS\Core\Http\AbstractApplication->run()
#72 /var/www/html/public/index.php(21): {closure}()
```

### Log Dateien ignorieren

Mache TYPO3 Extensions bringen ihre eigenen Log File Writer mit und sammeln somit alle Log-Einträge in ihren eigenen Log-Dateien. Um diese Dateien von der Analyse auszuschließen, kann man den Parameter `--ignore-log-files` verwenden.

```shell
vendor/bin/typo3 logformatter --ignore-file-pattern="typo3_mysql*.log"
```

`--ignore-file-pattern` kann im oberen Beispiel auch mehrfach verwendet werden, um weitere Dateien auszuschließen.

### Auf Log Datei beschränken

Standardmäßig werden alle Log-Dateien analysiert. Wenn ihr eine Analyse auf eine Datei beschränken wollt, dann könnt ihr den Dateinamen als Argument mit angeben:

```shell
vendor/bin/typo3 logformatter typo3_mysqlreport_9f3926ad20.log
```

### Logformatter mit stdin

Mit dem Linux Befehl `tail` kann man sich die letzten Zeilen einer Datei anzeigen lassen. Gerade in Verbindung mit `-f` (follow), sieht man LIVE welche Log-Einträge gerade frisch hinzugekommen sind. Um auch hier nicht von schier unendlich langen Stacktraces erschlagen zu werden, kann man die tail-Ausgabe als Eingabe für den Logformatter verwenden:

```shell
tail -f var/log/typo3_9f3926ad20.log | vendor/bin/typo3 logformatter -
```

Achtet darauf den abschließenden `-` Bindestrich mit anzugeben. Ab jetzt heißt es warten auf neue *verkürzte* Log-Einträge ;-)
