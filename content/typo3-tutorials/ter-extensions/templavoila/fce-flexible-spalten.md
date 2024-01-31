+++
title = "FCE: Flexible Spalten"
linkTitle = "Flexible Spalten"
date = 2024-01-09T20:53:23+01:00
aliases = ["flexible-spalten.html"]
+++

Wie viel Spalten dürfen es denn sein? 2 Spalten vielleicht? Dann installieren Sie bitte: `fdfx_2cols`. Oder doch eher 3 Spalten? Dann installieren Sie bitte: `fdfx_3cols`. 4 Spalten vielleicht? Sorry, aber dann muss erst eine neue Extension programmiert werden.

Wer im TER schon mal nach dem Stichwort `cols` gesucht hat, wird nur 5 Extensions finden. 3 davon beschäftigen sich mit 2-3 Spalten und die anderen Beiden bauen eher auf Tabellen auf.

Eine Extension mit der man x-beliebig viele Spalten (ob Sinn oder Unsinn) erzeugen kann, gibt es derzeit nicht. Einige Grafikdesigner wünschen sich von mir 3-Spaltige Contentelemente aber mal muss das Bild links sein, dann wieder rechts und mal muss Spalte 1 und 2 verbunden werden zu einer großen Spalte und Spalte 3 bleibt weiterhin für Bild oder Text in einer fest vorgegebenen Breite verfügbar.

Meine erste Idee war ein FCE aus dem Paket `templavoila`. Dank sogenannter Sections kann ich einen bestimmten Codebereich im HTML-Template beliebig oft anlegen.

## Was wird unser FCE können?

- Ihr könnt beliebig viele Spalten anlegen
- Ihr könnt wählen, ob Ihr Text oder ein Bild in Eurer Spalte haben wollt.
- Die Eingabefelder wechseln automatisch je nach Auswahl (Text oder Bild)
- Einige Werte können global definiert werden. Können aber innerhalb jeder einzelnen Spalte überschrieben werden.

## Also los geht's

Ich erwarte hier ein System, dass bereits auf Basis von `templavoila` aufgebaut ist und ihr euch mit `templavoila` zumindest ein bisschen mit auskennt.

Erstellt im `fileadmin/templates` Ordner eine neue Datei mit dem Namen `flex-cols.html` und folgendem Inhalt:

```html
<html>
<head>
<title>hallo</title>
<link rel="stylesheet" type="text/css" href="style/flex-cols.css" media="all" />
</head>
<body>

<div class="flex-section">
    <div class="flex-container">
        <div class="flex-cols">###CONTENT###</div>
    </div>
</div>

</body>
</html>
```

Im Unterverzeichnis `style` habe ich noch die zum Template gehörende Datei `flex-cols.css` mit diesem Inhalt angelegt:

```css
div.flex-col {
  float: left;
}

.clearer {
  clear: both;
  height: .1px;
  font-size: 0;
  line-height: 0;
}
```

Damit unsere Spalten nicht untereinander angezeigt werden :-)

Klick nun auf das Icon von unserer `flex-cols.html` und wählt im Contextmenü den Eintrag `TemplaVoila`. Im Mappingbereich erstellen wir nun folgende Zuordnungen:

- ROOT -> BODY (inner)
- Section (field_section) -> DIV.section (inner): SC:Abschnitt für Elementen
- Container (field_container) -> DIV.container (inner): CO:Container für Elemente
- Render (field_render) -> DIV.flex-cols (outer): EL:Element: (TypoScript-Objectpath: lib.flexcols)
- Type (field_type) -> NOT Mapped: Auswahlliste
- Text (field_text) -> NOT Mapped (Textbereich für Textinhalt)
- Image (field_image) -> NOT Mapped (Bildfeld mit fester B+H)
- Image width (field_imagewidth) -> NOT Mapped (Ganzzahliger Wert)
- Image border (field_imgborder) -> NOT Mapped (Markierungsfeld)

Dank der Section können wir nun mehrere Elemente hintereinander anlegen. Der Renderer holt sich die benötigten Daten von den anderen Elementen, die nicht gemapped sind (NOT Mapped) und baut diese zu einem großen Inhaltselement zusammen. Mit Type werden wir dem Redakteur später die Möglichkeit geben zwischen Text- oder Bild-Spalte entscheiden zu können.

Klickt nun auf Speichern und vergebt einen Namen wie `Flex-Cols`. Achtet darauf in der Selectbox `Inhaltselement` zu wählen und nicht `Seitentemplate`!

