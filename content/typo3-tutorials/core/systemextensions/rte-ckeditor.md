+++
title = "RTE Ckeditor"
date = 2024-01-09T20:14:42+01:00
aliases = ["rte-ckeditor.html"]
+++

Der CKEditor ist ein sehr umfangreicher und vor allem sehr anpassungsfähiger WYSIWYG Editor (What you see is what you get). Als einer der beliebtesten Editoren, auch in anderen CMS, freuen wir uns diesen Editor seit TYPO3 8.5.0 mit on Board zuhaben. Damit hat die bereits existierende Systemextension `EXT:rtehtmlarea` ausgedient.

## rte_ckeditor und seine Presets

Seit Einführung in TYPO3 8.5.0 kann der CKEditor mittels pageTSconfig konfiguriert werden. Ab TYPO3 8.6.0 kam die Konfiguration mittels YAML hinzu, die man über sogenannte Presets definiert.

## Die Presets

Mittels der Presets könnt ihr eine YAML Datei referenzieren, die die CKEditor Konfiguration beinhaltet. In dieser Konfiguration wird nicht nur der CKEditor konfiguriert, sondern auch die Verarbeitung des Inhalts beim Speichern in die Datenbank und Auslesen von der Datenbank.

Nach Aktivierung der Systemextension `rte_ckeditor` stehen euch 3 Presets von TYPO3 zur Verfügung: `default`, `minimal` und `full`. Als TYPO3 Entwickler könnt ihr weitere Presets im TYPO3 System registrieren. Schaut euch dazu mal die `ext_localconf.php` vom `rte_ckeditor` an:

```php
if (empty($GLOBALS['TYPO3_CONF_VARS']['RTE']['Presets']['default'])) {
    $GLOBALS['TYPO3_CONF_VARS']['RTE']['Presets']['default'] = 'EXT:rte_ckeditor/Configuration/RTE/Default.yaml';
}
if (empty($GLOBALS['TYPO3_CONF_VARS']['RTE']['Presets']['minimal'])) {
    $GLOBALS['TYPO3_CONF_VARS']['RTE']['Presets']['minimal'] = 'EXT:rte_ckeditor/Configuration/RTE/Minimal.yaml';
}
if (empty($GLOBALS['TYPO3_CONF_VARS']['RTE']['Presets']['full'])) {
    $GLOBALS['TYPO3_CONF_VARS']['RTE']['Presets']['full'] = 'EXT:rte_ckeditor/Configuration/RTE/Full.yaml';
}
```

Eine Registrierung für ein eigenes Preset könnte z.B. so aussehen:

```php
if (empty($GLOBALS['TYPO3_CONF_VARS']['RTE']['Presets']['presetNurBodytext'])) {
    $GLOBALS['TYPO3_CONF_VARS']['RTE']['Presets']['presetNurBodytext'] 
        = 'EXT:my_site_package/Configuration/RTE/PresetNurBodytext.yaml';
}
```

Falls ihr eigene Presets baut verwendet bitte eigene Preset Namen und überschreibt nicht die von TYPO3.

Dank dieser Presets bedarf es nur einer einzigen Zeile pageTSconfig oder einer Zeile TCA, um bis auf Spaltenebene eine individuelle CKEditor Konfiguration anzuwenden.

## Beispiel pageTSconfig

Setzen aller CKEditoren auf das Preset `presetGanzeWebseite`

```typo3_typoscript
RTE.default.preset = presetGanzeWebseite
```

Speziell für die Spalte `bodytext` der Tabelle `tt_content` wird statt `presetGanzeWebseite` das Preset `presetNurSpalteBodytext` verwendet.

```typo3_typoscript
RTE.config.tt_content.bodytext.preset = presetNurSpalteBodytext
```

## Beispiel TCA

Folgendes Beispiel klappt nur, wenn für die angegebene Spalte im TCA auch enableRichtext aktiviert wurde.

```php
$GLOBALS['TCA']['tx_news_domain_model_news']['columns']['bodytext']['config']['richtextConfiguration']
    = 'presetNurBodytextDerNews';
```

## Priorisierung der Presets

Auf der Suche nach dem zu verwendenden Preset wird folgende Reihenfolge berücksichtigt: von höchster Priorität nach niedrigste Priorität:

- Auslesen des Presets von RTE.config.[Tabellenname].[Spaltenname].types.[Typ].preset
- Auslesen des Presets von RTE.config.[Tabellenname].[Spaltenname].preset
- Auslesen des Presets von TCA richtextConfiguration
- Auslesen des Presets von RTE.default.preset

Wenn kein Preset gefunden, dann verwende Preset: `default`

## Konfiguration mittels pageTSconfig

