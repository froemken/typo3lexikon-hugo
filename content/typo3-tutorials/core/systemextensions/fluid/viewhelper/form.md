+++
title = "Form"
date = 2024-01-09T20:27:29+01:00
aliases = ["form.html"]
+++

## f:form.checkbox

Dieser ViewHelper erzeugt eine Checkbox. 

| Parameter | Erklärung |
|-----------|-----------|
| checked | Wenn aktiviert, dann gilt diese Checkbox als markiert. |
| property | Wenn angegeben, dann bezieht sich diese Checkbox die benötigten Daten aus einer Objekteingenschaft |

Derzeit sind Checkboxen innerhalb von Fluid noch eine echte Katastrophe. Es hat mich viel Zeit gekostet eine Checkbox überhaupt ans Laufen zu bekommen, da sie ein Model mit vorausgefüllten Eigenschaften erfordern:

{{% notice style="red" title="Error" icon="skull-crossbones" %}}
No value found for key "\Typo3Fluid\Fluid\ViewHelpers\FormViewHelper->formObject"
{{% /notice %}}

Um diesen Fehler zu beheben, darf das Objekt nicht `null` sein. In der entsprechenden Action muss ein leeres Objekt erstellt werden. Könnte dann z.B. so aussehen (Auszug aus dem extension_builder):

```php
public function newAction(\SFroemken\Sffluid\Domain\Model\Auto $newAuto = null) {
    if ($newAuto == null) { // workaround for fluid bug ##5636
        $newAuto = \TYPO3\CMS\Core\Utility\GeneralUtility::makeInstance(\SFroemken\Sffluid\Domain\Model\Auto::class);
    }
    $this->view->assign('newAuto', $newAuto);
}
```

Erst nach dieser Änderung erscheint überhaupt mal eine Checkbox.

## Beispiel ohne property

Das Problem bei dieser Variante: Ihr müsst Euch selbst darum kümmern zu prüfen, ob die Checkbox markiert ist oder nicht. Ihr müsst die Werte schon innerhalb ures Controllers gesetzt haben und mithilfe des Checked-Attributes zuweisen. Erlaubt ist nur `true` oder `false` bzw. `1` oder `0`. Ihr müsst hier auch die leeren eckigen Klammern setzen, damit die markierten Werte später als Array (Mehrfachauswahl) übergeben werden können.

```html
<f:form.checkbox name="myExtName[pizza][]" checked="{data.salami}" value="Salami" />
<f:form.checkbox name="myExtName[pizza][]" checked="{data.hawaii}" value="Hawaii" />
<f:form.checkbox name="myExtName[pizza][]" checked="{data.tonno}" value="Tonno" />
```

## Beispiel mit property

Damit ihr dem Problem von oben entgehen könnt, bindet die Checkboxen an eine Objekteigenschaft, die ihr im `f:form` ViewHelper hinterlegt habt:

```html
<f:form.checkbox property="pizza" value="Salami" />
<f:form.checkbox property="pizza" value="Hawaii" />
<f:form.checkbox property="pizza" value="Tonno" />
```

Schaut schon eine ganze Ecke kürzer aus.

## Beispiel Multiselect

Der `EXT:extension_builder` kann bislang nur eine Checkbox erstellen. Nämlich dann, wenn der Typ `Boolean` gewählt wurde. Wollt ihr aber wie in den oberen Beispielen mehrere Checkboxen gleichzeitig setzen, dann müsst ihr euch vom Typ `Boolean` verabschieden. Ich will euch heute eine Möglichkeit zeigen, wie ihr über 10 Checkboxen abspeichern könnt.

Innerhalb des `EXT:extension_builder` wählt ihr nun den Typ `Text` aus. Damit wird in der Datenbank eine Spalte erstellt, die etwas über 65.000 Zeichen abspeichern kann. Vergesst nicht über Database Analyser den neuen Typ in die Datenbank zu schreiben.

Das Formular bleibt ähnlich den oberen Beispielen:

```html
<f:form.checkbox property="farbe" value="gelb" />&nbsp:gelb<br>
<f:form.checkbox property="farbe" value="braun" />&nbsp:braun<br>
<f:form.checkbox property="farbe" value="blau" />&nbsp:blau<br>
```

Da ihr ein Array nicht in der Datenbank abspeichern könnt, müsst ihr das Array, das von den Checkboxen kommt, in einen String konvertieren. Das könnt ihr zum Beispiel mit `PHP:serialize()` und `PHP:unserialize()` realisieren. Bearbeitet dazu in eurem Model den Getter und Setter für die Checkboxen:

```php
protected string $farbe = [];

public function getFarbe(): array
{
    return unserialize($this->farbe);
}

public function setFarbe(array $farbe): void
{
    $this->farbe = serialize($farbe);
}
```

Schaut euch auch den Quellcode im HTML an:

```html
<input type="checkbox" name="tx_sffluid_sffluid[newAuto][farbe][]" value="gelb"/><br>
<input type="checkbox" name="tx_sffluid_sffluid[newAuto][farbe][]" value="braun"/><br>
<input type="checkbox" name="tx_sffluid_sffluid[newAuto][farbe][]" value="blau"/><br>
```

Anhand der leeren eckigen Klammern kann man schön erkennen, dass nun die Mehrfachauswahl möglich ist. Auch das Bearbeiten vorhandener Datensätze klappt mit dieser Methode problemlos.

Nachteil: Im Backend lassen sich die Checkboxen nicht mehr setzen. Hier bedarf es einer weiteren Anpassung im TCA.

## f:form.errors

