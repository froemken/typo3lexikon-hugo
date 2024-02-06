+++
title = "FLUIDTEMPLATE"
date = 2024-01-09T20:58:10+01:00
aliases = ["fluidtemplate.html"]
+++

Nach vielen Jahren der Marker- und SubParts ersetzenden Variante mithilfe von purem TypoScript bestimmte Teilbereiche einer HTML-Datei zu ersetzen, wurde es Zeit mehr Logik direkt, wie bei Smarty, in das Template selbst auszulagern. TypoScript versteht nicht jeder oder ist für Viele zu komplex. Mithilfe der seit TYPO3 4.3 verfügbaren Fluid Engine haben wir ein sehr viel mächtigeres Tool an Bord, das ich Euch nun an Hand von Beispielen erklären will.

## templateName

Mithilfe von templateRootPaths wird ein Verzeichnis angegeben, in dem sich alle verfügbaren Templates befinden. Damit FLUIDTEMPLATE weiß, welches dieser Templates zu verwenden ist, müsst ihr mit der Eigenschaft templateName den Dateinamen ohne Dateiextension angeben. Im Template-Verzeichnis gibt es z.B. diese Dateien:

- Main.html
- StartPage.html
- TwoCols.html
- ThreeCols.html

Mit diesem TypoScript könnt ihr nun angeben, dass beim Rendern, das Template TwoCols.html verwendet werden soll:

```typo3_typoscript
page = PAGE
page.10 = FLUIDTEMPLATE
page.10 {
  # Hier keine Dateiendung angeben!
  templateName = TwoCols
  templateRootPaths {
    10 = EXT:my_sitepackage/Resources/Private/Templates
  }
}
```

## template

Bitte mit Vorsicht verwenden

Mit template könnt ihr das HTML-Template mithilfe von TypoScript zusammen bauen. Natürlich macht hier die Zusammenarbeit mit dem ContentObject FILE am meisten Sinn. Jedoch ist das FILE Objekt seit TYPO3 9 deprecated und wird mit TYPO3 10 entfernt. Durch Entfernung dieser Eigenschaft frage ich mich, wie viel Sinn es dann noch macht die template-Eigenschaft überhaupt noch zu verwenden. Denn ein Template komplett mit TypoScript zu erstellen macht IMHO sehr wenig Sinn.

```typo3_typoscript
page = PAGE
page.10 = FLUIDTEMPLATE
page.10 {
  template = TEXT
  template.value = <h2>{title}</h2>
  variables {
    title = TEXT
    title.data = PAGE:title
  }
}
```

## file

Alternativ zu templateRootPaths, wo ihr zwischen mehreren Templates wählen könnt, bietet Euch file die Möglichkeit an exakt EIN Template inkl. Pfad anzugeben.

```typo3_typoscript
page = PAGE
page.10 = FLUIDTEMPLATE
page.10 {
  file = EXT:my_sitepackage/Resources/Private/Templates/MyIndividualLayout.html
}
```

## templateRootPaths

Mit templateRootPaths habt ihr die Möglichkeit mehrere Verzeichnis-Pfade anzugeben, aus denen das Template, definiert über templateName, geladen werden soll. Jeder Eintrag unterliegt einer Priorität. Je höher die Zahl, desto höher die Priorität.

```typo3_typoscript
page = PAGE
page.10 = FLUIDTEMPLATE
page.10 {
  # Hier keine Dateiendung angeben!
  templateName = TwoCols
  templateRootPaths {
    0 = news/Resources/Private/Templates
    10 = EXT:my_sitepackage/Resources/Private/Templates
  }
}
```

Im Beispiel oben haben die Templates aus der news-Extension die niedrigste Priorität. Der 10er Eintrag hat eine höhere Priorität und überschreibt damit die Templates aus der news. Damit bietet sich folgende Möglichkeit: Ihr braucht nicht mehr ALLE Templates aus der news in Eure SitePackage-Extension zu kopieren, sondern nur noch die Datei, die ihr auch tatsächlich überschreiben wollt. Wenn sich also eine Detail.html in Eurer Extension befindet, so wird diese auch verwendet. Wenn ihr keine List.html habt, dann fällt Fluid auf die news-Extension zurück und läd die List.html von dort. Dieses Prinzip könnt ihr mit mehreren solcher Einträge beliebig oft erweitern: 20 = Pfad, 30 = Pfad, 40 = Pfad, ...

