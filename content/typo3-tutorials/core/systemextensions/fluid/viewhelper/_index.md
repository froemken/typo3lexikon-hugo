+++
title = "ViewHelper"
date = 2024-01-09T20:27:06+01:00
ordersectionsby = "linktitle"
alwaysopen = false
aliases = ["viewhelper.html"]
+++

Mit reinem HTML wird wiederkehrender Quellcode sehr schnell sehr groß und unübersichtlich. Im Hinblick auf TYPO3, wo Seiten verschoben und umbenannt werden können, müssten Links und URLs im HTML Quelltext immer wieder erneut angepasst werden. Abhilfe schaffen hier besondere HTML-Tags, die von Fluid, dem Core und anderen Extensions mitgeliefert werden. Mit diesen sogenannten ViewHelpern können direkt im HTML Quelltext Schleifen, Verlinkungen zu TYPO3 Seiten, Abfragen und das Ausliefern von per TypoScript generiertem Inhalt realisiert werden. Dank Layouts, Partials und Templates können die HTML-Templates sehr dynamisch und wiederverwendbar strukturiert werden.

Im Folgenden eine Auflistung der zur Verfügung stehenden ViewHelper.

## f:alias

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Mit diesem ViewHelper könnt ihr eigene Variablen innerhalb des öffnenden und schließenden Tags zur Verfügung stellen. Das ist sinnvoll, wenn ihr einen bestimmten Wert z.B. aus Objekten oder eine etwas längere ViewHelper Aneinanderreihung in eurem Template mehrfach wieder verwenden wollt.

| Parameter | Erklärung |
|-----------|-----------|
| map       | Die Eingabe erfolgt in Arraynotation. Der Key gibt den Namen der neuen Variable wieder, während der Wert den Inhalt wieder spiegelt. |

### Beispiel für einfache Texte

```html
<f:alias map="{vorname: 'Stefan', nachname: 'Froemken'}">
    <p>Hallo zusammen,<br>mein Name ist {vorname} {nachname}</p>
</f:alias>
```

### Beispiel für selbst erstellte Arrays

Dieses Beispiel ist vielleicht wenig sinnvoll, aber es zeigt sehr gut, wie man innerhalb von Fluid mehrdimensionale Arrays erstellen kann.

```html
<f:alias map="{company: {name: 'Mueller und Co.', mitarbeiter: {0: {name: 'Stefan'}, 1: {name: 'Petra'}}}}">
    <p>Wir, die Firma {company.name}, haben folgende Mitarbeiter:</p>
    <ul>
        <f:for each="{company.mitarbeiter}" as="mitarbeiter">
            <li>{mitarbeiter.name}</li>
        </f:for>
    </ul>
</f:alias>
```

### Beispiel für Werte aus Objekten

Dieses Beispiel funktioniert nur, wenn ihr das `feUser` Objekt in eurer Extension per `$this->view->assign()` verfügbar gemacht habt.

```html
<f:alias map="{vorname: feUser.firstName, nachname: feUser.lastName}">
    <p>Hallo zusammen,<br />mein Name ist {vorname} {nachname}</p>
</f:alias>
```

## f:base

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Erstellt einen `<base>` Tag. Mit diesem Tag wird ein Pfad angegeben, der die Grundlage aller anderen Pfade wie Links und Bilder vorgibt. Diesen Basispfad holt sich der ViewHelper vorzugweise aus der Site-Konfiguration. In älteren TYPO3 Versionen wurden die TypoScript Eigenschaften `config.baseURL` und `config.absRefPrefix` dafür ausgelesen.

{{% notice style="info" title="Information" icon="exclamation" %}}
Da der `<base>` Tag in den `<head>` Bereich einer Webseite gehört, macht die Verwendung diesen ViewHelpers nur dann Sinn, wenn ihr eure komplette Webseite mithilfe von Fluid aufbaut. Definiert ihr die Angaben für den `<head>` Bereich über TypoScript und dem Site-Modul, würde diese ViewHelper evtl. im `<body>` Tag landen, wo er nicht hingehört.
{{% /notice %}}

```html
<f:base />
```

Die Ausgabe im Quelltext:

```html
<base href="https://www.typo3lexikon.de/" />
```

## f:case

{{% badge style="green" icon="angle-double-up" %}}TYPO3 6.2{{% /badge %}}

| Parameter | Erklärung |
|-----------|-----------|
| value     | Der Inhalt des f:case ViewHelpers wird nur dann angezeigt, wenn der Wert von value dem expression-Argument aus dem f:switch ViewHelper übereinstimmt 
| default   | Ein f:case ViewHelper, bei dem default aktiviert wurde, wird immer dann angezeigt, wenn kein anderer f:case ViewHelper zu der expression des f:switch ViewHelpers passt.

Ein Beispiel findet ihr beim f:switch ViewHelper

## f:cObject

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Dieser ViewHelper nutzt den angegeben `typoscriptObjectPath`, um das dahinterliegende TypoScript auszuführen und das Resultat im Template auszugeben.

| Parameter            | Erklärung | Pflichtfeld |
|----------------------|-----------|-------------|
| typoscriptObjectPath | Gebt hier den TypoScript Objektpfad an wie z.B. `lib.beispieldaten.10` | Ja |
| data                 | Übergebt dem TypoScript Daten in Form eines Arrays, Objektes oder auch einen Text, auf dessen Basis das TypoScript Rendering vonstatten gehen soll. Im Falle von Array oder Objekt könnt ihr mit der TypoScript Eigenschaft `field` auf individuelle Daten zugreifen. Im Falle von Text verwendet bitte `current`.
| currentValueKey      | Angabe eines Array Keys oder Objekt Eigenschaft, die innerhalb von TypoScript über `current` zur Verfügung gestellt werden soll.

### Beispiel mit Array

Sofern mittels `assign` zur Verfügung gestellt, wird der aktuelle `tt_content` Datensatz als Array dem TypoScript unter `lib.content` zur weiteren Verarbeitung durchgereicht. Mittels `field` kann das TypoScript auf alle Eigenschaften des `tt_content` Datensatzes zugreifen. Über `current` kann das TypoScript auf die Überschrift des `tt_content` Datensatzen direkt zugreifen.

```html
<f:cObject typoscriptObjectPath="lib.content" data="{ttContentRecord}" currentValueKey="header" />
```

### Beispiel mit Text

