+++
title = "Be"
date = 2024-01-09T20:28:02+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["be.html"]
+++

## f:be.container

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 11.3{{% /badge %}}
{{% badge style="red" icon="skull-crossbones" %}}TYPO3 12.0{{% /badge %}}

Wurde damals verwendet, um ein Layout für Backend Module zu schaffen, das dem der TYPO3 eigenen Backend Module entspricht. Durch zusätzliche Parameter konnten RequireJS Module, CSS- und JS Dateien angegeben werden. Da die meisten Parameter eh alle an die TYPO3 Klasse `PageRenderer` übergeben wurden, entstand der `f:be.pageRenderer` ViewHelper als Ablösung.

## f:be.infobox

{{% badge style="green" icon="angle-double-up" %}}TYPO3 7.2{{% /badge %}}

Erstellt eine InfoBox mit Überschrift, einer Nachricht und wahlweise einem Icon. 

{{% notice style="info" title="Hinweis" icon="info" %}}
Warum ein neuer ViewHelper und warum wird nicht auch hier `f:flashMessages` verwendet? Von TYPO3 4.3 bis TYPO3 8.6 wird das zugrunde liegende HTML Grundgerüst für Flash Messages (`<ul><li>...</li></ul>`) direkt und unveränderlich durch den `f:flashMessages` ViewHelper erstellt. Mit Einführung von TYPO3 7 wurden jedoch im TYPO3 Backend immer mehr CSS-Klassen auf Bootstrap umgestellt. Manche Bootstrap Features wie die Alert-Boxen erforderten jedoch ein anderes HTML Grundgerüst, weshalb der `f:be.infobox` ViewHelper Einfuhr erhielt.
{{% /notice %}}

### Parameter

| Parameter   | Erklärung                                                                                                                                                                                                                                                                          | Standardwert                                              |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| title       | Überschrift für die InfoBox. Enthaltenes HTML wird escaped.                                                                                                                                                                                                                        |                                                           |
| message     | Die Nachricht, die die InfoBox ausgeben soll. Angegeben als Attribut wird HTML escaped. Als Inhalt zwischen den HTML-Tags ist HTML jedoch erlaubt.                                                                                                                                 |                                                           |
| state       | Status der InfoBox. Je nach Status wird die InfoBox in anderen Signalfarben ausgegeben:<br>-2: Hinweis<br>-1: Information<br>0: Alles OK<br>1: Warnung<br>2: Fehler                                                                                                                | -2: Hinweis                                               |
| iconName    | Der CSS Klassenname aus dem kostenlosen Font-Awesome Paket ohne das führende `fa-`. Ab {{% badge style="green" icon="angle-double-up" %}}TYPO3 12.0{{% /badge %}} wurde auf die IconFactory umgestellt, wodurch ihr von nun an Zugriff auf alle in TYPO3 registrierten Icons habt. | Es wird ein entsprechendes Icon je nach `state` gerendert |
| disableIcon | Wenn aktiviert, dann wird das Icon nicht angezeigt                                                                                                                                                                                                                                 | 0                                                         |

### Beispiel: ohne HTML

```html
<f:be.infobox title="Fehler beim Abrufen" message="Die Datei Schlafwandeln.pdf konnte auf dem remote Server nicht gefunden werden."/>
```

### Beispiel: mit HTML

```html
<f:be.infobox title="Fehler beim Abrufen">Die Datei <em>Schlafwandeln.pdf</em> konnte auf dem remote Server nicht gefunden werden.</f:be.infobox>
```

## f:be.link

{{% badge style="green" icon="angle-double-up" %}}TYPO3 9.0{{% /badge %}}

Erstellt einen a-Tag mit einer Verlinkung zu einem TYPO3 Backend Modul.

{{% notice style="info" title="Hinweis" icon="info" %}}
Das Backend Routing wurde zwar mit TYPO3 7.5 eingeführt, dennoch wurde bei vielen Backend-Modulen die URI zu einem anderen Backend-Modul weiterhin über die TYPO3 API PHP-seitig erstellt und an die View übergeben. Der Wunsch, diese URIs auch in der View selbst zu erstellen kam später, weshalb dieser ViewHelper erst mit TYPO3 9.0 eingeführt wurde.
{{% /notice %}}

### Parameter