## layoutRootPaths

Mit layoutRootPaths habt ihr die Möglichkeit mehrere Verzeichnis-Pfade anzugeben, aus denen die Layouts, definiert über f:layout innerhalb einer Template-Datei, geladen werden soll. Jeder Eintrag unterliegt einer Priorität. Je höher die Zahl, desto höher die Priorität.

```typo3_typoscript
page = PAGE
page.10 = FLUIDTEMPLATE
page.10 {
  # Hier keine Dateiendung angeben!
  templateName = TwoCols
  templateRootPaths {
    0 = news/Resources/Private/Templates
    10 = EXT:my_sitepackage/Resources/Private/Templates
  }
  layoutRootPaths {
    0 = news/Resources/Private/Layouts
    10 = EXT:my_sitepackage/Resources/Private/Layouts
  }
}
```

Im Beispiel oben haben die Layouts aus der news-Extension die niedrigste Priorität. Der 10er Eintrag hat eine höhere Priorität und überschreibt damit die Layouts aus der news. Damit bietet sich folgende Möglichkeit: Ihr braucht nicht mehr ALLE Layouts aus der news in Eure SitePackage-Extension zu kopieren, sondern nur noch die Datei, die ihr auch tatsächlich überschreiben wollt. Wenn sich also eine Detail.html in Eurer Extension befindet, so wird diese auch verwendet. Wenn ihr keine List.html habt, dann fällt Fluid auf die news-Extension zurück und läd die List.html von dort. Dieses Prinzip könnt ihr mit mehreren solcher Einträge beliebig oft erweitern: 20 = Pfad, 30 = Pfad, 40 = Pfad, ...

## partialRootPaths

Mit partialRootPaths habt ihr die Möglichkeit mehrere Verzeichnis-Pfade anzugeben, aus denen die Partials, definiert über f:render, geladen werden soll. Jeder Eintrag unterliegt einer Priorität. Je höher die Zahl, desto höher die Priorität.

```typo3_typoscript
page = PAGE
page.10 = FLUIDTEMPLATE
page.10 {
  # Hier keine Dateiendung angeben!
  templateName = TwoCols
  templateRootPaths {
    0 = news/Resources/Private/Templates
    10 = EXT:my_sitepackage/Resources/Private/Templates
  }
  partialRootPaths {
    0 = news/Resources/Private/Partials
    10 = EXT:my_sitepackage/Resources/Private/Partials
  }
}
```

Im Beispiel oben haben die Partials aus der news-Extension die niedrigste Priorität. Der 10er Eintrag hat eine höhere Priorität und überschreibt damit die Partials aus der news. Damit bietet sich folgende Möglichkeit: Ihr braucht nicht mehr ALLE Partials aus der news in Eure SitePackage-Extension zu kopieren, sondern nur noch die Datei, die ihr auch tatsächlich überschreiben wollt. Wenn sich also eine Detail.html in Eurer Extension befindet, so wird diese auch verwendet. Wenn ihr keine List.html habt, dann fällt Fluid auf die news-Extension zurück und läd die List.html von dort. Dieses Prinzip könnt ihr mit mehreren solcher Einträge beliebig oft erweitern: 20 = Pfad, 30 = Pfad, 40 = Pfad, ...

## format

Hier könnt ihr angeben welche Dateiendung für die Templates verwendet werden soll. Wenn ihr Euch die Eigenschaft templateName anschaut, werdet ihr merken, dass keine Dateiendung anzugeben ist. Und genau das macht ihr halt mit format. Standard ist html, aber auch xml oder json wären mögliche Dateiendungen.

## extbase.pluginName

Wenn ihr alle extbase.* Eigenschaften angebt, könnt ihr mit FLUIDTEMPLATE auch Links und Formulare auf Extbase-Plugins weiterleiten.

## extbase.controllerExtensionName