Wenn statt Array oder Objekt nur Text übergeben wird, dann wird dieser Text automatisch dem TypoScript über `current` zur Verfügung gestellt.

```html
<!-- Beliebig viel Text mit öffnendem und schließendem Tag -->
<f:cObject typoscriptObjectPath="lib.content">
Stefan heisst Euch willkommen
</f:cObject>

<!-- Ein Einzeiler an Text mittels data Attribut -->
<f:cObject typoscriptObjectPath="lib.content" data="Stefan heisst Euch willkommen" />
```

## f:comment

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}

Während HTML Kommentare auch im Quellcode ausgegeben werden, können mit `f:comment` ganze Bereiche vom Quellcode ausgeschlossen werden. Diese kommen somit im HTML Quellcode nicht mehr vor und die enthaltenen ViewHelper werden auch nicht ausgeführt. Sehr nützlich im Rahmen von Debugging.

### Beispiel

Alles zwischen den `f:comment` Tags wird nicht ausgegeben und enthaltene ViewHelper nicht verarbeitet.

```html
<f:comment><!--
<p>Dieser Text wird nicht angezeigt</p>
<p>Mein Name ist: {vorname}</p>
ViewHelper wird nicht verarbeitet: <f:alias map="{}"></f:alias>
--></f:comment>
```

## f:count

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Dieser ViewHelper zählt die Elemente im übergebenen Array oder die enthaltenen Objekte eines zählbaren Objektes wie `\SplObjectStorage` oder dem Extbase `ObjectStorage`.

| Parameter | Erklärung |
|-----------|-----------|
| subject   | Gebt hier das Array oder das Objekt an, dessen Elemente gezählt werden sollen. Wenn dieser Wert leer gelassen wird, versucht `f:count` das Array oder Objekt zwischen dem öffnenden und schließendem Tag zu zählen |

### Beispiel mit subject

```html
<f:alias map="{mitarbeiter: {0: 'Stefan',1: 'Petra',2: 'Sascha'}}">
    <p>Bei uns arbeiten <f:count subject="{mitarbeiter}" /> Mitarbeiter</p>
</f:alias>
```

### Beispiel mit Inhalt

```html
<f:alias map="{mitarbeiter: {0: 'Stefan', 1: 'Petra', 2: 'Sascha'}}">
    <p>Bei uns arbeiten <f:count>{mitarbeiter}</f:count> Mitarbeiter</p>
</f:alias>
```

### Beispiel Inlinenotation

```html
<f:alias map="{mitarbeiter: {0: 'Stefan',1: 'Petra',2: 'Sascha'}}">
    <p>Bei uns arbeiten {f:count(subject: mitarbeiter)} Mitarbeiter</p>
</f:alias>
```

### Beispiel mit renderChildren

```html
<f:alias map="{mitarbeiter: {0: 'Stefan',1: 'Petra',2: 'Sascha'}}">
    <p>Bei uns arbeiten {mitarbeiter -> f:count()} Mitarbeiter</p>
</f:alias>
```

## f:cycle

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

`f:cycle` wird im Rahmen von Schleifen wie `f:for` verwendet, um für jedes xte Element in der Schleife eine Sonderbehandlung durchführen zu können. Gerade `odd` und `even` Operationen, um abwechselnde Hintergrundfarben in Tabellen zu erzeugen sinnvoll.

| Parameter | Erklärung |
|-----------|-----------|
| values    | Ein Array mit Werten für jeden xten Durchlauf |
| as        | Variablenname, mit der Wert aus `values`, der nur zwischen öffnendem und schließendem `f:cycle` Tag Gültigkeit hat |

### Beispiel

`f:for` durchläuft alle 5 Mitarbeiter. `f:cycle` spring mit jedem Durchlauf von `f:for` ein Element aus `values` weiter. Somit bekommt der erste Mitarbeiter einen grünen, der zweiter Mitarbeiter einen roten und der dritte Mitarbeiter einen blauen Hintergrund. Ab dem vierten Mitarbeiter geht es wieder mit einem grünen Hintergrund weiter.

```html
<f:alias map="{mitarbeiter: {0: {vorname: 'Stefan', stadt: 'Lindlar'}, 1: {vorname: 'Petra', stadt: 'Lindlar'}, 2: {vorname: 'Sascha', stadt: 'Remscheid'}, 3: {vorname: 'Patrick', stadt: 'Bonn'}, 4: {vorname: 'Sven', stadt: 'Gummersbach'}, 5: {vorname: 'Andrea', stadt: 'Wuppertal'}}}">
    <table cellpadding="5" cellspacing="0" border="2">
        <f:for each="{mitarbeiter}" as="kollege">
            <f:cycle values="{0: 'green', 1: 'red', 2: 'blue'}" as="color">
                <tr>
                    <td style="color: {color}">{kollege.vorname}</td>
                    <td style="color: {color}">{kollege.stadt}</td>
                </tr>
            </f:cycle>
        </f:for>
    </table>
</f:alias>
```

## f:debug

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Zeigt den Inhalt einer Variable oben am Rand des Browsers an. Verschachtelte Elemente können mittels dem `+` Icon weiter aufgeklappt werden.

| Parameter | Erklärung |
|-----------|-----------|
| title | Gibt der Debug-Ausgabe einen Titel. Bei mehreren Debug-Ausgaben können so die jeweiligen Ausgaben besser differenziert werden. |
| maxDepth | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.7{{% /badge %}} Durch die teilweise sehr tiefe Verschachtelung von Objekten kann die Debug-Ausgabe sehr groß werden. Mit `maxDepth` kann die Ausgabe nach Erreichen der angegebenen Tiefe unterbunden werden. |
| plainText | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.7{{% /badge %}} Anstatt einer HTML/JavaScript Ausgabe könnt ihr die Ausgabe auch komplett als reinen Text ausgeben lassen. |
| ansiColors | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.7{{% /badge %}} Wenn `plainText` aktiv ist, könnt ihr mit `ansiColors` die Textausgabe mit etwas Farbe aufhübschen |
| inline | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.7{{% /badge %}} Üblicherweise wird die Debug-Ausgabe oben am Rand des Browsers ausgegeben. Durch Aktivierung von `inline` wird die Ausgabe dort platziert, wo sich der `f:debug` ViewHelper im HTML befindet. |
| blacklistedClassNames | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.7{{% /badge %}} Einige Klassen werden bei der Debug-Ausgabe sehr groß. So z.B. der `ObjectManager`. Gebt deren Klassennamen als Array an, um deren Ausgabe zu verhindern |
| blacklistedPropertyNames | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.7{{% /badge %}} Ähnlich wie `blacklistedClassNames`, nur mit dem Unterschied, dass hiermit bestimmte Eigenschaften von der Debug-Ausgabe ausgeschlossen werden. |

