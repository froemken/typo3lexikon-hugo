+++
title = "Uri"
date = 2024-01-09T20:27:50+01:00
aliases = ["uri.html"]
+++

## f:uri.action

Dieser ViewHelper erzeugt nur die URI `https://...` zu einer Seite oder Extbase Action. Es wird kein `<a>` Tag erstellt.

| Parameter | Erklärung |
|-----------|-----------|
| action | Auf welche Actionmethode soll der Link zeigen |
| arguments | Welche Argumente/Parameter sollen dem Link angehängt werden |
| controller | Auf welchen Controller soll der Link zeigen |
| extensionName | Auf welchen Controller und/oder Action welcher Extension soll der Link zeigen |
| pluginName | Auf welchen Controller und/oder Action welchen Plugins soll der Link zeigen |
| pageUid | Auf welche Seiten-UID soll verlinkt werden |
| pageType | Auf welche Seitentyp ID soll verlinkt werden. |
| noCache | Verhindert das Caching der aufzurufenden Seite |
| noCacheHash | Verhindert, dass der cHash-Parameter nicht mit an die URL angehangen wird. |
| section | Auf welchen Anker soll die Zielseite springen (#anker) |
| format | Gibt an um welches Format es sich bei der Zielseite handelt. Standard: `html` |
| linkAccessRestrictedPages | Normalerweise werden Links auf geschützte Seiten nicht erzeugt. Hier mit kann man die Link-Generation erzwingen. Standard: deaktiviert |
| additionalParams | Fügt weitere Parameter der Zielseite an. Im Gegensatz zu `arguments`, können hiermit Variablen hinzugefügt werden, die nicht mit dem Extensionnamen vorangestellt werden. |
| absolute | Nach Aktivierung wird der Zielseite noch der Domainname und Pfad vorangestellt. Standard: deaktiviert |
| addQueryString | Falls der aktuellen Seite bereits Parameter über die URL mitgegeben wurden, könnt ihr hier nun entscheiden, ob diese Parameter auch mit auf die Zielseite übergeben werden. |
| argumentsToBeExcludedFromQueryString | Falls ihr `addQueryString` aktiviert habt, aber einen oder zwei bestimmte Parameter wieder entfernen wollt, dann tragt ihr hier diese Parameter ein. |

### Beispiel

```html
<f:uri.action action="show">Zeige Details</f:uri.action>
```

## f:uri.email

Ein ViewHelper um eine URL für eine E-Mail-Adresse zu erzeugen.

| Parameter | Erklärung |
|-----------|-----------|
| email | Die zu verlinkende E-Mail-Adresse |

### Beispiel

```html
<f:uri.email email="meine@mailadresse.tld"/>
```

## f:uri.external

Nutzt diesen ViewHelper, um eine URL zu einer externe Seiten zu erzeugen. Wenn ihr bei `uri` eine vollständige URI angebt, also inkl. `https://` oder `ftps://`, dann braucht ihr `defaultScheme` nicht zu setzen. `defaultScheme` ist nur dafür da, falls ihr lieber einfach nur `www.sfroemken.de` als `uri` eingeben wollt. Dann merkt dieser ViewHelper, dass ihm was fehlt und ergänzt diese URL mit dem Standard-Schema `http://`.

| Parameter | Erklärung |
|-----------|-----------|
| uri | Die URL zu der externen Zielseite |
| defaultScheme | Hier sind alle gültigen Schemas für Links erlaubt wie z.B. `ftp` oder `https`. Standard: `http` |

### Beispiel

```html
<f:uri.external uri="www.sfroemken.de">Externer Link zu meiner Seite</f:uri.external>
```

## f:uri.image

Dieser ViewHelper erzeugt nur den relativen Pfad zu einem Bild. Wenn die Größe nicht verändert wird, erhält man den direkten Pfad zu dem Bild, sei er in einer Extension oder im `fileadmin`. Verändert man allerdings die Größe, dann wird das Bild entsprechend gerendert und in dem Unterordner `_processed_` abgelegt. Dementsprechend ändert sich also auch der relative Pfad.

| Parameter | Erklärung |
|-----------|-----------|
| src | Pfad zu der Datei. Hier kann auch mit `EXT:` gearbeitet werden, da es sich hier um ein `IMG_RESOURCE` handelt |
| alt | Alternativtext für das Bild, falls im Browser die Bilddarstellung deaktiviert wurde |
| width | Breite des Bildes. Hier kann z.B. mit einem angehängtem `c` gesagt werden, dass das Bild, falls die Proportionen nicht genau passen geschnitten wird. Z.B. 200c |
| height | Höhe des Bildes. Hier kann z.B. mit einem angehängtem `c` gesagt werden, dass das Bild, falls die Proportionen nicht genau passen geschnitten wird. Z.B. 100c |
| minWidth | Auch wenn die Bilder im Original evtl. kleiner sind wie dieser Wert hier, so werden diese Bilder auf die hier angegebene Breite gezoomt. |
| minHeight | Auch wenn die Bilder im Original evtl. kleiner sind wie dieser Wert hier, so werden diese Bilder auf die hier angegebene Höhe gezoomt. |
| maxWidth | Auch wenn die Bilder im Original evtl. größer sind wie dieser Wert hier, so werden diese Bilder auf die hier angegebene Breite verkleinert. |
| maxHeight | Auch wenn die Bilder im Original evtl. größer sind wie dieser Wert hier, so werden diese Bilder auf die hier angegebene Höhe verkleinert. |

### Beispiel mit Originalgröße

```html
<f:uri.image src="fileadmin/bilder/landschaft.jpg" alt="landschaft" />
```

### Beispiel: Beibehaltung der Proportionen

```html
<f:uri.image src="fileadmin/bilder/landschaft.jpg" alt="landschaft" width="50" />
```

### Beispiel: Geschnittenes Bild

```html
<f:uri.image src="fileadmin/bilder/landschaft.jpg" alt="landschaft" width="100c" height="100c" />
```

Die kürzere Seite wird auf 100 Pixel gesetzt und bei der längeren Seite wird nach 100 Pixeln einfach abgeschnitten.

## f:uri.page

Mit diesem ViewHelper erstellt ihr eine URL, die auf eine andere Seite verweist.

| Parameter | Erklärung |
|-----------|-----------|
| pageUid | Auf welche Seiten-UID soll verlinkt werden |
| additionalParams | Fügt weitere Parameter der Zielseite an. Im Gegensatz zu arguments, können hiermit Variablen hinzugefügt werden die nicht mit dem Extensionnamen geprefixed werden. |
| pageType | Auf welche Seitentyp ID soll verlinkt werden. |
| noCache | Verhindert das Caching der aufzurufenden Seite |
| noCacheHash | Verhindert, dass der cHash-Parameter nicht mit an die URL angehangen wird. |
| section | Auf welchen Anker soll die Zielseite springen (#anker) |
| linkAccessRestrictedPages | Normalerweise werden Links auf geschützte Seiten nicht erzeugt. Hier mit kann man die Linkgeneration erzwingen. Standard: deaktiviert |
| absolute | Nach Aktivierung wird der Zeilseite noch der Domainname und Pfad vorangestellt. Standard: deaktiviert |
| addQueryString | Falls der aktuellen Seite bereits Parameter über die URL mitgegeben wurden, könnt Ihr hier nun entscheiden, ob diese Parameter auch mit auf die Zielseite übergeben werden. |
| argumentsToBeExcludedFromQueryString | Falls ihr `addQueryString` aktiviert habt, aber einen oder zwei bestimmte Parameter wieder entfernen wollt, dann tragt Ihr hier diese Parameter ein. |

### Beispiel

```html
<f:uri.page pageUid="134">Linkname für die Seite 134</f:uri.page>
```