Die Konfiguration des CKEditors lässt sich wie schon zu `rtehtmlarea` Zeiten auch mittels pageTSconfig konfigurieren. Im Preset `default` stehen euch üblicherweise die Format-Tags p, h1, h2, h3, h4, h5 und pre zur Verfügung. Mit diesem Einzeiler könnt ihr ein paar Format-Tags entfernen:

```typo3_typoscript
RTE.default.editor.config.format_tags = p;h2;h3;h4
```

Um das Verhalten der Inhalte beim Speichern und Laden des Editors zu beeinflussen stellt euch TYPO3 die proc Eigenschaft zur Verfügung. Hier ein Beispiel, um die Überschrift H4 beim Speichern zu entfernen:

```typo3_typoscript
RTE.default.proc {
    exitHTMLparser_db = 1
    exitHTMLparser_db {
      allowTags = p,h2,h3
    }
}
```

Achtet bitte darauf, dass ihr euch im Bereich `RTE.default.editor` in der Konfiguration vom CKEditors befindet und ihr die Werte bitte so eintragt, wie ihr Sie in der Dokumentation des CKEditors vorfindet. In meinem Beispiel werden die Format Tags mit Semikolon getrennt. Das ist Vorgabe vom CKEditor. Innerhalb der `proc` Eigenschaft befinden wir uns jedoch in einer TYPO3 Konfiguration, in der mehrere Werte üblicherweise mit Komma getrennt werden.

## Konfiguration mittels YAML Dateien

Seit TYPO3 8.6.0 lässt sich der CKEditor auch mittels YAML Dateien konfigurieren, die mithilfe der Presets vorkonfiguriert werden müssen. Siehe Erklärung weiter oben. Warum man sich hier allerdings für YAML entschieden hat, erklärt sich mir leider nicht. Ja, es ist übersichtlicher, aber sämtliche in YAML angegebenen Tags werden bei der Verarbeitung durch den YAML-Parser in ein Array umgewandelt. Schön, dass wir dann ein Array haben, aber solange der TYPO3 Core intern die RTE Konfiguration größtenteils noch immer als Sting verarbeitet und Komma separierten Listen benötigt helfen uns die Arrays ziemlich wenig. Hinzu kommt, dass sämtliche Beispiele aus der Dokumentation des CKEditors im JavaScript und JSON Stil sind und man jede Konfiguration erstmal in YAML konvertieren darf. Copy&Paste ist hier nicht.

## Erstellen eines eigenen Presets

Diesen Eintrag habe ich in die `ext_localconf.php` meines SitePackages gepackt. Alternativ könnt ihr diese Zeile auch in die `AdditionalConfiguration.php` packen.

```php
$GLOBALS['TYPO3_CONF_VARS']['RTE']['Presets']['sfGlobalPreset'] 
   = 'EXT:my_site_package/Configuration/RTE/SfGlobalPreset.yaml';
```

## Standard-Preset setzen

Tragt diese pageTSconfig in den Seiteneigenschaften der Rootseite Eurer Webseite ein:

```typo3_typoscript
RTE.default.preset = sfGlobalPreset
```

## YAML-Konfiguration erstellen

Hierbei handelt es sich um eine absolut minimalistische Konfiguration. Da hier kaum etwas konfiguriert ist, greift CKEditor und TYPO3 auf diverse Standardeinstellungen zurück. Somit werden mit dieser Konfiguration z.B. Buttons angezeigt, die zwar funktionieren, aber sich das Ergebnis nicht abspeichern lässt, weil ich mittels `allowTags` ja nur die 3 Tags erlaube.

Hinweis: Ohne den styleSet-Eintrag wird der CKEditor überhaupt nicht geladen.

```yaml
processing:
  mode: default
  exitHTMLparser_db:
    allowTags: "p, h2, h3"
    editor:
      config:
        format_tags: "p;h2;h3;h4"
        stylesSet: []
```

## YAML Dateien importieren

Falls eure YAML Datei arg groß werden sollte oder ihr mehrere Presets konfiguriert habt, kann es Sinn machen bestimmte Teilbereiche der Konfiguration auszulagern. Nutzt dazu die `imports` Eigenschaft innerhalb eurer YAML Datei.

```yaml
imports:
- { resource: "EXT:my_site_package/Configuration/RTE/Processing.yaml" }
- { resource: "EXT:my_site_package/Configuration/RTE/Plugins.yaml" }

processing:
  mode: default
  editor:
    config:
      stylesSet: []
```

## Konfiguration Mischmasch