### Beispiel

```html
<f:alias map="{mitarbeiter: {0: {vorname: 'Stefan', stadt: 'Lindlar'}, 1: {vorname: 'Petra', stadt: 'Lindlar'}, 2: {vorname: 'Sascha', stadt: 'Remscheid'}, 3: {vorname: 'Patrick', stadt: 'Bonn'}, 4: {vorname: 'Sven', stadt: 'Gummersbach'}, 5: {vorname: 'Andrea', stadt: 'Wuppertal'}}}">
    <f:debug title="Mitarbeiter">{mitarbeiter}</f:debug>
</f:alias>
```

## f:else

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Diesen ViewHelper erkläre ich im ViewHelper `f:if`, da er nur dort verwendet werden kann.

| Parameter | Erklärung |
|-----------|-----------|
| if | {{% badge style="green" icon="angle-double-up" %}}TYPO3 8.0{{% /badge %}} Durch das neue `if` Attribut können nun mehrfache else-if Abfragen realisiert werden |

## f:escape

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.4{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 4.6{{% /badge %}}
{{% badge style="red" icon="skull-crossbones" %}}TYPO3 6.0{{% /badge %}}

Diesen ViewHelper gibt es nicht mehr. Bitte verwendet einen der `f:format.*` ViewHelper.

## f:flashMessages

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.4{{% /badge %}}

Dieser ViewHelper macht nur im Bereich selbstprogrammierter Extensions Sinn. Denn nur hier können Fehler auftauchen, die dem Webseitenbesucher mitgeteilt werden müssen. Hat der User z.B. bei einem Loginformular seinen Usernamen vergessen anzugeben und die dafür zuständige Action-Methode wurde so programmiert, dass der Username eine Pflichtangabe ist, dann wird dies dem Validator gemeldet, der daraufhin eine errorAction-Methode aufruft, die dann wiederum Fehlermeldungen zuerst sammelt und dann als "Bündel" an der Stelle ausgibt, an der ihr diesen ViewHelper platziert habt.

| Parameter | Erklärung |
|-----------|-----------|
| renderMode | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.4{{% /badge %}} {{% badge style="orange" icon="angle-double-up" %}}TYPO3 7.3{{% /badge %}} {{% badge style="red" icon="skull-crossbones" %}}TYPO3 8.0{{% /badge %}}Sollen die Fehlermeldungen als Liste ul oder als Container div gerendert werden.
| as | {{% badge style="green" icon="angle-double-up" %}}TYPO3 7.3{{% /badge %}} Die Angabe aus `as` wird zum Erstellen einer Variable für das Fluid-Template genutzt, die alle Benachrichtigungen beinhaltet. Um das Rendering dieser Nachrichten muss sich selbst gekümmert werden. |
| queueIdentifier | {{% badge style="green" icon="angle-double-up" %}}TYPO3 7.6{{% /badge %}} Alle Benachrichtigungen einer Extension landen normalerweise in einer Warteschlange mit dem Namen `extbase.flashmessages.[extension]_[plugin]`. Da ihr aber auch eigene Namen vergeben könnt, war es bisher nicht möglich diese im Frontend rendern zu lassen. Dieser Parameter schafft Abhilfe. |

{{% notice style="info" title="Information" icon="exclamation" %}}
Seit TYPO3 8.6 gibt es den `FlashMessageRenderResolver`. Immer dann, wenn das `as` Attribut nicht angegeben wurde, springt der Resolver ein und rendert die Benachrichtigungen je nach Kontext. Im TYPO3 Backend findet also eine andere Ausgabe statt als im Frontend und nochmals anders, wenn die Ausgabe im CLI erfolgt.
{{% /notice %}}

### Beispiel: Resolver

Hier spring der `FlashMessageRenderResolver` ein und rendert im Frontend Kontext eine Liste auf Basis von `div` Tags.

```html
<f:flashMessages />
```

### Beispiel: Eigenes Rendering

```html
<f:flashMessages as="flashMessages">
    <f:for each="{flashMessages}" as="flashMessage">
        {flashMessage.code}
        {flashMessage.message}
    </f:for>
</f:flashMessages>
```

## f:for

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Erzeugt eine Schleife. Jedes Element des übergebenen Arrays oder ObjectStorages wird durchlaufen und kann als Datenbasis des zugrunde liegenden Templates verwendet werden.

| Parameter | Erklärung |
|-----------|-----------|
| each | Array oder Objekt, das durchlaufen werden soll |
| as | Ein Variablenname, der Daten des aktuellen Durchlaufs enthält |
| key | Falls ihr den Key/Schlüssel des aktuellen Durchlaufes benötigt, könnt ihr hiermit den Namen einer weiteren Variable definieren |
| reverse | Der Durchlauf des Arrays oder Objektes geschieht rückwärts |
| iteration | Eine Arrayvariable, die Informationen darüber beinhaltet, ob man sich im ersten oder letzten Durchlauf befindet. Außerdem enthalten: `index`, `cycle`, `total`, `isEven`, `isOdd` |

### Beispiel

Ich weise der Variable mitarbeiter 6 Einträge zu, die dann eins nach dem Anderen von f:for durchlaufen werden. Mit jedem Durchlauf wird eine weitere Tabellenzeile erstellt.

```html
<f:alias map="{mitarbeiter: {0: {vorname: 'Stefan', stadt: 'Lindlar'}, 1: {vorname: 'Petra', stadt: 'Lindlar'}, 2: {vorname: 'Sascha', stadt: 'Remscheid'}, 3: {vorname: 'Patrick', stadt: 'Bonn'}, 4: {vorname: 'Sven', stadt: 'Gummersbach'}, 5: {vorname: 'Andrea', stadt: 'Wuppertal'}}}">
    <table cellpadding="5" cellspacing="0" border="2">
        <f:for each="{mitarbeiter}" as="kollege">
            <tr>
                <td>{kollege.vorname}</td>
                <td>{kollege.stadt}</td>
            </tr>
        </f:for>
    </table>
</f:alias>
```

### Beispiel für rückwärts

```html
<f:alias map="{mitarbeiter: {0: {vorname: 'Stefan', stadt: 'Lindlar'}, 1: {vorname: 'Petra', stadt: 'Lindlar'}, 2: {vorname: 'Sascha', stadt: 'Remscheid'}, 3: {vorname: 'Patrick', stadt: 'Bonn'}, 4: {vorname: 'Sven', stadt: 'Gummersbach'}, 5: {vorname: 'Andrea', stadt: 'Wuppertal'}}}">
    <table cellpadding="5" cellspacing="0" border="2">
        <f:for each="{mitarbeiter}" as="kollege" reverse="1">
            <tr>
                <td>{kollege.vorname}</td>
                <td>{kollege.stadt}</td>
            </tr>
        </f:for>
    </table>
</f:alias>
```

### Beispiel mit Array-Key

Ich weiß, das ist jetzt nicht das beste Beispiel, aber ihr seht, wie ich mithilfe des `key` Attributes auf die Array-Keys der jeweiligen Einträge zugreifen kann.

```html
<f:alias map="{mitarbeiter: {0: {vorname: 'Stefan', stadt: 'Lindlar'},1: {vorname: 'Petra', stadt: 'Lindlar'},2: {vorname: 'Sascha', stadt: 'Remscheid'},3: {vorname: 'Patrick', stadt: 'Bonn'},4: {vorname: 'Sven', stadt: 'Gummersbach'},5: {vorname: 'Andrea', stadt: 'Wuppertal'}}}">
    <table cellpadding="5" cellspacing="0" border="2">
        <f:for each="{mitarbeiter}" as="kollege" reverse="1" key="eintrag">
            <tr>
                <th colspan="2">Eintrag: {eintrag}</th>
            </tr>
            <tr>
                <td>{kollege.vorname}</td>
                <td>{kollege.stadt}</td>
            </tr>
        </f:for>
    </table>
</f:alias>
```

### Beispiel mit Durchlaufinformationen

`iterator.cycle` ist genau wie `iterator.index` einfach nur ein Zähler und hat nichts mit dem ViewHelper `f:cycle` zu tun. Dieses Beispiel zeigt die Verwendung aller Durchlaufinformationen. Auch wenn ihr den `f:if` ViewHelper noch nicht kennengelernt haben solltet, so sollte dieses Beispiel selbsterklärend sein.

```html
<f:alias map="{mitarbeiter: {0: {vorname: 'Stefan', stadt: 'Lindlar'}, 1: {vorname: 'Petra', stadt: 'Lindlar'}, 2: {vorname: 'Sascha', stadt: 'Remscheid'}, 3: {vorname: 'Patrick', stadt: 'Bonn'}, 4: {vorname: 'Sven', stadt: 'Gummersbach'}, 5: {vorname: 'Andrea', stadt: 'Wuppertal'}}}">
    <table cellpadding="5" cellspacing="0" border="2">
        <f:for each="{mitarbeiter}" as="kollege" iteration="iterator">
            <f:if condition="{iterator.isFirst}">
                <tr><th colspan="2">Los gehts</th></tr>
            </f:if>
            <tr>
                <td>Durchlauf beginnend bei 0: {iterator.index}</td>
                <td>Durchlauf beginnend bei 1: {iterator.cycle}</td>
                <td{f:if(condition:iterator.isOdd, then: ' style="color: green;"')}>{kollege.vorname}</td>
                <td{f:if(condition:iterator.isEven, then: ' style="color: red;"')}>{kollege.stadt}</td>
            </tr>
            <f:if condition="{iterator.isLast}">
                <tr><th colspan="2">Eintraege: {iterator.total}</th></tr>
            </f:if>
        </f:for>
    </table>
</f:alias>
```

## f:form

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Dieser ViewHelper ist für die Erstellung von HTML Formularen zuständig und bildet die zentrale Schnittstelle für alle anderen `f:form.*` ViewHelper.

| Parameter | Erklärung |
|-----------|-----------|
| action | Welche Actionmethode soll aufgerufen werden, wenn das Formular abgesendet wird |
| arguments | Welche zusätzlichen Variablen sollen beim Absenden mit übergeben werden |
| controller | Falls sich die gewünschte Actionmethode nicht im gleichen Controller befindet, muss hier dieser entsprechende Controller angegeben werden |
| extensionName | Falls das Formular von einer anderen Extension abgearbeitet werden soll, dann muss hier der Extensionname ohne tx_ und ohne Unterstriche angegeben werden |
| pluginName | Falls das Formular von einem anderen Plugin abgearbeitet werden soll, dann muss hier der Pluginname angegeben werden |
| pageUid | Seiten-UID eintragen, wenn das Formular von einer anderen Seite aus abgearbeitet werden soll |
| object | Übergebt hier ein Objekt mit Eigenschaften, die die Eingabefelder im Formular wiederspiegeln. |
| pageType | Gebt hier eine Seitentyp ID an, die das Formular abarbeiten soll. Könnte für AJAX interessant sein. |
| noCache | Kann aktiviert werden, um das Caching für die Zielseite zu deaktivieren. |
| noCacheHash | Nach Aktivierung wird dem generierten Link zur Zielseite kein cHash-Parameter angehangen |
| section | Definition eines Ankers zu dem auf der Zielseite gesprungen werden soll. Interessant wir Seiten auf denen viele Inhalte sind. |
| format | Gibt an um welches Format es sich bei der Zielseite handelt. Alternativ ginge auch `xml`, obwohl das bei einer Formularzielseite wenig Sinn machen würde. Klappt nur wenn `actionUri` nicht gesetzt ist. |
| additionalParams | Fügt weitere Variablen der Zielseite an. Im Gegensatz zu `arguments`, können hiermit Variablen hinzugefügt werden die nicht mit dem Extensionnamen geprefixed werden. Klappt nur, wenn `actionUri` nicht gesetzt ist. |
| absolute | Nach Aktivierung wird der Zeilseite noch der Domainname und Pfad vorangestellt. Klappt nur, wenn `actionUri` nicht gesetzt ist. |
| addQueryString | Falls dem Formular bereits Parameter über die URL mitgegeben wurden, könnt ihr hier entscheiden, ob diese Parameter auch mit auf die Zielseite übergeben werden. Klappt nur, wenn actionUri nicht gesetzt ist. |
| argumentsToBeExcludedFromQueryString | Falls ihr `addQueryString` aktiviert habt, aber einen oder zwei bestimmte Parameter wieder entfernen wollt, dann tragt ihr hier diese Parameter ein. Klappt nur, wenn `actionUri` nicht gesetzt ist. |
| addQueryStringMethod | {{% badge style="green" icon="angle-double-up" %}}TYPO3 7.6{{% /badge %}} Normalerweise werden nur die GET Parameter bei Verwendung von `addQueryString` an die Zielseite angehangen. Dieser Parameter erlaubt 4 Werte. `GET`: Standard. Die GET-Parameter werden übergeben. `POST`: Die POST Parameter werden mit übergeben. `GET,POST`: GET-Parameter sind die Basis und werden mit POST Parametern überschrieben, sofern beide Methoden gleichnamige Parameter beinhalten. `POST,GET`: POST-Parameter werden mit GET-Parametern überschrieben. |
| fieldNamePrefix | Falls ein anderer Prefix gewünscht ist. Macht eigentlich nur Sinn, wenn die Formulardaten von einer anderen Extension abgearbeitet werden müssen. |
| actionUri | Gebt hier Eure ganz eigene individuelle Zielseiten-URL ein. Viele der oberen Parameter haben aber dann keine Wirkung mehr. |
| objectName | Hier kommt ein Objekt- bzw. Modelname rein, in das die nach Absenden gesammelten Formulardaten gespeichert werden sollen. Hat den Vorteil, dass Ihr nicht in jeder Action die Formulardaten überprüfen, sondern die Überprüfung nur einmalig im Model vornehmen müsst. |
| hiddenFieldClassName | {{% badge style="green" icon="angle-double-up" %}}TYPO3 4.7{{% /badge %}} Der `f:form` ViewHelper rendert ein paar hidden Felder, um z.B. eine Liste über die erlaubten Formularfelder zu generieren oder auch die Action zu kennen, die dieses Formular rendern lässt. Mit diesem Parameter könnt Ihr dem div-Tag, der diese hidden Felder enthält explizit setzen. |

### Beispiel

Fluid Template:

```html
<f:form object="{feUser}" objectName="newFeUser">
    <f:form.textarea property="firstName" rows="5" cols="50" />
</f:form>
```
HTML Ausgabe:

```html
<form action="/typo3_46/index.php?id=6&amp;tx__%5Bcontroller%5D=Standard&amp;cHash=d1469ddb628871564f3257920c1f6ee8" method="post">
    <div style="display: none">
        <input type="hidden" name="__referrer[extensionName]" value="" />
        <input type="hidden" name="__referrer[controllerName]" value="Standard" />
        <input type="hidden" name="__referrer[actionName]" value="index" />
        <input type="hidden" name="__hmac" value="a:2:{s:9:&quot;newFeUser&quot;;a:1:{s:9:&quot;firstName&quot;;i:1;}s:4:&quot;tx__&quot;;a:1:{s:10:&quot;controller&quot;;i:1;}}ff5ff9b62f7b5c49a696d3f7b1009991853d6533" /> 
    </div>
    <textarea rows="5" cols="50" name="newFeUser[firstName]"></textarea>
</form>
```

Hier seht Ihr das Thema Sicherheit. Fluid baut automatisch einen versteckten Bereich mit ein paar Werten in Euer Formular ein. Unteranderem befindet sich dort ein __hmac-Wert. Innerhalb dieses Wertes sind alle erlaubten Formularfelder nochmals enthalten. Wenn also über bestimmte Webseitenattacken Felder entfernt oder hinzugefügt werden, dann kann Extbase später feststellen, dass die Anzahl und/oder Feldnamen nicht übereinstimmen und wirft eine Fehlermeldung. Das Formular kann also nicht abgesendet werden.

Ich habe in meinem Beispiel noch einen Objektnamen angegeben `newFeUser`. Wie ihr seht, wurde dieser Wert jedem Feld in meinem Formular vorangestellt. Das hat den Vorteil, dass meine Formularfelder nicht einzeln, sondern gebündelt in einem Array an die Zielseite übertragen werden. Dort angekommen kann ich meiner Action mitteilen, dass der Inhalt diesen Arrays in ein Modell überführt werden soll. Bei dieser Überführung werden auch automatisch die enthaltenen Werte auf Gültigkeit überprüft, sofern ihr überhaupt Überprüfungsregeln in euren Modellen angegeben habt. Nur wenn alle Überprüfungen gültig waren, kann das Modell mit den Formulardaten an die Action übergeben werden und dort mit einem Einzeiler in der Datenbank gespeichert werden.

## f:groupedFor

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Ein sehr mächtiger ViewHelper im Bereich der Listengenerierung. Übergebt dem ViewHelper ein Array und ein Gruppierungskriterium und ihr erhaltet mit jedem Durchlauf bzw. mit jeder gefundenen Gruppe ein Array mit den dazugehörigen Arrayelementen zurück. Zu kompliziert? Na dann schaut mal in die Beispiele.

| Parameter | Erklärung |
|-----------|-----------|
| each | Array oder Objekt, das durchlaufen werden soll |
| as | Ein Variablenname, der die gruppierten Datensätze enthält |
| groupBy | anhand welcher Eigenschaft soll das Array gruppiert werden |
| groupKey | Innerhalb der `f:groupedBy` Tags kann mit dieser Variable auf den gruppierten Wert zugegriffen werden. |

### Beispiel

Ich hab hier wieder das Beispiel mit den 6 Mitarbeitern ausgepackt. Wie Ihr sehen könnt habe ich dem `f:groupedBy` ViewHelper gesagt, dass er diese Mitarbeiter anhand ihrer Städte gruppieren soll (groupBy). Das geübte Auge sieht evtl. sofort, dass die ersten beiden Arrayeinträge in Lindlar wohnen. Um innerhalb der `f:groupedBy` Tags auf diesen Städtenamen zugreifen zu können, verwende ich den Parameter groupKey. Der Wert aus groupBy und groupKey müssen nicht übereinstimmen. Innerhalb von groupKey hätte ich auch einfach city nehmen können.

Über die Variable, die ich unter "as" angegeben habe kann ich nun auf die Elemente der ersten gefundenen Gruppe zugreifen und mit `f:for` durchlaufen lassen.

```html
<f:alias map="{mitarbeiter: {0: {vorname: 'Stefan', stadt: 'Lindlar'},1: {vorname: 'Petra', stadt: 'Lindlar'},2: {vorname: 'Sascha', stadt: 'Remscheid'},3: {vorname: 'Patrick', stadt: 'Bonn'},4: {vorname: 'Sven', stadt: 'Gummersbach'},5: {vorname: 'Andrea', stadt: 'Wuppertal'}}}">
    <table cellpadding="5" cellspacing="0" border="2">
        <f:groupedFor each="{mitarbeiter}" as="kollegen" groupBy="stadt" groupKey="stadt">
            <tr>
                <th colspan="2">{stadt}</th>
            </tr>
            <f:for each="{kollegen}" as="kollege">
                <tr>
                    <td style="color: {color}">{kollege.vorname}</td>
                    <td style="color: {color}">{kollege.stadt}</td>
                </tr>
            </f:for>
        </f:groupedFor>
    </table>
</f:alias>
```

## f:if

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Ermöglicht Wenn-Dann-Sonst-Abfragen.

| Parameter | Erklärung |
|-----------|-----------|
| condition | Hier kommt die Vergleichsabfrage rein (Siehe Beispiele) |

Folgende Vergleiche sind erlaubt: `==`, `!=`, `<`, `<=`, `>`, `>=` und `%`.

Ab TYPO3 8.0 können auch komplexere Operatoren wie `&&` und `||` inkl. Gruppierungen mittels Klammern verwendet werden.

Verglichen werden können nur Variablen folgenden Typs: Zahlen, Objekteigenschaften, Arrays und Ergebnisse aus ViewHelpern.

### Einfache Beispiele

```html
<f:alias map="{wert1: 1, wert5: 5}">
    <f:if condition="{wert1}==1">
        <p>Der Wert ist 1</p>
    </f:if>
    <f:if condition="{wert5}==5">
        <f:then>
            <p>Der Wert ist 5</p>
        </f:then>
        <f:else>
            <p>Der Wert ist NICHT 5</p>
        </f:else>
    </f:if>
    <f:if condition="{wert5} % 2">
        <f:then>
            <p>Die Berechnung liefert einen Restwert von 1.</p>
        </f:then>
        <f:else>
            <p>Es konnte kein Restwert ermittelt werden</p>
        </f:else>
    </f:if>
    <f:if condition="{wert1}!={wert5}">
        <p>wert1 ist NICHT gleich wert5</p>
    </f:if>
    <f:if condition="{wert1}<{wert5}">
        <p>wert1 ist kleiner als wert5</p>
    </f:if>
    <f:if condition="{0:wert1,1:wert5}=={0:1,1:5}">
        <p>Der erste Array ist gleich mit allen Werten aus dem zweiten Zahlenarray</p>
    </f:if>
</f:alias>

<f:alias map="{elemente: {0: wert1, 1: wert2}}">
    <f:if condition="{elemente -> f:count()==2">
        <p>Vergleich mit ViewHelpern: Das Elementearray beinhaltet 2 Elemente</p>
    </f:if>
</f:alias>

<f:alias map="{wert1: 'hallo'}">
    <f:if condition="{0: wert1} == {0: 'hallo'}">
        <p>Stringvergleiche klappen nur als Array</p>
    </f:if>
</f:alias>
```

Wenn kein `f:then` oder `f:else` ViewHelper gefunden wurde, dann wird der Inhalt zwischen den `f:if` ViewHelpern immer nur dann ausgegeben, wenn die Bedingung wahr ist. Wenn komplette WENN->DANN->SONST-Konstrukte erzeugt werden müssen, dann müssen auch immer die `f:then` und `f:else` ViewHelper verwendet werden.

## f:image

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Ich war total begeistert, als ich von diesem ViewHelper das erstmal gelesen habe. Die Arbeit, die man sich normalerweise umständlich in einer Extension oder im TS machen musste gibt es nun fertig als ViewHelper und die Bilder werden nicht einfach nur verkleinert dargestellt. Nein! Sie werden mithilfe von PHP-GD und imagemagick auf die hier angegebene Größe verkleinert. Eine geniale Erfindung, die ich nicht mehr missen möchte.

| Parameter | Erklärung |
|-----------|-----------|
| src | - Pfad zu der Datei. Prefix `EXT:` möglich.<br>- Kombinierter Identifier wie `2:uploads/tx_news/hund.png`. Wobei die Zahl die UID des Storages angibt.<br>- UID aus der Tabelle sys_file<br>- Wenn `treatIdAsReference` aktiviert wurde, dann gilt nicht mehr die UID aus der Tabelle `sys_file`, sondern die UID aus der Tabelle `sys_file_reference`. |
| alt | Alternativtext für das Bild, falls es nicht angezeigt wird |
| width | Breite des Bildes. Hier kann z.B. mit einem angehängtem `c` gesagt werden, dass das Bild, falls die Proportionen nicht genau passen, abgeschnitten wird. Z.B. 200c |
| height | Höhe des Bildes. Hier kann z.B. mit einem angehängtem `c` gesagt werden, dass das Bild, falls die Proportionen nicht genau passen, abgeschnitten wird. Z.B. 100c |
| minWidth | Auch wenn die Bilder im Original evtl. kleiner sind wie dieser Wert hier, so werden diese Bilder auf die hier angegebene Breite gezoomt. |
| minHeight | Auch wenn die Bilder im Original evtl. kleiner sind wie dieser Wert hier, so werden diese Bilder auf die hier angegebene Höhe gezoomt. |
| maxWidth | Auch wenn die Bilder im Original evtl. größer sind wie dieser Wert hier, so werden diese Bilder auf die hier angegebene Breite verkleinert. |
| maxHeight | Auch wenn die Bilder im Original evtl. größer sind wie dieser Wert hier, so werden diese Bilder auf die hier angegebene Höhe verkleinert. |
| treatIdAsReference | {{% badge style="green" icon="angle-double-up" %}}TYPO3 6.0{{% /badge %}} Verwende die UID beim `src` Parameter für die Tabelle `sys_file_reference` und nicht mehr für Tabelle `sys_file`. |
| image | {{% badge style="green" icon="angle-double-up" %}}TYPO3 6.2{{% /badge %}} Anstatt dem `src` Parameter könnt ihr hier das komplette Bildobjekt von FAL übergeben (FileInterface/AbstractFileFolder). `src` und `image` sind eine entweder-oder-Sache. Ihr könnt nicht beide gleichzeitig verwenden. |
| crop | {{% badge style="green" icon="angle-double-up" %}}TYPO3 7.3{{% /badge %}} Hiermit könnt Ihr das in sys_file_reference hinterlegte Cropping überschreiben. Erwartet wird ein Text wie: X,Y,WIDTH,HEIGHT. X = Horizontale Anzahl Pixel von der oberen linken Ecke. Y = Vertikale Anzahl von Pixel der oberen linken Ecke. WIDTH/HEIGHT: Breite und Höhe des Ausschnitts in Pixel. Wenn 0, dann wird das in `sys_file_reference` hinterlegte Cropping komplett deaktiviert. |
| absolute | {{% badge style="green" icon="angle-double-up" %}}TYPO3 7.6{{% /badge %}} Wenn 1, dann wird das Bild mit einem absoluten Pfad gerendert. |

### Beispiel in Originalgröße

```html
<f:image src="fileadmin/bilder/landschaft.jpg" alt="landschaft" />
```

### Beispiel: Beibehaltung der Proportionen

```html
<f:image src="fileadmin/bilder/landschaft.jpg" alt="landschaft" width="50" />
```

### Beispiel: Geschnittenes Bild

```html
<f:image src="fileadmin/bilder/landschaft.jpg" alt="landschaft" width="100c" height="100c" />
```

Die kürzere Seite wird auf 100 Pixel gesetzt und bei der längeren Seite wird nach 100 Pixeln einfach abgeschnitten.

## f:layout

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Doku kommt noch

## f:media

{{% badge style="green" icon="angle-double-up" %}}TYPO3 7.6{{% /badge %}}

| Parameter | Erklärung |
|-----------|-----------|
| file | Hier geht es nicht um eine UID, Dateinamen oder Referenz. Diese Eigenschaft erwartet das komplette Bildobjekt vom Typ FileInterface oder AbstractFileFolder |
| additionalConfig | Eine zusätzliche Konfiguration, die an den Bildrenderer Prozess übergeben wird |
| width | Breite des Bildes. Hier kann z.B. mit einem angehängtem `c` gesagt werden, dass das Bild, falls die Proportionen nicht genau passen geschnitten wird. Z.B. 200c |
| height | Höhe des Bildes. Hier kann z.B. mit einem angehängtem `c` gesagt werden, dass das Bild, falls die Proportionen nicht genau passen geschnitten wird. Z.B. 100c |

## f:render

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Die Partials sind in Fluid wie die FCEs in TemplaVoila. Kurz: Wiederverwendbare Templates. Eine geniale Sache, solange ihr diese ViewHelper in Maßen einsetzt, denn das Laden eines Partials dauert ca. 5 Millisekunden. Wenn ihr also irgendwann mal auf die Idee kommen solltet, jede Zelle einer Tabelle mit Partials generieren zu wollen, dann kann das Laden der Webseite bei 700 Tabellenzeilen und 15 Spalten schonmal etwas dauern: 700 Zeilen * 15 Spalten * 5 Millisekunden = 52500 Millisekunden. Plus die Zeit, die TYPO3 selbst noch braucht sind wir bei knapp einer Minute.

Die Dateien für Partials liegen immer in fest vorgegebenen Verzeichnissen. Innerhalb von Extensions ist dies: `typo3conf/ext/[ExtensionKey]/Resources/Private/Partials/`. Wenn Ihr allerdings mit FLUIDTEMPLATE arbeitet, dann gebt Euren gewünschten Verzeichnispfad mithilfe der TS-Eigenschaft `partialRootPath` mit abschließendem `/` an.

Sections haben kein eigenes Verzeichnis, da diese immer innerhalb der aktuellen Templatedatei definiert werden müssen. Ausnahmen machen da die Layouts.

| Parameter | Erklärung |
|-----------|-----------|
| section | Der Name einer Section, die gerendert werden soll. |
| partial | Pfad + Dateiname ohne .html-Endung ab dem Verzeichnis, das für Partials definiert wurde. |
| arguments | Welche Variablen sollen in den Partial/das Layout übernommen werden. |
| optional | Normalerweise hagelt es Fehlermeldungen, wenn Sections nicht auffindbar sind. Setzt man diesen Parameter aber auf TRUE, dann wird eben nichts ausgegeben. |

### Beispiel für Partial

In unserem Fluidtemplate:

```html
<f:alias map="{mitarbeiter: {0: {vorname: 'Stefan', stadt: 'Lindlar'},1: {vorname: 'Petra', stadt: 'Lindlar'},2: {vorname: 'Sascha', stadt: 'Remscheid'},3: {vorname: 'Patrick', stadt: 'Bonn'},4: {vorname: 'Sven', stadt: 'Gummersbach'},5: {vorname: 'Andrea', stadt: 'Wuppertal'}}}">
    <table cellpadding="5" cellspacing="0" border="2">
        <f:for each="{mitarbeiter}" as="kollege">
            <f:render partial="TableRow" arguments="{kollege: kollege}"/>
        </f:for>
    </table>
</f:alias>
```

In der Partial Datei `TableRow.html`

```html
<tr>
    <td>{kollege.vorname}</td>
    <td>{kollege.stadt}</td>
</tr>
```

Weitere Beispiele folgen

## f:renderChildren

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.5{{% /badge %}}

Dieser ViewHelper findet nur innerhalb der Templates von Fluid Widgets Gebrauch. Gerade beim `f:widget.paginate` ViewHelper wird `f:renderChildren` benötigt, um den Inhalt zwischen dem öffnenden und schließenden `f:widget.pageinate` Tags zu rendern.

| Parameter | Erklärung |
|-----------|-----------|
| arguments | Variablen in ArrayNotation, die an das Unter-Template übergeben werden sollen |

## f:renderFlashMessages

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}
{{% badge style="orange" icon="angle-double-up" %}}TYPO3 4.5{{% /badge %}}
{{% badge style="red" icon="skull-crossbones" %}}TYPO3 4.5{{% /badge %}}