| Parameter     | Erklärung                                                                                                                                                               | Standardwert |
|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| route         | Name der Route. Im Backend gibt es links den Menüpunkt "Konfiguration". Wählt dort "Backendroutes" aus der Selektbox aus, um alle verfügbaren Routen anzeigen zulassen. |              |
| parameters    | Gebt der aufzurufenden Route oder auch Backend Modul ein paar zusätzliche URI Parameter mit an.                                                                         |              | 
| referenceType | Angabe des Link-Typs.<br>url: Absolute URI<br>absolute: Absoluter Pfad<br>share: Absolute URI zum Teilen mit anderen                                                    | absolute     |

### Beispiel

```html
<f:be.link route="web_info" parameters="{id: 84}">Wechsel in das Web Info Module für die Seite 84</f:be.link>
```

## f:be.pageInfo

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Mit diesem ViewHelper wird in einigen Backend-Modulen oben rechts der Seitentitel ausgegeben. Vor dem Seitennamen das Seiten-Typ-Icon, das bei Klick das Kontext-Menü für die Seite öffnet und rechts vom Seitentitel steht die Seiten-UID in eckigen Klammern.

{{% notice style="info" title="Hinweis" icon="info" %}}
Seit TYPO3 12 gibt es den Hinweis, dass dieser ViewHelper möglicherweise entfernt werden könnte. Mit Einführung des neuen TemplateModule, das sich auch um das Rendern der DocHeader kümmert, wird dieser ViewHelper möglicherweise bald obsolet.
{{% /notice %}}

### Beispiel

```html
<f:be.pageInfo />
```

## f:be.pagePath

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Mit diesem ViewHelper wird in einigen Backend Modulen oben rechts der derzeitige Web-Pfad zu der aktuellen Seite im Backend angezeigt. Sieht aus wie eine Bread-Crumb. Meist steht die Ausgabe vom `f.be.pageInfo` ViewHelper direkt dahinter.

{{% notice style="info" title="Hinweis" icon="info" %}}
Seit TYPO3 12 gibt es den Hinweis, dass dieser ViewHelper möglicherweise entfernt werden könnte. Mit Einführung des neuen TemplateModule, das sich auch um das Rendern der DocHeader kümmert, wird dieser ViewHelper möglicherweise bald obsolet.
{{% /notice %}}

### Beispiel

```html
<f:be.pagePath />
```

## f:be.pageRenderer

{{% badge style="green" icon="angle-double-up" %}}TYPO3 7.6{{% /badge %}}

Mit diesem ViewHelper wird in einigen Backend Modulen oben rechts der derzeitige Web-Pfad zu der aktuellen Seite im Backend angezeigt. Sieht aus wie eine Bread-Crumb. Meist steht die Ausgabe vom `f.be.pageInfo` ViewHelper direkt dahinter.

### Parameter

| Parameter                | Erklärung                                                                                                                                                                                                                                                                                                                    |
|--------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| pageTitle                | Der Seitentitel der rendernden Ansicht                                                                                                                                                                                                                                                                                       |
| loadExtJs                | {{% badge style="orange" icon="angle-double-up" %}} TYPO3 8.7{{% /badge %}} {{% badge style="red" icon="skull-crossbones" %}}TYPO3 9.0{{% /badge %}} Soll die ExtJS Library geladen werden?                                                                                                                                  |
| loadExtJsTheme           | {{% badge style="orange" icon="angle-double-up" %}}TYPO3 8.7{{% /badge %}} {{% badge style="red" icon="skull-crossbones" %}}TYPO3 9.0{{% /badge %}} Welcher Stil soll für die ExtJS Komponenten verwendet werden?                                                                                                            |
| enableExtJsDebug         | {{% badge style="orange" icon="angle-double-up" %}}TYPO3 8.7{{% /badge %}} {{% badge style="red" icon="skull-crossbones" %}}TYPO3 9.0{{% /badge %}} Aktivieren der Debug-Ausgabe für ExtJS                                                                                                                                   |
| loadJQuery               | {{% badge style="orange" icon="angle-double-up" %}}TYPO3 8.7{{% /badge %}} {{% badge style="red" icon="skull-crossbones" %}}TYPO3 9.0{{% /badge %}} Einbindung von jQuery aktivieren                                                                                                                                         |
| jQueryNamespace          | {{% badge style="orange" icon="angle-double-up" %}}TYPO3 8.7{{% /badge %}} {{% badge style="red" icon="skull-crossbones" %}}TYPO3 9.0{{% /badge %}} Um Side-Effects mit anderen JavaScript Bibliotheken zu verhindern, kann hier ein eigener Namespace für jQuery angegeben werden.                                          |
| includeCssFiles          | Angabe von einer oder mehreren CSS Dateien. Angabe von `EXT:` ist erlaubt.                                                                                                                                                                                                                                                   |
| includeJsFiles           | Angabe von einer oder mehreren JavaScript Dateien. Angabe von `EXT:` ist erlaubt.                                                                                                                                                                                                                                            |
| addJsInlineLabels        | Angabe von Übersetzungen, auf die mittels `TYPO3.lang.*` innerhalb von JavaScript-Dateien wieder zugegriffen werden kann.                                                                                                                                                                                                    |
| includeRequireJsModules  | {{% badge style="orange" icon="angle-double-up" %}}TYPO3 12.0{{% /badge %}} {{% badge style="red" icon="skull-crossbones" %}}TYPO3 13.0{{% /badge %}} TYPO3 bringt von Haus aus schon eine Menge JavaScript Module wie Drag&Drop, Events, Notifications und Vielem mehr bereits mit. Ladet hier die Module, die ihr braucht. |
| includeJavaScriptModules | {{% badge style="green" icon="angle-double-up" %}}TYPO3 12.0{{% /badge %}} TYPO3 bringt von Haus aus schon eine Menge JavaScript Module wie Drag&Drop, Events, Notifications und Vielem mehr bereits mit. Ladet hier die Module, die ihr braucht.                                                                            |
| addInlineSettings        | Angabe von beliebigen Einstellungen ähnlich der `settings`, die ihr aus Extbase und Fluid kennt. Alle Einstellungen sind innerhalb von JavaScript mittels `TYPO3.settings.*` verfügbar.                                                                                                                                      |

