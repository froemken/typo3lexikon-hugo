+++
title = "PropertyMapper"
date = 2024-01-09T20:22:21+01:00
aliases = ["propertymapper.html"]
+++

## Was ist die Aufgabe eines Property Mappers?

Ein Property Mapper hat die Aufgabe, die Daten, die von Formularen an den Webserver gesendet werden in die korrekten Variablentypen zu konvertieren. Alle Daten eines Formulars werden entweder mithilfe von POST oder GET (Url) an den Server übertragen, aber diese Transportmethoden können nur mit Arrays und Texten umgehen. Wenn ihr also in Eurem Formular ein Feld für das Alter einer Person habt und absendet, dann ist für den Server dieses Alter grundsätzlich erstmal ein Text und keine Zahl. Vielleicht habt ihr aber auch ein Formular auf Eurer Webseite, auf dem ihr die Beläge Eurer Pizza zusammenstellen könnt (Auswahl per Checkbox). In diesem Fall werden die Daten als Array an der Server übertragen. Solange diese Transportmethoden nicht zwischen Text und Zahl oder auch Array und Objekten unterscheiden können, seid ihr die Einzigen die wissen von welchem Typ diese Daten sind. Ein Property Mapper kann Euch bei dieser Aufgabe helfen.

## Der bisherige Property Mapper

Der bisherige Property Mapper hilft Euch allerhöchstens dabei ein Array in ein Objekt zu konvertieren. Auch die Konvertierung eines Timestamps in ein DateTime-Object ist enthalten, aber alle anderen Werte werden so wie sie sind einfach an die Settermethoden des Domainmodels weitergereicht und wenn ihr Euch hier nicht selbst um die Konvertierung kümmert, dann landen weiterhin Texte in Eigenschaften, die eigentlich Zahlenwerte sein sollten. Besten Beispiel: Das Alter einer Person.

## Was unterscheidet den neuen Property Mapper vom Alten?

Der neue Property Mapper ist ein Backport des Flow Frameworks und bietet Euch eine sehr detaillierte Möglichkeit genau entscheiden zu können, welche Variablen der Transportmethoden (POST, GET) in einen bestimmten Datentyp konvertiert werden sollen. Ihr könnt also genau angeben, dass die Variable "Alter", die per Default als Text übertragen wird in einen Integerwert konvertiert werden soll. Ihr könnt auch angeben, dass die Variable "Alter" auf die Eigenschaft "Age" Eures Domainmodels gemapped werden soll. Und wenn ihr mal gar keine Konfiguration angebt, dann wird der Datentyp anhand der Eigenschaft im Domainmodel abgefragt und verwendet. Über einen Hook könnt ihr sogar eigene TypeConverter registrieren und verwenden. Ihr seht: Ihr habt alles selbst in der Hand, die Frage ist nur, wie konfiguriert man den Property Mapper. Darüber handelt die nachfolgende Dokumentation.

## Aktivieren des neuen Property Mappers

Den neuen Property Mapper gibt es seit TYPO3 4.7 und kann folgendermaßen aktiviert werden:

```typo3_typoscript
plugin.tx_[euerExtensionName].features.rewrittenPropertyMapper = 1
```

Ab TYPO3 6.1 ist der Property Mapper per Default aktiviert. Wer mag, kann die TypoScript-Eigenschaft von oben wieder auf 0 setzen, um weiterhin die alte Variante zu aktivieren. Ab TYPO3 7.0 ist dann aber wirklich Schluss. Hier wurde der alte Property Mapper komplett entfernt und ihr müsst Euch mit dem neuen Property Mapper begnügen.

## Analyse des Request

Ich habe hier mal ein kleines Formular mit Fluid zusammen gebaut:

```html
<f:form action="create" name="person" object="{person}">
    <f:translate key="tx_sfpm2_domain_model_person.first_name" />
    <f:form.textfield property="firstName" />
  
    <f:translate key="tx_sfpm2_domain_model_person.last_name" />
    <f:form.textfield property="lastName" />
  
    <f:translate key="tx_sfpm2_domain_model_person.age" />
    <f:form.textfield property="age" />
     
    <f:form.submit value="Speichern" />
</f:form>
```