Veraltet. Bitte verwendet nun `f:flashMessages`.

## f:section

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Sections arbeiten ähnlich wie Partials. Während Partials in eine eigene Datei ausgelagert werden und somit innerhalb der Extension von überall aufgerufen werden können, befinden sich Sections innerhalb der Templatedateien und können auch nur von dort und Layouts aufgerufen werden. Mit Hilfe von Sections lassen sich gerade große Templates hervorragend strukturieren.

| Parameter | Erklärung |
|-----------|-----------|
| name | Ein Name unter dem man den Inhalt zwischen den Tags wieder auffinden kann |

### Beispiel

```html
<f:alias map="{mitarbeiter: {0: {vorname: 'Stefan', stadt: 'Lindlar'}, 1: {vorname: 'Petra', stadt: 'Lindlar'}, 2: {vorname: 'Sascha', stadt: 'Remscheid'}, 3: {vorname: 'Patrick', stadt: 'Bonn'}, 4: {vorname: 'Sven', stadt: 'Gummersbach'}, 5: {vorname: 'Andrea', stadt: 'Wuppertal'}}}">
    <f:for each="{mitarbeiter}" as="kollege">
      <f:render section="TableRow" arguments="{kollege: kollege}"/>
    </f:for>
</f:alias>

<f:section name="TableRow">
    {kollege.vorname}
    {kollege.stadt}
</f:section>
```

