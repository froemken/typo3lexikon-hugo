+++
title = "Ueberschriften Nummerieren"
date = 2024-01-09T20:57:12+01:00
aliases = ["uerschriften-nummerieren", "ueberschriften-nummerierung.html"]
+++

## Nummerierung der Überschriften in der Sitemap

Hier oben seht Ihr eine mit TYPO3 erzeugte Sitemap mit der Einstellung "Abschnittsübersicht (mit Seiteninhalt)". Diese Sitemap erzeugt pro Inhaltselement auf dieser Seite einen Link zu dem jeweiligen Inhaltselement. Um diese Sitemap zu nummerieren reicht bei Verwendung von css_styled_content eine Zeile TypoScript:

```typo3_typoscript
tt_content.menu.20.3.wrap = <ol class="csc-menu csc-menu-3">|</ol>
```

Unter Verwendung von fluid_styled_content wird es etwas aufwändiger. Dadurch, dass das HTML-Element für die Auflisten fest in den HTML-Templates verankert ist, müsst Ihr das Partial, das für diese Art Menü zuständig ist austauschen. Kopiert Euch dazu die Type-3.html aus dem Verzeichnis fluid_styled_content/Resources/Private/Partials/Menu/ in eine Eurer Extensions und ändert dort das <ul> in ein <ol>. Teilt Fluid nun mit, dass Euer Partial geladen werden soll:

```typo3_typoscript
lib.fluidContent.partialRootPaths.10 = EXT:my_ext/Resources/Private/Fsc/Partials/
```

## Die Überschrift der Sitemap nicht mit ins Menü

Da es ein bissl blöde ausschaut, wenn auch die Überschrift der Sitemap im Sitemapmenü auftaucht, sollten wir diese aus dem Menü entfernen. Dafür hat TYPO3 in den Inhaltselementen eine Option auf dem Tab "Zugriff" mit dem Namen "In Menüs zeigen". Hier den Haken raus und unser Menü ist fertig.

## Das Problem mit den Überschriften

Bis jetzt schaut alles schon ganz gut aus. Das Einzige was noch nervt sind die Überschriften der Inhaltselemente, denn bei diesen fehlt die Nummerierung, wie wir sie in der Sitemap haben.

Dieser Text hier steht in einem Inhaltselement OHNE Überschrift. Wenn alles funktioniert, darf dieser nicht in der Sitemap erscheinen und auch nicht die Nummerierung beeinflussen.

## Eine TypoScript-Lösung für css_styled_content

Im folgenden TypoScript wird die Variable sectionIndex Dank LOAD_REGISTER mit jedem Aufruf um den Wert 1 erhöht, aber nur wenn eine Überschrift gesetzt wurde. Mit prioriCalc wird dann der neue sectionIndex berechnet.

Im unteren Abschnitt wird eigentlich nur noch überprüft, ob es sich bei dem aktuellen Inhaltselement NICHT um das Erste handelt, denn wir wollen vor der Überschrift der Sitemap keine Nummerierung haben. Es soll erst mit dem zweiten Inhaltselement angefangen werden zu zählen.

```typo3_typoscript
# this must be 1, because of the both RESTORE_REGISTER
lib.stdheader.1 = LOAD_REGISTER
lib.stdheader.1 {
  sectionIndex.cObject = TEXT
  sectionIndex.cObject {
    data = register:sectionIndex
    # calculate only when not the first contentelement
    if.isGreaterThan.data = COBJ:parentRecordNumber
    if.value = 1
    # appent 1 if a header is set
    append = TEXT
    append.if.isTrue.field = header
    append.value = +1
  }
  sectionIndex.prioriCalc = intval
}
lib.stdheader.10.setCurrent.prepend = TEXT
lib.stdheader.10.setCurrent.prepend {
  if.isGreaterThan.data = COBJ:parentRecordNumber
  if.value = 1
  data = register:sectionIndex
  noTrimWrap = ||. |
}
```

## DataProcessor für fluid_styled_content

Unter Verwendung von fluid_styled_content könnte man zwar auch die TypoScript-Lösung anwenden, müsste aber dann die TypoScript-Struktur mit einem COA-Element erweitern. Kann man machen, aber die Idee von fluid_styled_content ist die Reduzierung von TypoScript. Als Alternative zeige ich Euch hier eine Lösung mit Hilfe eines DataProcessors, den ich in meine Extension eingebaut habe:

`typo3lexikon/Classes/Frontend/DataProcessing/HeaderProcessor.php`

```php
<?php
namespace StefanFroemken\Typo3lexikon\Frontend\DataProcessing;
use TYPO3\CMS\Frontend\ContentObject\ContentObjectRenderer;
use TYPO3\CMS\Frontend\ContentObject\DataProcessorInterface;
use TYPO3\CMS\Frontend\Controller\TypoScriptFrontendController;

class HeaderProcessor implements DataProcessorInterface
{
    /**
     * Process content object data
     * 
     * @param ContentObjectRenderer $cObj The data of the content element or page
     * @param array $contentObjectConfiguration The configuration of Content Object
     * @param array $processorConfiguration The configuration of this processor
     * @param array $processedData Key/value store of processed data (e.g. to be passed to a Fluid View)
     * @return array the processed data as key/value store
     */
    public function process(
        ContentObjectRenderer $cObj,
        array $contentObjectConfiguration, 
        array $processorConfiguration,
        array $processedData
    ) {
        if (empty($this->getTypoScriptFrontendController()->register['currentIndexForHeader'])) {
            $this->getTypoScriptFrontendController()->register['currentIndexForHeader'] = 1;        
        }
       
        // don't create header numbers for first element
        if ($cObj->parentRecordNumber !== 1) { 
            if (!empty($processedData['data']['header'])) {
                $currentIndex = $this->getTypoScriptFrontendController()->register['currentIndexForHeader'];
                $processedData['data']['header'] = $currentIndex . '. ' . $processedData['data']['header'];
                $this->getTypoScriptFrontendController()->register['currentIndexForHeader'] += 1;   
            }
        }
        
        return $processedData;
    }   

    /**
     * get TypoScriptFrontendController
     *
     * @return TypoScriptFrontendController
     */
    protected function getTypoScriptFrontendController()
    {
        return $GLOBALS['TSFE'];
    } 
}
```

Mithilfe der dataProcessing-Eigenschaft des FLUIDTEMPLATEs könnt Ihr nun Euren DataProcessor einbinden. In diesem Fall wird der DataProcessor nur für das TextMedia-Element eingebunden. Für das TextMedia-Element sind bereits 2 DataProcessoren (10 und 20) registriert, deshalb habe ich mich hier für einen deutlich höheren Key (100) entschieden:

```typo3_typoscript
tt_content.textmedia.dataProcessing {
  100 = StefanFroemken\Typo3lexikon\Frontend\DataProcessing\HeaderProcessor
}
```