### Beispiel

```html
<f:be.pageRenderer
        pageTitle="Beste Überschrift ever"
        includeCssFiles="{0: 'EXT:my_ext/Resources/Public/Css/Stylesheet.css'}"
        includeJsFiles="{0: 'EXT:my_ext/Resources/Public/JavaScript/Library1.js', 1: 'EXT:my_ext/Resources/Public/JavaScript/Library2.js'}"
        addJsInlineLabels="{'events2.organizer': 'LLL:EXT:events2/Resources/Private/Language/locallang.xlf:organizer'}"
        includeJavaScriptModules="{0: '@my-vendor/my-ext/my-module.js'}"
        addInlineSettings="{'maps2.googleKey': 'AB.56.ghr.4832.139fjr'}"/>
```

## f:be.tableList

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Erstellt eine Listenansicht, wie ihr sie aus dem List-Modul kennt. Anzahl Datensätze, Filter, Speicher, Sortierung und mehr können über Parameter konfiguriert werden.

### Parameter

| Parameter                 | Erklärung                                                                                                                                                                                                                                                                                          | Standardwert              |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| tableName                 | Der Name der Tabelle                                                                                                                                                                                                                                                                               |                           |
| fieldList                 | Welche Spalten der Tabelle sollen angezeigt werden (Arraynotation)                                                                                                                                                                                                                                 |                           |
| storagePid                | Die Daten welcher Seite sollen angezeigt werden? Wenn nichts anderen angegeben wurde, werden die Datensätze der Seite angezeigt, die über persistence.storagePid angegeben wurde.                                                                                                                  |                           |
| levels                    | Falls die Seite aus storagePid noch Unterordner enthält, könnt ihr hier angeben wie viele Ebenen tiefer nach weiteren Datensätzen gesucht werden soll.                                                                                                                                             | 1                         |
| filter                    | Gebt hier ein Suchwort ein, nach dem Eure Datensätze gefiltert werden sollen.                                                                                                                                                                                                                      |                           |
| recordsPerPage            | Wie viele Datensätze dürfen maximal auf einer Seite angezeigt werden.                                                                                                                                                                                                                              | Angabe aus TCA, sonst 100 |
| sortField                 | Nach welchem Feld sollen die gefundenen Datensätze sortiert werden                                                                                                                                                                                                                                 |
| sortDescending            | Wenn aktiviert, dann werden die Datensätze rückwärts sortiert.                                                                                                                                                                                                                                     | deaktiviert               |
| readOnly                  | Wenn aktiviert, dann werden die Bearbeitungssymbole nicht mehr angezeigt.                                                                                                                                                                                                                          | deaktiviert               |
| enableClickMenu           | Wenn aktiviert, dann kann das Contextmenü verwendet werden                                                                                                                                                                                                                                         | aktiviert                 |
| clickTitleMode            | Auswahl von `edit`, `info` und `show`. Falls ihr `show` auswählt, dann klappt das nur bei der Tabelle `pages` und `tt_content`. Normalerweise müsst ihr immer erst über das Kontextmenü oder über ein anderes Modul in diesen Bereich klicken. Nun reicht ein Klick auf den Titel des Datensatzes. |                           |
| enableControlPanels       | {{% badge style="green" icon="angle-double-up" %}}TYPO3 10.4.6{{% /badge %}} Aktivierung der Control Buttons, wie löschen, History, sort up, sort down                                                                                                                                             | deaktiviert               |
| alternateBackgroundColors | {{% badge style="red" icon="skull-crossbones" %}}TYPO3 8.0{{% /badge %}} Wenn aktiviert, wechseln sich die Hintergrundfarben je Datenzeile ab.                                                                                                                                                     | deaktiviert               |