## f:spaceless

{{% badge style="green" icon="angle-double-up" %}}TYPO3 7.6{{% /badge %}}

Jedes Enter, jede Einrückung und jedes Leerzeichen in Euren Fluidtemplates werden 1zu1 im Seiten Quelltext Eurer Webseite ausgegeben. Zwar reduziert der Browser überflüssigen Leerraum bei der Ausgabe auf ein Leerzeichen zusammen, aber trotzdem muss jedes Leerzeichen zum Browser übertragen werden und kostet somit zusätzlichen Traffic. Mit diesem ViewHelper können die überflüssigen Leerräume schon vor der Ausgabe entfernt werden.

Im folgenden Beispiel würde der HTML-Quelltext als eine lange Zeile zum Browser übertragen werden.

Es wird nur Leerraum zwischen den HTML-Tags entfernt. Nicht jedoch der Leerraum im Fließtext.

### Beispiel

```html
<f:spaceless>



        Hallo



</f:spaceless>
```

## f:switch

{{% badge style="green" icon="angle-double-up" %}}TYPO3 6.2{{% /badge %}}

Mit dem f:switch ViewHelper könnt Ihr gleich mehrere Varianten eines Wertes überprüfen. Auf die Frage "Was bist Du?" könnte die Antwort Vater, Mutter, Tochter oder Sohn erfolgen. Diese 4 Antwortmöglichkeiten mittels dem `f:if` ViewHelper zu realisieren wäre schon sehr komplex und schwer zu warten. Mit jeder weiteren Variante würde der Quelltext ein bisschen weiter nach rechts rutschen. Nach ein paar Monaten weiß kein Mensch mehr, was da eigentlich gerade passiert.

