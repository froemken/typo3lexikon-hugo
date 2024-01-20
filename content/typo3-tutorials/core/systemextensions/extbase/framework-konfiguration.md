+++
title = "Framework Konfiguration"
date = 2024-01-09T20:26:24+01:00
aliases = ["extbase-framework-konfiguration"]
+++

Während wir im damaligen Templatesystem alle TypoScript-Angaben selbst im Skript verankern mussten, bringt Extbase bereits ein paar TS-Strukturen mit. So können wir unseren storage-Folder nicht mehr mit jeder x-beliebigen TS-Variable abfragen im Script abfragen, sondern müssen uns an die Vorgaben von Extbase halten.

Riskiert mal einen Blick in den `ConfigurationManager`. In der Methode `getConfiguration()` könnt ihr gut erkennen, dass ihr dort drei Möglichkeiten habt auf die Teilbereiche Eurer TypoScript-Struktur zuzugreifen:

- Settings (Ein Teilbereich der Plugin Konfiguration, der auch in Fluid-Templates zur Verfügung steht)
- Plugin Konfiguration (Der zusammengeführte Teilbereich aus `config.tx_extbase`, `plugin.tx_[ext]` und `plugin.tx_[ext]_[plugin]`)
- Full TypoScript (Das komplette TypoScript, das für die aktuelle Seite gültig ist.)

## Lade-Reihenfolge der Konfigurationen

Im Folgenden erkläre ich Euch die Lade-Reihenfolge der unterschiedlichen Konfigurationsmöglichkeiten anhand der storagePid. Basis in diesem Prozess ist das komplette TypoScript der aktuellen Seite, das ihr euch z.B. über den TypoScript-Objekt-Browser im TYPO3 Backend anschauen könnt.

### config.tx_extbase

In dem kompletten Seiten TypoScript wurde bereits das Standard TypoScript von Extbase eingebunden, dass Ihr in dieser Datei findet:

`typo3/sysext/extbase/ext_typoscript_setup.txt`

Über TypoScript Templates hättet Ihr schon jetzt das erste Mal die Möglichkeit global für alle Extbase-Plugins auf dieser Seite und allen Unterseiten eine oder mehrere storagePids anzugeben. Hier ein Beispiel, auch wenn ich diese Art der Konfiguration als eher untypisch empfinde:

`config.tx_extbase.persistence.storagePid = 123`

### DEFAULT_BACKEND_STORAGE_PID

Im nächsten Schritt wird geprüft, ob im vorherigen Schritt eine StoragePid gesetzt wurde. Falls nicht, wird diese mithilfe der Konstante `DEFAULT_BACKEND_STORAGE_PID` auf 0 gesetzt.

Heißt: Wenn Ihr vergessen solltet in den weiteren Konfigurationen eine StoragePid anzugeben, dann wird die Query mit pid=0 ausgeführt, was in den meisten Fällen zu einem leeren Ergebnis führen wird.

### plugin.tx_myext

Im nächsten Schritt wird die TypoScript Konfiguration für die komplette Extension aus dem Bereich `plugin.tx_[ext]` geladen

`plugin.tx_events2.persistence.storagePid = 234`

### plugin.tx_myext_myplugin

Alle Werte des vorherigen Abschnitts können nun mit der Plugin individuellen Konfiguration überschrieben werden.

`plugin.tx_events2_events.persistence.storagePid = 345`

### Datenspeicher

Einige Extensions lassen es zu, dass ihr im `tt_content` Datensatz über die Spalte `pages` eine StoragePid hinterlegen könnt. Der hier angegebene Wert überschreibt die bisherigen StoragePids.

### plugin.tx_myext_myplugin

Ist das schräg oder ist das schräg? Den Wert hatten wir doch gerade eben schon! Richtig! Mit dieser Plugin individuellen Konfiguration wird die StoragePid aus dem tt_content Datensatz wieder überschrieben.

{{% notice style="info" title="Wichtig" icon="exclamation" %}}
Nur die Plugin individuelle Konfiguration kann hier an dieser Stelle den tt_content Wert überschreiben. Nicht jedoch die Konfiguration aus `plugin.tx_[ext]`.
{{% /notice %}}

### Flexform

Zu guter Letzt wird die Konfiguration aus der Flexform ausgelesen. Diese Konfiguration überschreibt alle bisherigen Konfigurationen.