Ihr kennt jetzt die Presets, die Konfiguration mittels pageTSconfig und YAML. All das lässt sich auch noch kombinieren. Zuallererst wird die Reihenfolge der Preset Prioritäten durchlaufen (siehe oben). Wenn ein Preset gefunden wurde und die dazugehörende YAML-Datei existiert, dann wird diese als Basis geladen. Ab jetzt könnt Ihr mit pageTSconfig diese geladene Konfiguration individuell überschreiben. TYPO3 kombiniert dazu in der `Richtext.php` zunächst die Konfiguration aus `RTE.default`, überschreibt diese mit der Konfiguration aus `RTE.config.[Tabellenname].[Spaltenname]` und überschreibt diese Konfiguration dann nochmals, sofern vorhanden, mit der Konfiguration aus `RTE.config.[Tabellenname].[Spaltenname].types.[type]`.

Im Backend habt ihr über das Info-Modul nur Zugriff auf die RTE Konfiguration, die ihr über pageTSconfig erzeugt habt. Eine vollständig kombinierte Fassung aller Einstellungen inkl. der aus den YAML-Dateien ist leider nicht möglich.

## CKEditor Konfiguration nach YAML migrieren

Ich habe auf der CKEditor Webseite einen Konfigurator gefunden, mit dem ihr euch den CKEditor zusammen klicken könnt. Sie kann zwar nicht 1zu1 in YAML übertragen werden, aber trotzdem nimmt euch das Teil einen Großteil an Arbeit und Sucherei ab.

## Beispiel aus dem Konfigurator

```javascript
CKEDITOR.editorConfig = function( config ) {
    config.toolbar = [
        { name: 'document', items: [ 'Source' ] },
        { name: 'basicstyles', items: [ 'Bold', 'Italic', 'Underline' ] },
        { name: 'paragraph', items: [ 'NumberedList', 'BulletedList', '-', 'JustifyLeft', 'JustifyRight' ] },
        '/',
        { name: 'styles', items: [ 'Styles', 'Format' ] },
        { name: 'about', items: [ 'About' ] }
    ];
};
```

## Migrierte Fassung für YAML

Grob muss somit die erste und letzte Zeile weg und dann interessiert uns nur noch der Wert hinter dem `config.`. Dann noch alle Anführungszeichen weg und schon haben wir eine YAML Fassung der CKEditor Konfiguration:

```yaml
editor:
  config:
    toolbar:
    - { name: document, items: [ Source ] }
    - { name: basicstyles, items: [ Bold, Italic, Underline ] }
    - { name: paragraph, items: [ NumberedList, BulletedList, -, JustifyLeft, JustifyRight ] }
    - '/'
    - { name: styles, items: [ Styles, Format ] }
    - { name: about, items: [ About ] }
```

## Beispiele für CKEditor Konfiguration

Die wichtigsten Einstellungen, um den CKEditor zu konfigurieren, findet ihr in der CKEditor Dokumentation (englisch). Theoretisch sollten sich alle dort aufgeführten Einstellungen in die YAML Konfiguration umbauen lassen.

### contentsCss

Anstatt mithilfe von `stylesSet` für jeden einzelnen Eintrag, ob Überschrift, Fließtext oder Vorformattiert (pre), könnt ihr auch eine CSS Datei definieren. Wenn ihr in dieser CSS Datei nur eine CSS Klasse definiert, dann kann diese Klasse jedem HTML-Tag zugewiesen werden. Definiert ihr eine CSS Klasse jedoch so, dass sie nur einem bestimmten HTML-Tag zugewiesen ist, dann kann auch im CKEditor diese Klasse nur dem entsprechenden HTML-Tag zugewiesen werden.

```yaml
editor:
  config:
    contentsCss: "EXT:my_site_package/Resources/Public/Css/CkEditor.css"
```

### justifyClasses

Angabe von exakt 4 CSS Klassennamen in folgender Reihenfolge: Links, Zentriert, Rechts, Blocksatz

```yaml
editor:
  config:
    justifyClasses:
    - text-left
    - text-center
    - text-right
    - text-justify
```

### removeButtons

Hiermit könnt ihr einzelne Buttons entfernen. Bei Entfernung mehrerer Buttons macht es evtl. mehr Sinn das CKEditor Plugin zu deaktivieren als diese Eigenschaft völlig auszureizen

```yaml
editor:
  config:
    removeButtons:
    - Subscript
    - Superscript
```

### stylesSet

Mit stylesSet könnt ihr das Aussehen der Schrift in den Selectboxen und auch im Text selbst gestalten und die Vorgaben aus contentsCss individuell überschreiben.

```yaml
editor:
  config:
    stylesSet:
    - { name: "Ich bin fett", element: "strong" }
    - { name: "Ich bin schräg", element: "em", styles: { color: "blue" } }
    - { name: "Ich bin zerstochen", element: "strike", attributes: { class: "strike" } }
```