Wählt nun über TemplaVoila euer neu erstelltes FCE aus und editiert das XML. Ich habe euch hier mein vollständiges XML zur Verfügung gestellt und euch geänderte Bereich fett hervorgehoben:

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes" ?>
<T3DataStructure>
    <meta type="array">
        <langDisable>1</langDisable>
    </meta>
    <ROOT type="array">
        <tx_templavoila type="array">
            <title>ROOT</title>
            <description>Wählen Sie das HTML-Element der Seite, das Sie als übergeordnetes Container-Element der Vorlage möchten.</description>
        </tx_templavoila>
        <type>array</type>
        <el type="array">
            <field_section type="array">
                <type>array</type>
                <tx_templavoila type="array">
                    <title>Section</title>
                    <description>Section</description>
                    <eType>input</eType>
                    <tags>div:inner</tags>
                    <preview></preview>
                </tx_templavoila>
                <el type="array">
                    <field_container type="array">
                        <type>array</type>
                        <tx_templavoila type="array">
                            <title>Container</title>
                            <description>Container</description>
                            <eType>input</eType>
                            <tags>div:inner</tags>
                            <TypoScript></TypoScript>
                            <proc type="array">
                                <int>0</int>
                                <HSC>0</HSC>
                                <stdWrap></stdWrap>
                            </proc>
                            <preview></preview>
                        </tx_templavoila>
                        <TCEforms type="array">
                            <label></label>
                            <config type="array">
                            </config>
                        </TCEforms>
                        <el type="array">
                            <field_render type="array">
                                <tx_templavoila type="array">
                                    <title>Render</title>
                                    <description>Render</description>
                                    <sample_data type="array">
                                        <numIndex index="0">RENDER</numIndex>
                                    </sample_data>
                                    <eType>TypoScriptObject</eType>
                                    <tags>div:outer</tags>
                                    <proc type="array">
                                        <int>0</int>
                                        <HSC>0</HSC>
                                        <stdWrap></stdWrap>
                                    </proc>
                                    <preview></preview>
                                    <TypoScriptObjPath>lib.flexcols</TypoScriptObjPath>
                                </tx_templavoila>
                            </field_render>
                            <field_type type="array">
                                <type>no_map</type>
                                <tx_templavoila type="array">
                                    <title>Type</title>
                                    <description>Type</description>
                                    <sample_data type="array">
                                        <numIndex index="0">TYPE</numIndex>
                                    </sample_data>
                                    <eType>select</eType>
                                    <TypoScript></TypoScript>
                                    <proc type="array">
                                        <int>0</int>
                                        <HSC>0</HSC>
                                        <stdWrap></stdWrap>
                                    </proc>
                                    <preview></preview>
                                </tx_templavoila>
                                <TCEforms type="array">
                                    <label>Inhaltstyp</label>
                                    <onChange>reload</onChange>
                                    <config type="array">
                                        <type>select</type>
                                        <items type="array">
                                            <numIndex index="0" type="array">
                                                <numIndex index="0"></numIndex>
                                                <numIndex index="1">0</numIndex>
                                            </numIndex>
                                            <numIndex index="1" type="array">
                                                <numIndex index="0">Text</numIndex>
                                                <numIndex index="1">1</numIndex>
                                            </numIndex>
                                            <numIndex index="2" type="array">
                                                <numIndex index="0">Bild</numIndex>
                                                <numIndex index="1">2</numIndex>
                                            </numIndex>
                                        </items>
                                        <default>0</default>
                                    </config>
                                </TCEforms>
                            </field_type>
                            <field_text type="array">
                                <type>no_map</type>
                                <tx_templavoila type="array">
                                    <title>Text</title>
                                    <description>Text</description>
                                    <sample_data type="array">
                                        <numIndex index="0">TEXT</numIndex>
                                    </sample_data>
                                    <eType>text</eType>
                                    <TypoScript></TypoScript>
                                    <proc type="array">
                                        <HSC type="integer">1</HSC>
                                        <stdWrap></stdWrap>
                                    </proc>
                                    <preview></preview>
                                </tx_templavoila>
                                <TCEforms type="array">
                                    <label>Text</label>
                                    <displayCond>FIELD:field_type:=:1</displayCond>
                                    <config type="array">
                                        <type>text</type>
                                        <cols>48</cols>
                                        <rows>5</rows>
                                    </config>
                                </TCEforms>
                            </field_text>
                            <field_image type="array">
                                <type>no_map</type>
                                <tx_templavoila type="array">
                                    <title>Image</title>
                                    <description>Image</description>
                                    <sample_data type="array">
                                        <numIndex index="0">IMAGE</numIndex>
                                    </sample_data>
                                    <eType>imagefixed</eType>
                                    <TypoScript>
