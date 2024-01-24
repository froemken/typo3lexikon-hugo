+++
title = "Formulare"
date = 2024-01-09T20:25:39+01:00
+++

Evtl. fragt ihr euch warum für jedes Formelement ein eigener ViewHelper programmiert wurde? Na ich denke, weil man sich damit erstens Zeit spart (Dank der Property-Eigenschaft) und zweitens Fehler reduzieren kann (Dank der Property-Eigenschaft). Spielen wir mal ein paar Beispiele durch:

## Beispiel: Ohne Fluid

Wenn wir ohne TYPO3 bzw. ohne CMS ein einfaches Formular programmieren würden, dann könnte ein Eingabefeld in etwa so aussehen:

```html
<input type="text" name="autoMarke" />
```

Innerhalb der Programmierung würden wir dann mit `$_POST` oder ähnlichem wieder auf diesen Wert nach dem Absenden zugreifen. Im Falle von TYPO3 wird mit der TYPO3-API auf die Formulardaten zugegriffen:

```php
$marke = \TYPO3\CMS\Core\Utility\GeneralUtility::_GP('autoMarke');
```

Aber egal wie, um die Sicherheit habt ihr euch selbst zu kümmern. Ihr müsst selbst prüfen, ob es sich um einen Text, eine Zahl oder booleschen Wert handelt.

## Beispiel: Formulare mit Actions

Wer jetzt schonmal Extensions auf `piBase` Basis programmiert hat erinnert sich evtl. noch an `$this->piVars`. In diesem Array waren alle GET/POST-Variablen enthalten, die nur für diese eine Extension gültig waren. Bei Extbase geht dieser Schritt noch ein bisschen weiter. Hier wird nun sogar zwischen den jeweiligen Plugins unterschieden. Folgendes Formularfeld als Beispiel:

```html
<input type="text" name="tx_sffluid_sffluid[autoMarke]" />
```

Das `name` Attribut gibt an für welche Extension (das erste `sffluid`) bzw. welches Plugin (das zweite `sffluid`) der enthaltene Wert verfügbar gemacht werden soll. Wenn wir dieses Formular nun abschicken, können wir mit allen Actions der Extension `sffluid` in dem Plugin `sffluid` auf dieses Formularfeld zugreifen:

```php
public function createAction(string $autoMarke) {
    \TYPO3\CMS\Core\Utility\DebugUtility::debug($autoMarke, 'marke');
    die('Ende');
}
```

Kein `$_POST`, kein `$_GET` und keine Verwendung irgendwelcher TYPO3 API. Das macht alles Extbase für uns, solange wir uns an die exakte Namensgebung halten. Ein Rechtschreibfehler im `name` Attribut des Formularfeldes resultiert hier in diesem Beispiel in eine leere Ausgabe.

## Beispiel: Formulare mit Extbase (Array)

Auch wenn das Actionbeispiel funktioniert, so setzt Extbase noch einen obendrauf. Denn was wäre, wenn wir ein großes Formular mit 30, 40 oder sogar 50 Formularfeldern hätten? Dann müssten wir auch die Action entsprechend erweitern und 50 Parameter übergeben. Das wäre eine ewig lange und vor allem unübersichtliche Action.

Bei Extbase gehört immer alles irgendwie zusammen und so wollen wir natürlich nicht nur unsere `autoMarke` versenden, sondern mehrere Daten, die ein Auto betreffen. Also erstellt man sich ein Model, das z.B. `Auto` heißt und vergibt diesem unter anderem die Eigenschaft `marke` und schon könnte unser Formular folgendermaßen aussehen:

```html
<input type="text" name="tx_sffluid_sffluid[auto][marke]">
<input type="text" name="tx_sffluid_sffluid[auto][farbe]">
<input type="text" name="tx_sffluid_sffluid[auto][ps]">
```

Und der Aufruf innerhalb der Action wird auch eine ganze Ecke kürzer:

```php
public function createAction(array $auto) {
    \TYPO3\CMS\Core\Utility\DebugUtility::debug($auto, 'auto');
    die('Ende');
}
```

## Beispiel: Formulare mit Extbase (Objekt)

Wenn wir mit Objekten arbeiten, können wir uns einiges an Schreibarbeit sparen und die Sache mit den `name` Attributen ist eine ganze Ecke weniger fehleranfällig. In unserem `f:form` ViewHelper geben wir mithilfe des `name` Attributes an, wie unser Objekt heißen soll. Bei diesem Objektnamen handelt es sich später um den Variablennamen (Parameter) der an die aufgerufene Action übergeben wird. Also bitte keine Leerzeichen oder sowas.

Wenn wir unserem Template ein Objekt übergeben haben, dann können wir dieses Objekt mit dem `f:form` ViewHelper verknüpfen. In diesem besonderen Fall stehen uns nun die `property` Attribute zur Verfügung, die uns die Arbeit abnehmen das `name` Attribut richtig zu setzen. Das Ergebnis ist das Gleiche wie aus dem oberen Array-Beispiel.

```html
<f:form action="create" name="auto" object="{auto}">
    <f:form.textfield property="marke"/><br>
    <f:form.textfield property="farbe"/><br>
    <f:form.textfield property="ps"/><br>
    <f:form.submit value="Erstellen"/>
</f:form>
```

Nach Absenden des Formulars erhalten wir auch hier erstmal wieder ein Array, wenn uns Extbase nicht zuvorkommen würde:

```php
public function createAction(\SFroemken\Sffluid\Domain\Model\Auto $auto) {
    \TYPO3\CMS\Core\Utility\DebugUtility::debug($auto->getMarke(), 'Marke');
    \TYPO3\CMS\Core\Utility\DebugUtility::debug($auto->getFarbe(), 'Farbe');
    die('Ende');
}
```

Extbase liest vor dem Aufruf einer jeden Action die Parameter der aufzurufenden Action aus und kann somit den Typen der Variable ermitteln. Im vorherigen Beispiel hatte ich `array` vor den Parameter geschrieben und konnte innerhalb der Action wie mit einem Array auf die Inhalte zugreifen. Hier in diesem Beispiel steht nun aber der Typ \SFroemken\Sffluid\Domain\Model\Auto vor dem Parameter. Extbase prüft, ob es eine Klasse mit diesem Typennamen gibt und versucht alle Werte des Arrays in das Model zu übertragen. Dabei muss der Schlüsselname eines Arrays mit dem Namen der Eigenschaft aus dem Model übereinstimmen. Wenn nicht, dann wird der Wert nicht mit in das Model aufgenommen.