Wenn ihr alle extbase.* Eigenschaften angebt, könnt ihr mit FLUIDTEMPLATE auch Links und Formulare auf Extbase-Plugins weiterleiten.

Mit dieser Eigenschaft könnt ihr dem Fluid-Prozess vorgaukeln es würde aus der hier angegebenen Extension heraus aufgerufen werden. Das hat gerade in Bezug auf den f:translate ViewHelper Einfluss, denn mit dieser Angabe hier müsst ihr nicht mehr den völlständigen Pfad als Key angeben, sondern es reicht, wie bei Extbase gewohnt, nur den reinen Key, ohne Pfad, anzugeben, was die Übersichtlichkeit in den Templates erheblich verbessert.

## extbase.controllerName

Wenn ihr alle extbase.* Eigenschaften angebt, könnt ihr mit FLUIDTEMPLATE auch Links und Formulare auf Extbase-Plugins weiterleiten.

## extbase.controllerActionName

Wenn ihr alle extbase.* Eigenschaften angebt, könnt ihr mit FLUIDTEMPLATE auch Links und Formulare auf Extbase-Plugins weiterleiten.

## variables

Mit variables könnt ihr Daten aus ContentObjects an das Fluidtemplate übertragen. Gerade in Bezug auf dynimische Werte ist diese Eigenschaft der settings-Eigenschaft vorzuziehen.

```typo3_typoscript
page = PAGE
page.10 = FLUIDTEMPLATE
page.10 {
  template = TEXT
  template.value = <h2>{title}</h2>
  variables {
    title = TEXT
    title.data = PAGE:title
  }
}
```

## settings

Mit settings könnt ihr statische Daten an das Fluidtemplate übertragen. Gerade in Bezug auf dynimische Werte solltet ihr lieber auf variables ausweichen.

```typo3_typoscript
page = PAGE
page.10 = FLUIDTEMPLATE
page.10 {
  template = TEXT
  template.value = <h2>{title}</h2>
  settings {
    title = Header
    pidOfDetailPage = 1534
  }
}
```

## dataProcessing

Dokumentation folgt. Im Folgenden mal ein Beispiel, was möglich ist. Ich hole hier alle Unterseiten der Seite mit der UID 1 und lade PRO gefundener Seite alle Dateien, die über die Spalte media verknüpft sind. Dadurch dass der erste DatabaseQueryProcessor jedes einzelne Ergebnis an die data-Eigenschaft des ContentObjectRenderer übergibt, stehen Euch nun im zweiten DatabaseQueryProcessor diese Daten über die TS-Eigenschaft fields und data zur Verfügung. Auf diese Weise lassen sich die DatabaseQueryProcessors schier unendlich in die Tiefe verschachteln und ermöglichen auch komplexe Umsetzungen.

```typo3_typoscript
page = PAGE
page.10 = FLUIDTEMPLATE
page.10.file = fileadmin/template.html
page.10 {
  dataProcessing.10 = TYPO3\CMS\Frontend\DataProcessing\DatabaseQueryProcessor
  dataProcessing.10 {
    table = pages
    pidInList = 1
    as = subPages
    orderBy = pages.sorting
    dataProcessing.10 = TYPO3\CMS\Frontend\DataProcessing\FilesProcessor
    dataProcessing.10 {
      references.fieldName = media
    }
    dataProcessing.20 = TYPO3\CMS\Frontend\DataProcessing\DatabaseQueryProcessor
    dataProcessing.20 {
      table = tt_content
      as = inhalte
      pidInList.field = uid
    }
  }
  dataProcessing.20 = TYPO3\CMS\Frontend\DataProcessing\MenuProcessor
  dataProcessing.20 {
    #special = directory
    #special.value =
    levels = 2
    as = mainMenu
    dataProcessing.10 = TYPO3\CMS\Frontend\DataProcessing\FilesProcessor
    dataProcessing.10 {
      references.fieldName = media
    }
  }
}
```

## stdWrap

Diese Eigenschaft wird nach dem Rendering des Fluidtemplates gestartet. Damit habt ihr die Möglichkeit noch im Nachgang auf das Rendering Ergebnis einzugreifen.