10 = TEXT
10.current = 1
                                    </TypoScript>
                                    <preview></preview>
                                </tx_templavoila>
                                <TCEforms type="array">
                                    <label>Image</label>
                                    <displayCond>FIELD:field_type:=:2</displayCond>
                                    <config type="array">
                                        <type>group</type>
                                        <internal_type>file</internal_type>
                                        <allowed>gif,png,jpg,jpeg</allowed>
                                        <max_size>1000</max_size>
                                        <uploadfolder>uploads/tx_templavoila</uploadfolder>
                                        <show_thumbs>1</show_thumbs>
                                        <size>1</size>
                                        <maxitems>1</maxitems>
                                        <minitems>0</minitems>
                                    </config>
                                </TCEforms>
                            </field_image>
                            <field_imagewidth type="array">
                                <type>no_map</type>
                                <tx_templavoila type="array">
                                    <title>Image width</title>
                                    <description>Image width</description>
                                    <sample_data type="array">
                                        <numIndex index="0">Image width</numIndex>
                                    </sample_data>
                                    <eType>int</eType>
                                    <TypoScript></TypoScript>
                                    <proc type="array">
                                        <int>1</int>
                                        <HSC>0</HSC>
                                        <stdWrap></stdWrap>
                                    </proc>
                                    <preview></preview>
                                </tx_templavoila>
                                <TCEforms type="array">
                                    <label>Image width</label>
                                    <displayCond>FIELD:field_type:=:2</displayCond>
                                    <config type="array">
                                        <type>input</type>
                                        <size>4</size>
                                        <max>4</max>
                                        <eval>int</eval>
                                        <checkbox>0</checkbox>
                                        <range type="array">
                                            <upper>999</upper>
                                            <lower>25</lower>
                                        </range>
                                        <default type="integer">0</default>
                                    </config>
                                </TCEforms>
                            </field_imagewidth>
                            <field_imgborder type="array">
                                <type>no_map</type>
                                <tx_templavoila type="array">
                                    <title>Image border</title>
                                    <description>Image border</description>
                                    <sample_data type="array">
                                        <numIndex index="0">Image border</numIndex>
                                    </sample_data>
                                    <eType>check</eType>
                                    <TypoScript></TypoScript>
                                    <proc type="array">
                                        <int>0</int>
                                        <HSC>0</HSC>
                                        <stdWrap></stdWrap>
                                    </proc>
                                    <preview></preview>
                                </tx_templavoila>
                                <TCEforms type="array">
                                    <label>Image border</label>
                                    <displayCond>FIELD:field_type:=:2</displayCond>
                                    <config type="array">
                                        <type>check</type>
                                        <default type="integer">0</default>
                                    </config>
                                </TCEforms>
                            </field_imgborder>
                        </el>
                    </field_container>
                </el>
                <section>1</section>
            </field_section>
        </el>
    </ROOT>
</T3DataStructure>
```

Mithilfe von `onChange` und `reload` schaffen wir uns eine Möglichkeit, wie im Contentelement-Wizzard von TYPO3 auch, die Eingabemaske neu zu laden und damit andere Inhaltselemente anhand der Type-Selectbox (Text oder Bild) mithilfe der `displayCond` Zeilen anzeigen zu lassen.

Jetzt müssen nur noch unsere Daten zusammengeführt werden zu einem Inhaltselement. Geht dazu entweder in euer Seitentemplate (Setup und Constants) oder erstellt für die gewünschte Seite ein +ext-Template.

### Constants

```typo3_typoscript
flexcols {
  imageWidth = 150
  colWidth =
  borderWidth = 2
  borderStyle= solid
  borderColor = #000000
}
```

### Setup

```typo3_typoscript
lib.flexcols = COA
lib.flexcols {
  5 = LOAD_REGISTER
  5.first = first
  5.first.if.isTrue.data = REGISTER:tx_templavoila_pi1.sectionIsFirstItem
  
  6 = LOAD_REGISTER
  6.last = last
  6.last.if.isTrue.data = REGISTER:tx_templavoila_pi1.sectionIsLastItem
  
  7 = LOAD_REGISTER
  7.styles.cObject = COA
  7.styles.cObject.10 = TEXT
  7.styles.cObject.10 {
    value = width:
    wrap = |{$flexcols.colWidth};
    if.isTrue = {$flexcols.colWidth}
  }
  
  10 = COA
  10 {
    10 = TEXT
    10 {
      field = field_text
      if.isTrue.field = field_text
    }

    20 = IMAGE
    20 {
      file.import = uploads/tx_templavoila/
      file.import.field = field_image
      file.width = {$flexcols.imageWidth}
      file.width.override.field = field_imagewidth
      params.stdWrap.cObject = TEXT
      params.stdWrap.cObject {
        value = style="border:{$flexcols.borderWidth}px {$flexcols.borderStyle} {$flexcols.borderColor};"
        if.isTrue.field = field_imgborder
      }
      if.isTrue.field = field_image
    }
    stdWrap.dataWrap = <div class="flex-col flex-col-{REGISTER:tx_templavoila_pi1.sectionPos} {REGISTER:first} {REGISTER:last}" style="{REGISTER:styles}">|</div>
    stdWrap.append = TEXT
    stdWrap.append {
      value = <div class="clearer"></div>
      if.isTrue.data = REGISTER:tx_templavoila_pi1.sectionIsLastItem
    }
  }

  100 = RESTORE_REGISTER
  101 = RESTORE_REGISTER
}
```