| Parameter | Erklärung |
|-----------|-----------|
| expression | Ein string/int/bool Wert, auf den die enthaltenen f:case ViewHelper reagieren sollen. |

### Beispiel

```html
<f:switch expression="{whoAreYou}">
    <f:case value="Vater">Ich brauch Taschengeld</f:case>
    <f:case value="Mutter">Wann gibt's Mittach?</f:case>
    <f:case value="Tochter">Wo ist mein Lippenstift?</f:case>
    <f:case value="Sohn">Ich geh scaten</f:case>
    <f:case default="1">Stell Dich vor</f:case>
</f:switch>
```

## f:then

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Da dieser ViewHelper nur innerhalb des `f:if` ViewHelpers verwendet werden kann, erkläre ich diesen dort.

## f:translate

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.3{{% /badge %}}

Mit dem `f:translate` ViewHelper greift ihr auf eine beliebige Sprachdatei (standardmäßig `locallang.xlf`) zu und holt euch die entsprechende Übersetzung mithilfe der Angabe im Parameter `key`.

Innerhalb von Extensions wird immer auf die `locallang.xlf` im Verzeichnis `Resources/Private/Language/` zugegriffen. Im Bereich FLUIDTEMPLATE müsste ihr hier die Pfadsyntax verwenden:

`LLL:fileadmin/templates/locallang.xml:domain_model_irgendwas.titel`

bzw:

`LLL:EXT:meineExtension/Resources/Private/Language/locallang.xml:domain_model_irgendwas.titel`

Der `LLL:` Prefix muss dem Pfad immer vorangestellt sein! Etwas einfacher machen es sich die FLUIDTEMPLATE-User, wenn sie im TypoScript zuvor angeben auf welche `locallang.xlf` welcher Extension sie zugreifen wollen:

```typo3_typoscript
extbase.pluginName = Pi1
extbase.controllerExtensionName = MeineExtension
```

Dann reicht es auch wieder nur den `key` anzugeben ohne den ganzen Pfad.

| Parameter | Erklärung |
|-----------|-----------|
| key | Der key, mit dem man die Übersetzung aus dem Sprachdateien auslesen kann |
| default | Wenn der Key in der Sprachdatei nicht gefunden werden kann, dann verwende diesen Text. Wenn default nicht gesetzt ist, wird der Inhalt zwischen den Tags verwendet. |
| htmlEscape | Alle Übersetzungen aus den Sprachdateien werden durch htmlspecialchars geschleust, was es unmöglich macht, HTML-Tags in den Übersetzungen anzeigen zu lassen. Setzt diesen Wert auf FALSE, um dieses Vorgehen zu unterbinden. |
| arguments | In den Übersetzungen können Platzhalter definiert werden, die dann mit den Inhalten diesen Arrays gefüllt werden. |

### Beispiel

```html
<f:translate key="domain_model.title" htmlEscape="false" />
```

### Beispiel mit Pfad

```html
<f:translate key="LLL:fileadmin/lang/locallang.xml:domain_model.title" />
```

### Beispiel mit Platzhaltern

In unserem Template:

```html
<f:translate key="LLL:fileadmin/lang/locallang.xml:domain_model.title" arguments="{0: 'Herr der Ringe'}" />
```

In der `locallang.xlf`:

```xml
<label index="domain_model.title">Title of: %s</label>
```

Mit %s wird auf den ersten Wert des übergebenen Arrays zugegriffen. Kommt %s nochmals vor, dann wird auf den zweiten Arrayeintrag zugegriffen. Um das unabhängig von der Reihenfolge zu machen, empfehle ich noch folgende Notation:

```xml
<label index="domain_model.title">Titel von: %1$s</label>
```

Mit %1 greift ihr auf den ersten Eintrag zu und sagt diesem, dass er als String/Text interpretiert werden soll ($s). Auf php.net findet Ihr heraus wofür diese ganzen Kürzel stehen.
