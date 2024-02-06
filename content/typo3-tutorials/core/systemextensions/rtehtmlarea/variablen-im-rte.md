+++
title = "Variablen im RTE"
date = 2024-01-09T20:33:00+01:00
aliases = ["variablen-im-rte.html"]
+++

Ich bin das erste Mal über Marker im RTE gestolpert, das war Ende 2009 in einem Türchen des Advendskalender von jweiland.net. Die Idee ist irgendwie cool. Einfach ein

```typo3_typoscript
constants.FIRMA = Das ist mein Firmenname
```

in das TS-Setup und schon kann man mit dem Marker ###FIRMA### den Firmenname in den RTE einbinden. Und falls sich dieser (warum auch immer) mal ändern sollte, braucht man den Firmenname nur im TS-Setup ändern und automatisch ändert sich der Firmenname auch im Frontend.

## Dynamische Werte per TypoScript erlauben

Wir werden bei meiner Lösung des Problems auf alle Daten zugreifen können, die Ihr auch mit der stdWrap-Eigenschaft ".data" erreichen könnt. Wer sich auskennt wird wissen, dass man damit auch die Passwörter der Webseitenbenutzer auslesen kann. Deshalb schlage ich vor die folgenden Einstellungen nicht im Seitentemplate , sondern pro Seite mit Hilfe eines +extTemplates (Erweiterungs-Template) vorzunehmen:

TS-Setup:

```typo3_typoscript
lib.parseFunc_RTE.nonTypoTagStdWrap.encapsLines.innerStdWrap_all.insertData = 1
```

Mit nur dieser Zeile habt Ihr es nun geschafft auf alle TYPO3-Daten zuzugreifen. Hier mal ein Beispieltext, den Ihr nun in den RTE kopieren könnt:

```html
Hallo {TSFE:fe_user|user|name}. Wie geht's? Dein Benutzername ist {TSFE:fe_user|user|username}. Die aktuell aufgerufene Seite hat die ID {PAGE:uid}
```

## Dynamische Werte per TypoScript setzen

Schaut Euch mal mit Hilfe des Template-Analysers das statische Template von css_styled_content an und sucht dort nach der Zeile "tt_content.text":

```typo3_typoscript
# *****************
# CType: text
# *****************
tt_content.text = COA
tt_content.text {
  10 = < lib.stdheader
  <--- Hier haben wir Platz für eigene Elemente --->
  20 = TEXT
  20 {
    field = bodytext
    required = 1
    parseFunc = < lib.parseFunc_RTE
    editIcons = tt_content:bodytext, rte_enabled
    editIcons.beforeLastTag = 1
    editIcons.iconTitle.data = LLL:EXT:css_styled_content/pi1/locallang.xml:eIcon.bodytext
    prefixComment = 2 | Text:
  }
}
```

Im oberen Codefragment habe ich einen Bereich markiert, den wir für eigene Contentelemente verwenden können (11 - 19).

Mit dieser Information können wir nun weitere Zeilen in unser TS-Setup einfügen:

lib.parseFunc_RTE.nonTypoTagStdWrap.encapsLines.innerStdWrap_all.insertData = 1

```typo3_typoscript
tt_content.text.11 = LOAD_REGISTER
tt_content.text.11.image.cObject = IMAGE
tt_content.text.11.image.cObject.file = fileadmin/templates/bilder/tolles-bild.png
tt_content.text.11.image.cObject.file.width = 230
tt_content.text.11.image.cObject.border = 1
tt_content.text.12 = LOAD_REGISTER
tt_content.text.12.meinText = Trallala Blabla

tt_content.text.21 = RESTORE_REGISTER
tt_content.text.22 = RESTORE_REGISTER
```

Im RTE könnt Ihr nun mit Hilfe diesen Textes auf diese beiden neuen CEs zugreifen:

In diesem Satz {REGISTER:image} befindet sich ein Bild und hier ist die Variable meinText: {REGISTER:meinText}
