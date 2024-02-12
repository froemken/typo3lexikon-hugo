+++
title = "Be"
date = 2024-01-09T20:28:02+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["be.html"]
+++

## f:be.container

### Parameter

| Parameter | Erklärung | Standardwert |
|-----------|-----------|--------------|
| pageTitle | BE-Module werden in einem Frame dargestellt, von daher macht ein Seitentitel wenig sind. Einfach leer lassen | leer|
| enableJumpToUrl | Lasst diesen Parameter eingeschaltet, wenn ihr den ActionMenu-ViewHelper verwenden wollt, da dieser die entsprechenden JavaScripte dazu liefert. | aktiviert |
| enableClickMenu | Wenn aktiviert, wird das JavaScript für die Contextmenüs eingebunden | aktiviert |
| loadPrototype | Wenn aktiviert, wird das Prototype-JS-Framework eingebunden | aktiviert |
| loadScriptaculous | Wenn aktiviert, wird das Zusatzpaket für Prototype eingebunden | deaktiviert |
| scriptaculousModule | Ihr könnt hier noch weitere Module für das Scriptaculouspaket aktivieren | aktiviert |
| loadExtJs | Wenn aktiviert, wird das ExtJS-Framework eingebunden | deaktiviert |
| loadExtJsTheme | Wenn aktiviert, werden Vorgaben für die Grafischen Elemente eingebunden. | aktiviert |
| extJsAdapter | Statt dem Standard ext-base kann hier ein anderer Adapter angegeben werden. | standard |
| enableExtJsDebug | Sollte nur aktiviert werden, wenn man auf Basis von ExtJS entwickelt. | deaktiviert |
| addCssFile | Eine CSS-Datei einbinden | keine |
| addJsFile | Eine JavaScript-Datei einbinden | keine |

Zuallererst solltet ihr Euch eine grundsätzliche Frage stellen: Willst Du etwas entwickeln ganz im Stile von TYPO3 oder willst Du etwas völlig Eigenes bauen. Wenn ihr Euch für etwas völlig Eigenes entscheidet, dann braucht ihr diesen ViewHelper überhaupt nicht. Bindet in diesem Falle Eure benötigten StyleSheets und JavaScripte selbst ein.

Wenn ihr allerdings etwas bauen wollt, dass sich an das TYPO3-Design anlehnt und möglichst kompatibel ist, dann solltet ihr diesen ViewHelper auf jeden Fall verwendet. Das was damals zu Zeiten von MediumDoc und Co. eingebunden wurde, benötigt heute nur noch diesen Zweizeiler mit knapp 10 Parametern.

## f:be.pageInfo

Dieser ViewHelper benötigt keine Parameter. Er zeigt einfach nur das Icon der aktuellen Seite an mit der Information, um welche pid es sich dabei handelt.

### Beispiel

```html
<f:be.pageInfo />
```

## f:be.pagePath

Dieser ViewHelper benötigt keine Parameter. Er zeigt den Pfad zur aktuell gewählten Seite an. Diese Information ist vor allem bei sehr großen Webseiten nützlich, wenn Seiten eher über eine Suche gefunden werden. Dann ist es schon mal schwierig herauszufinden, wo sich diese Seite im Wust der ganzen Navigationen und Unterseiten im Seitenbaum überhaupt befindet.

## f:be.tableList

Sehr geiles Teil. Was wir aus dem Modul Web->Liste kennen können wir nun selbst verwenden und super einfach konfigurieren, wie die folgenden Beispiele zeigen.

### Parameter

| Parameter | Erklärung | Standardwert |
|-----------|-----------|--------------|
| tableName | Der Name der Tabelle ||
| fieldList | Welche Spalten der Tabelle sollen angezeigt werden (Arraynotation) ||
| storagePid | Die Daten welcher Seite sollen angezeigt werden? Wenn nichts anderen angegeben wurde, werden die Datensätze der Seite angezeigt, die über persistence.storagePid angegeben wurde. ||
| levels | Falls die Seite aus storagePid noch Unterordner enthält, könnt ihr hier angeben wie viele Ebenen tiefer nach weiteren Datensätzen gesucht werden soll. | 1 |
| filter | Gebt hier ein Suchwort ein, nach dem Eure Datensätze gefiltert werden sollen. ||
| recordsPerPage | Wie viele Datensätze dürfen maximal auf einer Seite angezeigt werden. | Angabe aus TCA, sonst 100 |
| sortField | Nach welchem Feld sollen die gefundenen Datensätze sortiert werden |
| sortDescending | Wenn aktiviert, dann werden die Datensätze rückwärts sortiert. | deaktiviert |
| readOnly | Wenn aktiviert, dann werden die Bearbeitungssymbole nicht mehr angezeigt. | deaktiviert |
| enableClickMenu | Wenn aktiviert, dann kann das Contextmenü verwendet werden | aktiviert |
| clickTitleMode | Auswahl von `edit`, `info` und `show`. Falls ihr `show` auswählt, dann klappt das nur bei der Tabelle `pages` und `tt_content`. Normalerweise müsst ihr immer erst über das Kontextmenü oder über ein anderes Modul in diesen Bereich klicken. Nun reicht ein Klick auf den Titel des Datensatzes. ||
| alternateBackgroundColors | Wenn aktiviert, wechseln sich die Hintergrundfarben je Datenzeile ab. | deaktiviert |

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
