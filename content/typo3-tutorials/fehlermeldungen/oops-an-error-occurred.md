+++
title = "Oops an error occurred"
date = 2024-01-09T20:45:24+01:00
aliases = ["oops-an-error-occurred.html"]
+++

Seit TYPO3 6.0 gibt es in TYPO3 den ApplicationContext, mit dem Ihr TYPO3 in folgenden Modi laufen lassen könnt: Productive, Development oder Testing. Diese Modi lassen sich nicht wie vielleicht vermutet im Installtool umschalten, sondern müssen mit Umgebungsvariablen gesetzt werden. Ja, es ist ein Irrglaube zu denken, dass die "Debug"-Einstellung unter "Configuration Presets" im Installtool diese Modi setzen kann.

Bei jedem TYPO3-Aufruf wird die Umgebungsvariable TYPO3_CONTEXT abgefragt. Falls diese nicht gesetzt ist, wird die Umgebungsvariable REDIRECT_TYPO3_CONTEXT abgefragt. Falls beide Variablen nicht gesetzt sind, schaltet TYPO3 als Fallback in den Productive-Modus.

Je nach Konfiguration des Webservers lassen sich solche Umgebungsvariablen auch in der .htaccess setzen:

```shell
SetEnv TYPO3_CONTEXT Development
```

Ich weiß, dass aufgrund von Sicherheitsbestimmungen, diese Art der Konfiguration nicht bei Domainfactory und jweiland.net funktioniert. Hier habe ich 3 Alternativen für Euch:

Die index.php von TYPO3 modifizieren und dadurch die Updatefähigkeit beeinflussen. Da sich auch mit PHP Umgebungsvariablen setzen lassen, tragt Ihr in diese Datei folgende Zeile ein: `putenv('TYPO3_CONTEXT=Development');`

Legt eine eigene `index.php` an. Fügt dort die putenv-Zeile von oben ein und ladet die Original TYPO3 index.php mit require_once('typo3_src/index.php'). Vorteil: Ihr seid updatefähig.

Bei den beiden genannten Hostern oben könnt Ihr im Konfigurationsmenü die php.ini editieren. Fügt hier eine `auto_prepend_file` Zeile ein, die auf eine PHP-Datei verweist, die wie im vorherigen Beispiel eine Umgebungsvariable mit putenv() registriert. Diese Datei wird nun vor jedem TYPO3-Aufruf geladen. Vorteil: Ihr seid updatefähig.

## ExceptionHandler für Inhaltselemente

Wie Ihr oben gelesen habt, läuft TYPO3 ohne gesetzte Umgebungsvariablen im Productive-Modus. In diesem Modus dürfen keine Exceptions an den Webseitenbesucher ausgegeben werden, denn in diesen sogenannten Backtraces können Hacker Informationen über das System ausspähen. Um diese informativen Ausgaben zu unterbinden ist der ContentObjectEceptionHandler integriert worden. Wird während der Verarbeitung von Inhaltselementen, und hierzu zählen auch sämtliche Frontendextensions/Plugins, eine Exception geworfen, fängt dieser ExceptionHandler diese ab und meldet sich nur noch mit den Worten: Oops, an error occurred!

Der ContentObjectExceptionHandler lässt sich mithilfe von TypoScript sehr individuell konfigurieren. Für die Zeit der Entwicklung kann der ExceptionHandler auch im Productive-Modus komplett abgeschaltet werden. Habt Ihr ZUSÄTZLICH im Installtool das "Configuration Preset" auf "Debug" eingestellt, dann erscheinen auch die wohl bekannten Backtraces wieder im Frontend:

```typo3_typoscript
config.contentObjectExceptionHandler = 0
```
