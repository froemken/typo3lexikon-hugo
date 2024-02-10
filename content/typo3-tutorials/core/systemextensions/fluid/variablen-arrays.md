+++
title = "Variablen und Arrays"
date = 2024-01-09T20:26:35+01:00
aliases = ["fluid-variablen-und-arrays", "fluid-variablen-und-arrays.html"]
+++

Arrays sind in Fluid ein zentraler Bestandteil. Sobald Ihr Daten an Partials übergeben oder mehrere Parameter an eine URL anhängen müsst, dann werdet Ihr um Arrays nicht mehr herumkommen.

Innerhalb von Fluid werden Arrays immer mit einer geschweiften Klammer begonnen und beendet. Werte auf die Ihr später zurückgreifen wollt stehen immer rechts und die Namen für Eure Werte, die sogenannten keys (zu Deutsch Schlüssel) stehen immer links. Werte und Keys werden immer durch einen Doppelpunkt (:) getrennt. Ein einfaches Array innerhalb von Fuid schaut zum Beispiel so aus:

```html
{alter: 33}
```

Wenn wir uns diese eine Zeile mal im Frontend betrachten, werden wir das Wort "Array" sehen. Toll. Jetzt haben wir ein Array, sehen aber den Inhalt nicht. Um Arrayinhalte sichtbar zu machen, gibt es den f:debug-ViewHelper. Schauen wir uns nun also mal den Inhalt unseres Arrays an:

```html
<f:debug title="Alter">{alter: 33}</f:debug>
```

In der tabellarischen Debugausgabe sehen wir unseren Key in der linken und den enthaltenen Wert in der rechten Tabellenspalte.

## Arrays dem Template zur Verfügung stellen

Damit wir nun von überall aus unserem Template auf unseren Wert (33) zugreifen können, müssen wir diesen für unser Template öffentlich zugänglich machen. Das macht Ihr entweder schon direkt in Eurer Extension im sogenannten Controller mit der Methode $this->view->assign('alter', 33); oder aber innerhalb von Fluid mit dem f:alias-ViewHelper:

```html
<f:alias map="{alter: 33}">
    <f:debug title="Alter">{alter}</f:debug>
    <p>Ich bin {alter} Jahre alt.</p>
</f:alias>
<p>Auch ich bin {alter} Jahre alt.</p>
```

Auf Werte eines Arrays können wir zugreifen, wenn wir den Key des gewünschten Wertes in geschweiften Klammern setzen. Im oberen Beispiel wollen wir auf die 33 zugreifen. Demnach setzen wir den Key "alter" in geschweifte Klammern {alter} und erhalten im Frontend überall dort, wo wir diesen Marker verwenden unseren Wert 33.

## Das Problem mit den Verschachtelungen

Wer das obere Beispiel ausprobiert hat, wird evtl. festgestellt haben, dass die letzte Zeile nicht das gewünschte Ergebnis liefert. Während mit $this->view->assign() erzeugte Variablen im ganzen Template zur Verfügung stehen, stehen Variablen, die mit ViewHelpern erzeugt wurden nur zwischen dem öffnenden und schließendem Tag zur Verfügung. Naja...sofern man sich innerhalb der ViewHelper-Programmierung an gewisse Standards gehalten hat, wovon wir bei TYPO3-eigenen ViewHelpern wohl ausgehen können :-) Auf jeden Fall steht die letzte Zeile außerhalb des schließenden Tags des f:alias-ViewHelpers und deshalb wird die 33 nicht angezeigt.

## Mehrere Elemente in einem Array

Mehrere Variablenzuordnungen innerhalb eines Arrays werden mit einem Komma realisiert. Hier ein Beispiel:

```html
<f:alias map="{alter: 33, plz: 23456, hausnummer: 84}">
    <p>Ich bin {alter} Jahre alt.</p>
    <p>Meine PLZ ist: {plz}.</p>
    <p>Ich wohne in der Milchstrasse {hausnummer}.</p>
</f:alias>
```

## Erlaubte Datentypen innerhalb von Arrays