Wir als Mensch wissen sofort, dass firstName und lastName ein Text sind. Auch beim Alter sind wir uns einig, dass es sich um ein Zahlenfeld handelt. Der Request, der bei Extbase jedoch ankommt, schaut folgendermaßen aus:

```
array(3 items)
action => 'create' (6 chars)
controller => 'PropertyMapper' (14 chars)
person => array(3 items)
firstName => 'Stefan' (6 chars)
lastName => 'Frömken' (8 chars)
age => '35' (2 chars)
```

Hier kann man sehr schön sehen, dass die Transfermethoden nur Text und Arrays interpretieren können.

## Konvertierung anhand Domainmodel

Bisher haben wir den Property Mapper noch überhaupt nicht konfiguriert. Somit greift er per Default auf das Domainmodel zu und extrahiert sich mithilfe der ReflectionClass von PHP die Datentypen von den enthaltenen Eigenschaften. Hier mal ein Beispiel, wie die Eigenschaften bei mir aussehen:

```php
/**
 * FirstName
 *
 * @var string
 */
protected $firstName;

/**
 * LastName
 *
 * @var string
 */
protected $lastName;

/**
 * Age
 *
 * @var integer
 */
protected $age;
```

und hier die Debugausgabe unseres Models:

```
StefanFroemken\Sfpm2\Domain\Model\Personprototypetransient entity
firstName => 'Stefan' (6 chars)
lastName => 'Frömken' (8 chars)
age => 35 (integer)
uid => NULL
_localizedUid => NULL
_languageUid => NULL
_versionedUid => NULL
pid => NULL
```

Nur durch die Angabe der Datentypen in unseren Domainmodellen, weiß der Property Mapper in welches Format die ankommenden Daten zu konvertieren sind. Sehr schön zu sehen an dem Beispiel mit der Eigenschaft "age", die hier bereits nach integer konvertiert wurde.

## Überschreiben der Datentypen

In den bisherigen Beispielen kümmert sich der Property Mapper automatisch um die richtige Konvertierung der ankommenden Daten. Hin und wieder kann es vorkommen, dass ihr diese Datentypen überschreiben wollt. Im Folgenden wollen wir die Eigenschaft "age" in einen Text konvertieren. Da die automatische Konvertierung noch VOR dem Aufruf unserer Action geschieht, müssen wir uns mit einer initializeAction begnügen. Diese Actions werden noch VOR der Konvertierung durch des Property Mapper ausgeführt. Der ideale Platz, um dem Property Mapper besondere Konvertierungsangaben mitzuliefern:

```php
/**
 * initialize create action
 *
 * @return void
 */
public function initializeCreateAction() 
{
    if ($this->arguments->hasArgument('person')) {
        $this->arguments->getArgument('person')->getPropertyMappingConfiguration()->setTargetTypeForSubProperty('age', 'string');
    }
}
```

Ich greife hier auf alle Personenfelder zu und lade mir die Property Mapper Konfiguration. Hier nun gibt es eine Methode, mit der ich den Untereigenschaften von "Person" einen anderen Datentypen zuweisen kann. Für das Alter setze ich den Datentyp auf "string". In unserer Debugausgabe erscheint nun:

```
StefanFroemken\Sfpm2\Domain\Model\Personprototypetransient entity
firstName => 'Stefan' (6 chars)
lastName => 'Frömken' (8 chars)
age => '35' (2 chars)
uid => NULL
_localizedUid => NULL
_languageUid => NULL
_versionedUid => NULL
pid => NULL
```

## Überspringen von Eigenschaften

Ich bin mir gerade unsicher, aber in Deutschland dürfen die Kreditkartennummern und/oder der Überprüfungscode nicht abgespeichert werden. Dennoch gehört die Kreditkarte einer Person. Wir müssen also ein Feld in unser Formular aufnehmen, dass die Kreditkartennummer aufnehmen kann:

```html
<f:translate key="tx_sfpm2_domain_model_person.creditCard" />
<f:form.textfield property="creditCard" />
```

Da dieser Wert aber nicht in der Datenbank gespeichert werden darf, fügen wir diese Eigenschaft auch nicht in unser Domainmodel ein. Wenn wir jetzt speichern erhalten wir eine Exception:

```
#1297759968: Exception while property mapping at property path "":Property "gender" was not found in target object of type "StefanFroemken\Sfpm2\Domain\Model\Person".
```

Diese Exception zwingt uns nun förmlich eine Eigenschaft im Domainmodel anzulegen, aber das wollen wir nicht. Somit behelfen wir uns wieder mit der Property Mapper Konfiguration:

```php
/**
 * initialize create action
 *
 * @return void
 */
public function initializeCreateAction() 
{
    if ($this->arguments->hasArgument('person')) {
        $this->arguments->getArgument('person')->getPropertyMappingConfiguration()->skipProperties('creditCard');
    }
}
```

Auf diese Weise wird die Eigenschaft "creditCard" nun nicht in das Domainmodel gemapped und wird somit auch nicht gespeichert. In die Methode skipProperties() könnt ihr auch mehrere Eigenschaften komma-separiert angeben. Für den weiteren Prozess, wie die Überprüfung der Kreditkartendaten könnt ihr im Controller immer noch mithilfe dieser Zeilen zugreifen:

```php
$person = $this->getControllerContext()->getRequest()->getArgument('person');
$creditCard = $person['creditCard'];
```

## Ausbruch aus der Konvention

Extbase ist voll von Konventionen/Vorgaben. Wenn euer Repository "Person" heißt, dann muss auch das Domainmodel "Person" heißen. Wenn euer Controller "Auto" heißt, dann muss "Auto" auch im Templatepfad enthalten sein. Auch Actionnamen spiegeln sich in den Templatenamen wider. Wer aus diesen Konventionen ausbricht, hat's schwer uns sollte genau wissen, was er tut. Im Folgenden ein Beispiel, um eine Pizza mit verschiedenen Belägen zusammenzustellen. Wir fügen folgende Zeilen in unser Fluid Template:

```html
<f:translate key="tx_sfpm2_domain_model_person.pizza" />

<f:form.checkbox property="pizza" value="Salami" /> Salami
<f:form.checkbox property="pizza" value="Schinken" /> Schinken
<f:form.checkbox property="pizza" value="Paprika" /> Paprika
<f:form.checkbox property="pizza" value="Pilze" /> Pilze
```

Das Ergebnis im Frontend schaut auf den ersten Blick zwar OK aus und es ist auch eine Mehrfachauswahl möglich, aber zum Server wird immer nur die letzte Zutat geschickt. Wenn also Salami und Pilze markiert wurde, wird die Variable Pizza nur mit "Pilze" befüllt. Der Wert "Salami" wird schlicht vergessen. Was im Template fehlt, sind diese eckigen Klammern [], um dem Formularfeld mitzuteilen, dass es mehrere Daten als Array aufnehmen kann. Also probieren wir den nächsten Schritt:

```html
<f:translate key="tx_sfpm2_domain_model_person.pizza" />

<f:form.checkbox property="pizza[]" value="Salami" /> Salami
<f:form.checkbox property="pizza[]" value="Schinken" /> Schinken
<f:form.checkbox property="pizza[]" value="Paprika" /> Paprika
<f:form.checkbox property="pizza[]" value="Pilze" /> Pilze
```

Anstatt besser, wird es jetzt sogar noch schlimmer. Im HTML-Quelltext steht nun:

```
tx_sfpm2_pm2[person][pizza[]]
```

Erst mit TYPO3 6.2 wird ein neuer Parameter "multiple" für Checkboxen eingeführt, der dann korrektes HTML erzeugt. Aber bis dahin müssen solche Implementationen selbst gebaut werden:

```html
<f:translate key="tx_sfpm2_domain_model_person.pizza" />

<input type="checkbox" name="tx_sfpm2_pm2[person][pizza][]" value="Salami" /> Salami
<input type="checkbox" name="tx_sfpm2_pm2[person][pizza][]" value="Schinken" /> Schinken
<input type="checkbox" name="tx_sfpm2_pm2[person][pizza][]" value="Paprika" /> Paprika
<input type="checkbox" name="tx_sfpm2_pm2[person][pizza][]" value="Pilze" /> Pilze
```

Als Dank für eine fehlende Unterstützung seitens Fluid erhalten wir bei Speicherung eine Exception:

```
#1297759968: Exception while property mapping at property path "":It is not allowed to map property "pizza". You need to use $propertyMappingConfiguration->allowProperties('pizza') to enable mapping of this property.
```

## Hintergrund

Sobald der ViewHelper f:form verwendet wird, unterhält sich dieser mit jedem weiteren f:form.* ViewHelper. Wird ein weiteres Formularfeld hinzugefügt, wird auch ein verstecktes Feld mit dem Namen __trustedProperies entsprechend erweitert:

```html
<input name="tx_sfpm2_pm2[__trustedProperties]" value="a:1:{s:6:"person";a:4:{s:9:"firstName";i:1;s:8:"lastName";i:1;s:3:"age";i:1;s:10:"creditCard";i:1;}}3cc0974de797a6278e67448a0b4cefbcc776e6ff" type="hidden">
```

Da wir in dem Beispiel oben aber nicht mehr mit einem f:form.* ViewHelper arbeiten, geht diese Verbindung verloren und dieses versteckte Feld, wird um unser neues Feld nicht erweitert. Nach dem Absenden des Formulars wird innerhalb von Extbase der Wert dieses versteckten Feldes abgearbeitet und jedes dort aufgeführte Formularfeld wird einer Liste der erlaubten Felder hinzugefügt, die auf die Eigenschaften des Domainmodels gemapped werden dürfen.

Das, was Extbase Dank diesem versteckten Feld automatisch für uns abnimmt, müssen wir nun für unser selbst kreiertes Formularfeld nachholen:

```php
/**
 * initialize create action
 *
 * @return void
 */
public function initializeCreateAction() 
{
    if ($this->arguments->hasArgument('person')) {
        $this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowProperties('pizza');
    }
}
```

Nach der Speicherung erhalten wir noch die Exception bzgl. der vergessenen Eigenschaft in unserem Domainmodel:

```
#1297759968: Exception while property mapping at property path "":Property "pizza" was not found in target object of type "StefanFroemken\Sfpm2\Domain\Model\Person".
```

Also fügen wir die Eigenschaft mit Getter und Setter im Domainmodel hinzu:

```php
/**
 * Pizza
 *
 * @var string
 */
protected $pizza;
```
Nach der Speicherung kommt gleich die nächste Exception:

```
#1297759968: Exception while property mapping at property path "pizza":No converter found which can be used to convert from "array" to "string".
```

Da haben wir den Salat. Wir erhalten die Daten für unsere Pizza als Array. Ein Array lässt sich aber nicht so 1zu1 in der Datenbank speichern, weshalb ich die Eigenschaft "Pizza" oben als "string" angegeben habe. Extbase bringt jedoch keinen Konverter mit, mit dem man ein Array in einen String konvertieren kann. Wir haben nun entweder die Möglichkeit unseren eigenen TypeConverter zu programmieren, oder aber teilen dem Property Mapper mit, dass er den Typ "Array" beibehalten soll:

```php
/**
 * initialize create action
 *
 * @return void
*/
public function initializeCreateAction() 
{
    if ($this->arguments->hasArgument('person')) {
        $this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowProperties('pizza');
        $this->arguments->getArgument('person')->getPropertyMappingConfiguration()->setTargetTypeForSubProperty('pizza', 'array');
    }
}
```

Nun landen unsere Werte für unsere Pizza 1 zu 1 als Array in dem Setter unseres Domainmodels setPizza(). Hier können wir uns nun selbst um die Konvertierung kümmern:

```php
/**
 * Returns the pizza
 *
 * @return array $pizza
 */
public function getPizza() 
{
    return explode(',', $this->pizza);
}

/**
 * Sets the pizza
 *
 * @param array $pizza
 * @return void
 */
public function setPizza(array $pizza) 
{
    $this->pizza = implode(',', $pizza);
}
```

Jetzt endlich klappt auch die Speicherung und unser Domainmodel "Person" wird korrekt befüllt:

```
StefanFroemken\Sfpm2\Domain\Model\Personprototypetransient entity
firstName => 'Stefan' (6 chars)
lastName => 'Frömken' (8 chars)
age => 35 (integer)
pizza => 'Salami,Paprika,Pilze' (20 chars)
uid => NULL
_localizedUid => NULL
_languageUid => NULL
_versionedUid => NULL
pid => NULL
```

## Mapping auf andere Eigenschaft

Im oberen Beispiel habe ich die Eigenschaft "Pizza" genannt, obwohl es sich eigentlich um den "Belag" handelt. Deshalb ändere ich das Template entsprechend ab:

```html
<f:translate key="tx_sfpm2_domain_model_person.pizza" />

<input type="checkbox" name="tx_sfpm2_pm2[person][belag][]" value="Salami" /> Salami
<input type="checkbox" name="tx_sfpm2_pm2[person][belag][]" value="Schinken" /> Schinken
<input type="checkbox" name="tx_sfpm2_pm2[person][belag][]" value="Paprika" /> Paprika
<input type="checkbox" name="tx_sfpm2_pm2[person][belag][]" value="Pilze" /> Pilze
```

Die Eigenschaft "Pizza" im Domainmodel lassen wir aber weiterhin bestehen, denn wir können mithilfe des Property Mappers bestimmen welches Formularfeld auf welche Domaineigenschaft gemapped werden soll:

```php
/**
 * initialize create action
 *
 * @return void
*/
public function initializeCreateAction() {
    if ($this->arguments->hasArgument('person')) {
        $this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowProperties('pizza');
        $this->arguments->getArgument('person')->getPropertyMappingConfiguration()->setTargetTypeForSubProperty('pizza', 'array');
        $this->arguments->getArgument('person')->getPropertyMappingConfiguration()->setMapping('belag', 'pizza');
    }
}
```

## Unterobjekte erlauben

Alles, was jetzt kommt, kann Extbase von Haus aus alleine. Es gibt nur schon mal Konstellationen, da muss das Formular eben selbst gebaut werden. Um einen solchen Fall nachzustellen, setzen wir das Domainmodel "Person" als z.B. Ehepartner in Relation zu ebenfalls "Person". Für diesen Fall fügen wir nun noch den Vor- und Nachnamen des Ehepartners unserer Person hinzu:

```html
<f:translate key="tx_sfpm2_domain_model_person.first_name" />
<input type="text" name="tx_sfpm2_pm2[person][partner][firstName]" />

<f:translate key="tx_sfpm2_domain_model_person.last_name" />
<input type="text" name="tx_sfpm2_pm2[person][partner][lastName]" />
```

Da wir hier wieder einmal mit einer Eigenimplementation arbeiten, können die Eigenschaften firstName und lastName nicht dem versteckten Formularfeld __trustedProperties hinzugefügt werden. Extbase kann dadurch kein Mapping für die Formularfelder zu den Eigenschaften in den Domainmodellen erstellen. Die Exception, die jetzt allerdings erscheint, schweift von der Exception von oben ab:

```
#1297759968: Exception while property mapping at property path "":It is not allowed to map property "partner". You need to use $propertyMappingConfiguration->allowProperties('partner') to enable mapping of this property.
```

Wir machen mal genau das, was uns die Exception vorschlägt:

```php
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowProperties('partner');
```

Es resultiert in die nächste Exception:

```
#1297759968: Exception while property mapping at property path "partner":It is not allowed to map property "firstName". You need to use $propertyMappingConfiguration->allowProperties('firstName') to enable mapping of this property.
```

Die Exception kennen wir noch von weiter oben und fügen das fehlende Mapping der Felder hin

