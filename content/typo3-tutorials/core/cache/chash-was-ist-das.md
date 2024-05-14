+++
title = "Was ist der cHash?"
linkTitle = "cHash? Was ist das?"
description = "Auf dieser Seite erkläre ich Euch, was der cHash ist und wie dieser funktioniert. Außerdem zeige ich Euch Beispiele, wie Ihr URL mit cHash selber erstellen könnt."
keywords = "typo3 stefan frömken lindlar oberberg oberbergischer kreis tutorial chash, generierung, kalkulation, parameter"
date = 2024-02-10T22:09:48+01:00
slug = "chash-was-ist-das"
aliases = ["chash-was-ist-das.html", "/typo3-tutorials/tipps-und-tricks/chash-was-ist-das.html"]
+++

Mit jedem Aufruf einer Seite im Frontend muss TYPO3 eine ganze Reihe von Informationen aufbereiten. Das Aufwändigste hier dürfte wohl das Abarbeiten von TypoScript sein. Allein die Extension css_styled_content kommt schon mit über 1000 Zeilen TypoScript daher. Auch das Templating kostet Zeit. Suchen und Ersetzen der Platzhalter in Fluid oder in früheren Installationen die Marker und Subparts. All das kostet Zeit und die Idee eines Seitencaches war geboren. Ein Cache, in dem das TypoScript schon fertig abgearbeitet wurde und die Seiteninhalte bereits in die Platzhalter der Templates eingebunden wurde. All diese Daten liegen nun in der Datenbanktabelle `cache_pages`.

Da jede Seite sein eigenes TypoScript haben könnte, wird der Cache pro Seite neu erstellt. Mit jedem Aufruf einer weiteren Seite füllt sich die Tabelle `cache_hash` um einen weiteren Eintrag. Unterschieden werden die Cache-Einträge anhand der Seiten-UID, die der Webseite mithilfe eines URL-Parameters wie ?id=12 übergeben wird.

Zusätzlich zum `id` Parameter gibt es in TYPO3 noch den `type` Parameter, mit dem ein und dieselbe Seite unterschiedlich dargestellt werden kann. Auf diese Weise könnte man z.B. eine Druckansicht `?id=12&type=89` oder eine PDF-Ausgabe `?id=12&type=123` oder auch eine Ausgabe für mobile Endgeräte `?id=12&type=2` erstellen.

Bevor ein solcher Cache-Eintrag erstellt wird, prüft TYPO3 zunächst, ob es eine Seite mit der entsprechenden Seiten-UID gibt. Mithilfe der `typeNum` Eigenschaft innerhalb von TypoScript weiß TYPO3, welche Seitentypen zur Verfügung stehen. Ist, wie in unserem Beispiel, keine PDF-Ansicht mit der `typeNum=123` konfiguriert, dann wird auch kein Cache-Eintrag erstellt.

## Sicherheit

Wir haben gerade gelernt, dass TYPO3 nur für Seiten und Seitentypen einen Cache-Eintrag erstellt, wenn diese auch vorhanden sind. Diese Überprüfung schützt vor einer überfluteten `cache_pages` Tabelle. Stellen wir uns mal vor, was passieren würde, wenn dieser Check nicht vorhanden wäre: Dann könnte ein Angreifer mithilfe eines Skriptes unsere
Seite mit beliebigen Parametern aufrufen und obwohl es die Seiten und Seitentypen überhaupt nicht gibt, würde trotzdem ein neuer Eintrag in der Cache-Tabelle erzeugt werden. Einige Hoster begrenzen die Größe der Datenbank auf ein paar wenige Gigabyte. Wenn dieses Limit erreicht ist, dann läuft die Seite gar nicht mehr.

Alle URL-Parameter, die nicht `id` oder `type` lauten, werden von TYPO3 nicht berücksichtigt.

## Das Problem

Ihr als Entwickler programmiert nun eine Extension und bindet ein Plugin auf einer Seite ein. Für TYPO3 ist euer Plugin ein ganz normales Inhaltselement. Der Inhalt wird extrahiert und in den Platzhalter eures Templates eingebaut und die generierten Daten in die Tabelle `cache_pages` geschrieben. Wenn euer Plugin nur statische Werte ausliefert, mag das noch OK sein, aber wenn wir uns eine Extension wie `news` oder einen Blog vorstellen, dann soll nicht nur der erste aufgerufene Newsartikel im Cache gespeichert werden, sondern auch alle anderen Newsartikel.