Erlaubt sind folgende Datentypen: Texte, Zahlen, Arrays, ViewHelper und Objekte

Der Datentyp Objekt kann nur über eine Extension im Template zur Verfügung gestellt werden. Außerdem sind nicht alle Objekte erlaubt. Es können nur bestimmte Objekte verwendet werden:

- Objekte, die unser Domainmodell wiederspiegeln
- Objekte wie das ObjectStorage, das eine Anzahl X von Domainmodellobjekten enthalten kann.
- Objekte vom Typ Tx_Extbase_Persistence_ResultInterface, die Domainmodelle, die durch eine Datenbankabfrage erzeugt wurden, enthalten.

Die letzten beiden Typen sind auch gleichzeitig vom Typ ArrayIterator und können zum Beispiel mit f:for-ViewHelpern durchlaufen werden.

## Arrays mit Texten

```html
<f:alias map="{firstName: 'Stefan', lastName: 'Frömken'}">
    <p>Ich heiße: {firstName} {lastName}.</p>
</f:alias>
```

Strings bzw. Texte müssen in Anführungszeichen gesetzt werden. Üblicherweise werden hier einfache Anführungszeichen verwendet. Ich selbst habe es auch mal anders herum ausprobiert und habe die einfachen Anführungszeichen nach außen gesetzt und die doppelten Anführungszeichen für die Texte verwendet. Es funktioniert, aber das ist sehr untypisch und ich kann nicht sagen, ob diese Variante auch in Zukunft so noch funktionieren würde. Von daher haltet Euch lieber an das Beispiel wie oben.

## Einfache Anführungszeichen im Text

```html
<f:alias map="{firstName: 'Stefan', lastName: 'Frömken', satz: '8 o\'clock'}">
    <p>Ich heiße: {firstName} {lastName}.</p>
    <p>We meet us at {satz}.</p>
</f:alias>
```

Einfache Anführungszeichen im Text müssen mit Backslash (\) maskiert werden. So ein Fall wie hier im Beispiel wird es wohl eher selten geben. Interessanter ist das Thema bei der Linkgenerierung, bei denen Datumswerte oder Zeitstempel der URL angehangen werden müssen:

```html
<f:link.page pageUid="2" additionalParams="{datum: '{f:format.date(date:\'\', format:\'U\')}'}">Link</f:link.page>
```

Schaut krank aus, oder? Naja...hat den Vorteil, dass Ihr jetzt auch wisst, wie man ViewHelper in Arrays integriert.

## Array in Array

Oder auch Objekte in Arrays, wenn diese über $this->view->assign() in das Template übergeben wurden. Diese Technik ist ein entscheidender Vorteil vieler anderer Templatemaschinen. Denn mit der Punktsyntax kann ich mit immer tiefer durch die Arrays und Objekte hangeln. Hier mal ein Beispiel:

```html
<f:alias map="{user: {name: 'Stefan Frömken',strasse: 'Müllerweg 31'}}">
    <f:debug title="Benutzer">{user: {name: 'Stefan Frömken',strasse: 'Müllerweg 31'}}</f:debug>

    <p>Ich heiße {user.name} und wohne im {user.strasse}</p>
    
    <ul>
        <f:for each="{user}" as="wert" key="key">
            <li>{key} => {wert}</li>
        </f:for>
    </ul>
</f:alias>
```

Im f:alias-ViewHelper definieren wir ein sogenanntes Multidimensionales Array. Kurz: Ein Array, dass ein Array enthält. Der erste Key heißt "user" und enthält ein Array mit den beiden Keys name und strasse. Um nun auf die Strasse zugreifen zu können verwenden wir die Punktsyntax zwischen den Arraykeys. Wie das aussieht seht Ihr in der Zeile mit "Ich heiße". Zur verdeutlichung habe ich Euch das komplette Array nochmals mit dem f:debug-ViewHelper ausgeben lassen.

Arrays lassen sich auch durchlaufen wie Ihr im f:for-ViewHelper erkennen könnt.