### Minimalistisches Beispiel

Dieses Beispiel zeigt die Tabelle `tt_content` mit den beiden Feldern `header` und `bodytext`. Die Tabelle wird nur dann angezeigt, wenn sich auf der Seite, die durch `storagePid` angegeben wurde auch tatsächlich Datensätze dieser Tabelle befinden. Alle Backendmodule, die zumindest über den extension_builder erzeugt wurden, bringen ein Extensiontemplate mit, das auf der Rootseite eingebunden werden sollte. Über den Konstanten-Editor kann nun eine Seiten-UID eingetragen werden, die als Fallback dient, falls wie hier in unserem Beispiel keine `storagePid` angegeben worden ist.

```html
<f:be.tableList tableName="tt_content" fieldList="{0: 'header', 1: 'bodytext'}" />
```

### Beispiel: Maximale Datensatzanzahl

Obwohl wir hier den Parameter `recordsPerPage` nicht angegeben haben, werden uns innerhalb des Introduction-Package nur 100 Datensätz auf der ersten Seite angezeigt von etwas über 150. Denn wenn dieser Parameter nicht angegeben wird, gilt erstens der Wert aus dem TCA `$GLOBALS['TCA'][TabellenName]['interface']['maxSingleDBListItems']` und wenn dieser nicht angegeben ist `100`. Mithilfe von `recordsPerPage` ist es auch möglich mehr als 100 Datensätze anzeigen zu lassen.

```html
<f:be.tableList tableName="tt_content" fieldList="{0: 'header', 1: 'bodytext'}" storagePid="1" levels="5" />
```

### Beispiel: Datensatz direkt anzeigen

Normalerweise muss man immer über das Modul Web->Anzeigen oder über das Kontextmenü navigieren, um eine erste Preview seines gewählten Datensatzes zu erhalten. Viel einfacher geht es, wenn man den Titel eines Datensatzes (Der Wert aus der ersten Spalte) mit dieser Möglichkeit verlinkt. Hierfür gibt es den Parameter clickTitleMode, den ihr auf "show" setzen müsst. Zusätzlich habe ich in dieses Beispiel nach einen Wechselnden Hintergrund je Datenzeile implementiert.

```html
<f:be.tableList tableName="tt_content" fieldList="{0: 'header', 1: 'bodytext'}" storagePid="1" levels="5" alternateBackgroundColors="TRUE" clickTitleMode="show" />
```

## f:be.uri

{{% badge style="green" icon="angle-double-up" %}}TYPO3 9.0{{% /badge %}}

Erstellt eine URI zu einem TYPO3 Backend Modul.

{{% notice style="info" title="Hinweis" icon="info" %}}
Das Backend Routing wurde zwar mit TYPO3 7.5 eingeführt, dennoch wurde bei vielen Backend-Modulen die URI zu einem anderen Backend-Modul weiterhin über die TYPO3 API PHP-seitig erstellt und an die View übergeben. Der Wunsch, diese URIs auch in der View selbst zu erstellen kam später, weshalb dieser ViewHelper erst mit TYPO3 9.0 eingeführt wurde.
{{% /notice %}}

### Parameter

| Parameter     | Erklärung                                                                                                                                                               | Standardwert |
|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| route         | Name der Route. Im Backend gibt es links den Menüpunkt "Konfiguration". Wählt dort "Backendroutes" aus der Selektbox aus, um alle verfügbaren Routen anzeigen zulassen. |              |
| parameters    | Gebt der aufzurufenden Route oder auch Backend Modul ein paar zusätzliche URI Parameter mit an.                                                                         |              | 
| referenceType | Angabe des Link-Typs.<br>url: Absolute URI<br>absolute: Absoluter Pfad<br>share: Absolute URI zum Teilen mit anderen                                                    | absolute     |

### Beispiel

```html
<f:be.uri route="web_info" parameters="{id: 84}">Wechsel in das Web Info Module für die Seite 84</f:be.uri>
```