```php
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowProperties('partner');
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->forProperty('partner')->allowProperties('firstName', 'lastName');
```

Obwohl jetzt alle Eigenschaften des Formulars erlaubt sind, kommt noch eine Exception:

```
#1297759968: Exception while property mapping at property path "partner":Creation of objects not allowed. To enable this, you need to set the PropertyMappingConfiguration Value "CONFIGURATION_CREATION_ALLOWED" to TRUE
```

Aufgrund von Sicherheit ist es im neuen Property Mapper nicht erlaubt Unterobjekte zu erzeugen. Dies muss explizit erlaubt werden:

```php
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowProperties('partner');
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->forProperty('partner')->allowProperties('firstName', 'lastName');
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowCreationForSubProperty('partner');
```

Wenn ihr später die Daten des Partners ändern wollt, dann müsst ihr auch das Bearbeiten explizit erlauben. Und wenn ihr einer Person während des Bearbeitens das erste Mal einen neuen Partner zuordnet, dann müsst ihr auch das Anlegen von Unterobjekten beim Update (initializeUpdateAction) erlauben:

```php
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowProperties('partner');
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->forProperty('partner')->allowProperties('firstName', 'lastName');
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowCreationForSubProperty('partner');
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowModificationForSubProperty('partner');
```

## Mehrere Unterobjekte erlauben mit Stern (*)

Wir treiben den Wahnsinn noch etwas weiter. Auch wenn ich nicht unbedingt der Fan davon bin, so wollen wir es nun erlauben, dass unsere Person mehrere Partner haben kann. Dazu ändern wir zunächst wieder das Fluidtemplate:

```html
<f:translate key="tx_sfpm2_domain_model_person.first_name" />
<input type="text" name="tx_sfpm2_pm2[person][partner][0][firstName]" />

<f:translate key="tx_sfpm2_domain_model_person.last_name" />
<input type="text" name="tx_sfpm2_pm2[person][partner][0][lastName]" />

<f:translate key="tx_sfpm2_domain_model_person.first_name" />
<input type="text" name="tx_sfpm2_pm2[person][partner][1][firstName]" />

<f:translate key="tx_sfpm2_domain_model_person.last_name" />
<input type="text" name="tx_sfpm2_pm2[person][partner][1][lastName]" />
```

Nach dem Absenden des Formulars hagelt es wieder eine Exception:

```
#1297759968: Exception while property mapping at property path "partner":It is not allowed to map property "0". You need to use $propertyMappingConfiguration->allowProperties('0') to enable mapping
```

Ihr seht schon, wohin das Problem führt. Wenn wir die Felder für einen Partner z.B. mithilfe von jQuery so oft hintereinander anlegen lassen, wie die Person Partner hat, dann müssten wir für jeden weiteren Partner eine weitere allowProperties-Zeile im Controller schreiben. Das kann keine Lösung sein. Deshalb hat das Flow-Team den Stern als Platzhalter für mehrere Elemente eingeführt:

```php
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowProperties('partners');
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->forProperty('partners.*')->allowProperties('firstName', 'lastName');
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowCreationForSubProperty('partners.*');
$this->arguments->getArgument('person')->getPropertyMappingConfiguration()->allowModificationForSubProperty('partners.*');
```

Es muss für jede Untereigenschaft eine Konfiguration angelegt werden. Normalerweise übernimmt Extbase diese Aufgabe für uns, aber bei selbstgebauten Formularfeldern, müssen wir diese selbst erstellen. Unterstützend wirkt hier die Methode forProperty(). Hier kann ein Propertypath angegeben werden. So wäre es z.B. möglich hier "auto.reifen.felge.hersteller" anzugeben. Für jede Eigenschaft in diesem Pfad legt forProperty automatisch eine Property Mapper Konfiguration an. Und mit allowProperties() können wir direkt auf diese neu angelegte Konfiguration zugreifen und bestimmte Eigenschaften direkt erlauben. Auch die Befehle für das Erlauben und Bearbeiten von Unterobjekte kann mit der Pfadsyntax umgehen.