Veraltet. Bitte verwendet den `f:form.validationResults` ViewHelper.

## f:form.hidden

Mit diesem ViewHelper erstellst Du ein verstecktes Feld. Das ist schonmal nützlich, um Datensatz-UIDs abzulegen, die der Webseitenbesucher nicht zu sehen braucht, aber für dich wichtig sind, wenn es darum geht, die eingegebenen Daten einem Datensatz zuzuordnen, um ihn z.B. zu speichern.

| Parameter | Erklärung |
|-----------|-----------|
| property | Wenn angegeben, dann bezieht sich dieses Eingabefeld die benötigten Daten aus einer Objekteingenschaft |

### Beispiel

```html
<f:form.hidden name="myExtName[ttAddressUid]" value="15"/>
```

oder

```html
<f:form.hidden property="ttAddressUid" value="15"/>
```

## f:form.password

Mit diesem ViewHelper erstellst du ein Textfeld, dessen Inhalt nicht lesbar ist. Alle Zeichen werden sofort in Sternchen umgewandelt.

| Parameter | Erklärung |
|-----------|-----------|
| property | Wenn angegeben, dann bezieht sich dieses Eingabefeld die benötigten Daten aus einer Objekteingenschaft |

### Beispiel

```html
<f:form.password name="myExtName[password]"/>
```

oder

````html
<f:form.password property="password"/>
````

## f:form.radio

Mit diesem ViewHelper erstellst du einen Radiobutton. Normalerweise tauchen diese immer in Gruppen auf und bieten dem Besucher an sich für eine Möglichkeit zu entscheiden. Eine Mehrfachauswahl wie bei den Checkboxen ist hier nicht möglich.

| Parameter | Erklärung |
|-----------|-----------|
| property | Wenn angegeben, dann bezieht sich dieses Eingabefeld die benötigten Daten aus einer Objekteingenschaft |

### Beispiel

```html
<f:form.radio name="myExtName[age]" value="1-10"/>
<f:form.radio name="myExtName[age]" value="11-40"/>
<f:form.radio name="myExtName[age]" value="41-99"/>
```

oder

```html
<f:form.radio property="age" value="1-10"/>
<f:form.radio property="age" value="11-40"/>
<f:form.radio property="age" value="41-99"/>
```

## f:form.select

Mit diesem ViewHelper erstellst Du eine Selectbox

| Parameter | Erklärung |
|-----------|-----------|
| property | Wenn angegeben, dann bezieht sich dieses Eingabefeld die benötigten Daten aus einer Objekteingenschaft |
| options | Welche Optionen sollen in der Auswahlliste erscheinen? |
| optionValueField | Wenn `options` Objekte enthält, dann könnt ihr hier angeben welche Eigenschaft als zu übergebenden Wert verwendet werden soll |
| optionLabelField | Wenn `options` Objekte enthält, dann könnt ihr hier angeben welche Eigenschaft als anzuzeigender Titel verwendet werden soll |
| sortByOptionLabel | Wenn aktiviert, dann werden die Labels alphabetisch sortiert. |
| selectAllByDefault | Damit alle Optionen direkt vorausgewählt sind, muss das Attribut `multiple` und die `size` größer `1` gesetzt sein. |

### Beispiel

```html
<f:form.select name="myExtName[country]" options="{data.countries}"/>
```

oder

```html
<f:form.select property="country" options="{data.countries}"/>
```

## f:form.submit

Dieser ViewHelper besitzt keine eigenen Parameter. Er bindet eine Schaltfläche an, die nach dem Anklicken das Formular absendet und somit die eingegebenen Formulardaten an den Server übermittelt

### Beispiel

```html
<f:form.submit value="Absenden"/>
```

## f:form.textarea

Mit diesem ViewHelper erstellst du ein Memofeld. Memofelder könnt ihr für die Eingabe mehrzeiliger Texte verwenden. Gut geeignet z.B. für das Nachrichtenfeld im Kontaktformular.

| Parameter | Erklärung |
|-----------|-----------|
| property | Wenn angegeben, dann bezieht sich diese Textarea die benötigten Daten aus einer Objekteingenschaft |

### Beispiel

```html
<f:form.textarea name="myExtName[nachricht]"/>
```

oder

```html
<f:form.textarea property="nachricht"/>
```

## f:form.textbox

Veraltet. Bitte verwendet den `f:form.textfield` ViewHelper.

## f:form.textfield

Mit diesem ViewHelper erstellst Du ein Textfeld

| Parameter | Erklärung |
|-----------|-----------|
| property | Wenn angegeben, dann bezieht sich dieses Textfeld die benötigten Daten aus einer Objekteigenschaft |
| required | Wenn aktiviert, dann handelt es sich um ein Pflichtfeld |
| type | Wert für das `type` Attribut. Standard: `text`. Alternativ `email` oder `url` |
| placeholder | Ein beliebiger Text, der verschwindet, sobald in dieses Feld reingeklickt wird. |

### Beispiel

```html
<f:form.textfield name="myExtName[strasse]"/>
```

oder

```html
<f:form.textfield property="strasse"/>
```

## f:form.upload

Mit diesem ViewHelper erstellst Du ein Uploadfeld, um Dateien an den Server zu senden.

| Parameter | Erklärung |
|-----------|-----------|
| property | Wenn angegeben, dann bezieht sich dieses Uploadfeld die benötigten Daten aus einer Objekteingenschaft |

### Beispiel

```html
<f:form.upload name="myExtName[image]"/>
```

oder

```html
<f:form.upload property="image"/>
```

## f:form.validationResults

Dokumentation folgt.