Wir benötigen demnach einen weiteren Parameter in der URL, um zwischen den Newsartikeln unterscheiden zu können. Wie aber sollen wir das anstellen, wenn TYPO3, wie oben bereits erklärt, weitere URL-Parameter nicht verarbeitet?

Laut dem [englischen Beitrag von Kasper](https://typo3.org/article/the-mysteries-of-chash) gibt es nun folgende 3 Möglichkeiten:

Neben dem `id` und `type` Parameter gibt es noch den `no_cache` Parameter. Durch Anfügen dieses Parameters an die URL, wird zwar ein Eintrag in der `cache_pages` Tabelle erzeugt, aber dieser beinhaltet nur noch ein paar statische Werte. Jegliches TypoScript und auch das Verarbeiten des Templates geschieht nun mit jedem Seitenaufruf erneut, was diese Lösung zu der Langsamsten macht. Dieselbe Vorgehensweise erhaltet ihr auch mit der Checkbox `no Cache` in den Seiteneigenschaften, auf der ihr euer Plugin platziert habt. Als Extension-Entwickler habt ihr die Möglichkeit euer Plugin als `USER_INT` einzubinden. Diese Vorgehensweise cacht das verarbeitete TypoScript und erstellt auch ein Template. In diesem Template wird jedoch nicht der Inhalt des Plugins eingefügt, sondern ein Platzhalter. Wenn die Seite geladen wird, wird das Template aus der Datenbank geholt und TYPO3 prüft, ob noch ein Platzhalter in diesem Template vorhanden ist. Wenn ja, dann weiß TYPO3 anhand der Platzhalter-ID, welcher Inhalt dort einzubinden ist. In diesem Fall der Inhalt eures Plugins. Auf diese Weise ist das ganze Außendrum gecacht und nur euer Plugin wird mit jedem Seitenaufruf erneut ausgeführt. Diese Lösung bietet eine mittlere Performance. Gerade für die Ansicht von Suchergebnissen sollte diese Umsetzung bevorzugt werden, da sonst für jedes Suchwort ein eigener Cache-Eintrag erzeugt wird.

Die performanceorientierteste Lösung ist die Verwendung von dem TYPO3 eigenen URL-Parameter `cHash`. Wenn ihr für eure Extension weitere URL-Parameter benötigt, dann funktioniert das nur unter Verwendung des `cHash` Parameters. Dieser bildet eine Art `PHP:md5()` Hash über alle zusätzlichen Parameter in der URL. Sollte nun ein Hacker einen weiteren Parameter hinzufügen, dann stimmt die MD5-Summe der URL-Parameter nicht mehr mit der MD5-Summe des cHashes überein und es erscheint je nach Konfiguration eine Fehlermeldung oder die Seite wird ohne Cache-Eintrag aufgerufen.

## Arbeitsweise des cHash-Parameters

Alle Methoden zur Generierung von `cHash` befinden sich in der PHP Klasse `TYPO3\CMS\Frontend\Page\CacheHashCalculator`.

Einfacher geht es jedoch, wenn ihr in euren Extensions immer die `typolink` Methoden verwendet. Diese generieren den `cHash` automatisch für euch.

## Erstellung eines cHash durch typolink

Ihr habt in eurer Extension eine Liste von Produkten. Jedes Produkt beinhaltet einen Link zu der Detailansicht. Für jeden Link in dieser Liste wird nun die `typolink` Methode aufgerufen. Schauen wir uns einen solchen Link genauer an:

`?id=21&type=89&tx_mxext[product]=1204&tx_myext[action]=detail`

Die Parameter `id` und `type`, sind wie bereits erwähnt, in TYPO3 integriert und somit bekannt. Diese beiden Werte werden somit nicht zur Generierung des `cHash` herangezogen. Bleiben nur noch die beiden anderen Werte:

`&tx_mxext[product]=1204&tx_myext[action]=detail`

Die Parameter werden alphabetisch sortiert und der generierte `cHash` hinten angehängt:

`&tx_myext[action]=detail&tx_mxext[product]=1204&cHash=cn4r3728cng483n2cfg7248nx782`

## Aufruf einer Seite mit cHash

Im Folgenden ein Beispiel mit dieser URL:

`?id=21&type=89&tx_myext[action]=detail&tx_mxext[product]=1204&cHash=cn4r3728cng483n2cfg7248nx782`

Die Parameter `id` und `type` sind TYPO3 bereits bekannt und werden herausgefiltert. Übrig bleibt:

`tx_myext[action]=detail&tx_mxext[product]=1204&cHash=cn4r3728cng483n2cfg7248nx782`

TYPO3 prüft nun auf den Parameter `cHash` und weiß, dass sich in der URL noch weitere Parameter befinden:

`tx_myext[action]=detail&tx_mxext[product]=1204`

Jetzt wird erneut die Methode zum Generieren von cHashes aufgerufen und der generierte Wert mit dem Wert aus `cHash` verglichen. Wenn diese Werte übereinstimmen, dann darf ein Cache-Eintrag erstellt werden bzw. die Seite aus dem Cache geladen werden.

Stellen wir uns vor, dass ein Hacker die Produkt-UID in unserer Beispiel-URL ändert:

`?id=21&type=89&tx_myext[action]=detail&tx_mxext[product]=2048&cHash=cn4r3728cng483n2cfg7248nx782`

Nach der Generierung des cHash-Wertes wird dieser nicht dem Wert aus dem `cHash` Parameter in der URL übereinstimmen und die Seite wird nicht gecacht bzw. es erscheint eine Fehlermeldung.

## Nochmal zurück zum Thema Sicherheit

Es bleibt die Frage, ob ein Hacker den `cHash` selbst generieren kann. Ich habe bereits oben schon geschrieben, dass der `cHash` nur so eine Art MD5-Hash ist. Laut den `cHash` Methoden wird den URI-Parametern noch ein weiterer Parameter hinzugefügt, bevor der `cHash` generiert wird:

```php
$relevantParameters['encryptionKey'] = $GLOBALS['TYPO3_CONF_VARS']['SYS']['encryptionKey'];
```

Dieser Wert ist nur über das Installtool erreichbar. Das Installtool ist 2-fach abgesichert durch Passwort und dem Vorhandensein der Datei `ENABLE_INSTALL_TOOL`. Somit geht die eigentliche Gefahr wohl eher von unsicheren FTP-Accounts aus. Immer wieder weisen wir Kunden darauf hin, entweder `sftp` oder noch besser per `SSH` auf unsere Server zuzugreifen.

Nur wenn ein Hacker an diesen `encryptionKey` gelangt, hat er die Möglichkeit eigene cHashes generieren zu lassen.

## Ein leerer cHash sorg für Verwirrung

Es passiert schon mal, dass Programmierer die URLs selber zusammenstellen und dabei oftmals den `cHash` vergessen. Wie bereits erwähnt interpretiert TYPO3 nur dann die zusätzlichen Parameter, wenn ein `cHash` Parameter vorhanden ist. Wenn dieser leer oder nicht vorhanden ist, dann ruft TYPO3 die Webseite so auf, als wären die zusätzlichen 
Parameter überhaupt nicht da. Es wird also nur der `id` und der `type` Parameter verwendet. So könnte z.B. laut URL das Produkt mit der ID 1204 angefragt werden, aber durch den fehlenden `cHash` Parameter wird das Produkt oder die Seite aufgerufen, die die Extension zuallererst angezeigt hat.

Erst durch ein Clear Cache im Backend wird die Tabelle `cache_pages` geleert und ein erneuter Aufruf zeigt dann das gewünschte Produkt an (ID 1204). Wird nun aber das Produkt 2048 ohne `cHash` Parameter aufgerufen wird weiterhin das Produkt 1204 angezeigt, weil eben nur der `id` und `type` Parameter verarbeitet werden.

## Manuelle Erstellung eines Links mit cHash

Die Generierung von Links mithilfe von `typolink` ist von der Performance her für vereinzelte Links OK. Wenn ihr aber eine Listenansicht mit mehreren 1000 Datensätzen habt, oder Links für Veranstaltungen des kompletten Jahres erstellen wollt, dann kann euch die Generierung von Links mithilfe von `typolink()` einiges an Sekunden kosten. Deshalb hier ein Beispiel, wie ihr in einem Extbase Controller solche Links selbst zusammen bauen könnt:

```php
$site = $this->request->getAttribute('site');
$uri = (string)$site->getRouter()->generateUri(
    $this->settings['pidOfDetailPage'],
    [
        'tx_events2_events' => [
            'controller' => 'Day',
            'action' => 'show',
            'day' => $dayUid,
        ]
    ],
));
```

## Einstellungen aus dem Installtool

Im Installtool befinden sich ein paar Einstellungen, mit denen man aktiv auf die Generierung des `cHash` zugreifen kann:

| Eigenschaft                    | Erklärung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| pageNotFoundOnCHashError       | Wenn aktiviert, dann wird eine Fehlerseite im Frontend ausgegeben, wenn die Summe der Parameter mit dem angegebenen Wert aus dem `cHash` Parameter nicht übereinstimmt. Wenn deaktiviert, dann wird das Caching abgeschaltet und die Seite mit jedem Aufruf von neuem generiert (langsam).                                                                                                                                                                                                                                                                                  |
| cHashExcludedParameters        | Alle hier angegebenen Parameter werden von der `cHash` Generierung ausgeschlossen. Gerade bei Suchen ist das interessant, wenn sich das Suchwort auch in der URL befindet. Es handelt sich hierbei um eine kommaseparierte Liste von Parametern. Ein Beispiel könnte so aussehen: `tx_myext[search],tx_myext_mypi[searchword]`                                                                                                                                                                                                                                              |
| cHashOnlyForParameters         | Die Angaben sind wie oben kommasepariert anzugeben. Normalerweise ist dieser Wert leer und in meinen Augen sollte dieser Wert auch leer bleiben, denn stellen wir uns mal vor, wir tragen hier etwas ein, dann wird innerhalb der `cHash` Generierung automatisch das Whitelisting aktiviert. Somit müssen alle benötigten Parameter hier in dieser Auflistung vorhanden sein. Bei größeren Projekten mit 50 Extensions, die je 5 Parameter haben, müssten demnach 250 Parameter hier eingetragen werden, damit die Webseite weiterhin einwandfrei läuft bzw. gecacht wird. |
| cHashRequiredParameters        | Wir haben gelernt, dass bei fehlendem `cHash` Parameter alle zusätzlichen Parameter nicht mehr berücksichtigt werden. Im oberen Beispiel hatten wir das Produkt 1204 das gecacht wurde, aber auch nach Angabe der Produkt UID 2048 in der URL immer noch das Produkt 1204 angezeigt wurde. Um diese Verwirrung zu lösen, kann man hier Parameter angeben, die zwingend einen `cHash` erfordern. Somit würde statt dem Laden des Produktes 1204 nun eine Fehlermeldung erscheinen oder je nach Konfiguration die Seite ungecacht geladen.                                    |
| cHashExcludedParametersIfEmpty | Normalerweise werden URL-Parameter auch dann mit in die `cHash` Generierung reingerechnet, wenn diese leer sind. Mit der Angabe von `*` kann dem Generator mitgeteilt werden, dass alle Parameter, die leer sind nicht mit in die Berechnung einfließen sollen. Unter Angabe der Parameter als Komma separierte Liste können auch nur ganz bestimmte Parameter von der Berechnung ausgeschlossen werden.                                                                                                                                                                    |

## Überschreiben der Parameter aus dem Installtool pro Extension

Es sollte nicht die Aufgabe eines TYPO3 Administrator sein, all diese Werte (oben) im Installtool zu pflegen. Viel besser wäre es, wenn die Extension-Entwickler diese Werte selbst setzen. Alle Werte aus dem Installtool, die für die `cHash` Generierung erforderlich sind, werden in der Bootstrap-Klasse in Arrays konvertiert: `TYPO3\CMS\Core\Core\Bootstrap->setCacheHashOptions()`.

Ihr könnt euch dieses generierte Array im Backend unter Konfiguration >> SelectBox: `$GLOBALS['TYPO3_CONF_VARS']` >> Aufklappen von `FE` >> Aufklappen von `cacheHash` anschauen.

Die Methode `setCacheHashOptions()` wird nach dem Laden der `LocalConfiguration.php` geladen. Somit können diese Werte nicht mithilfe einer `AdditionalConfiguration.php` überschrieben werden. Erst innerhalb der `ext_tables.php` oder auch `ext_localconf.php` ist es euch möglich diese Werte zu überschreiben. Ich empfehle jedoch den Einsatz der 
`ext_localconf.php`.

Nach Eingabe von

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['cHashExcludedParameters'] = 'L,tx_myext[search]';
```

erscheint automatisch dieses Array hier:

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['cacheHash']['excludedParameters']['0'] = 'L';
$GLOBALS['TYPO3_CONF_VARS']['FE']['cacheHash']['excludedParameters']['1'] = 'tx_myext[search]';
```

Mit dieser Zeile in der `ext_localconf.php`

```php
$GLOBALS['TYPO3_CONF_VARS']['FE']['cacheHash']['excludedParameters'][] = 'tx_myotherext[search]';
```

könnt ihr weitere Parameter diesem Array hinzufügen.

Der `CacheHashCalculator` besitzt einen Konstruktor, der dieses Array automatisch als interne Konfiguration wieder einließt:

```php
public function __construct() {
    $this->setConfiguration($GLOBALS['TYPO3_CONF_VARS']['FE']['cacheHash']);
}
```
