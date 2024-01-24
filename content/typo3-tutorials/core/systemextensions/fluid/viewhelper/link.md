+++
title = "Link"
date = 2024-01-09T20:27:38+01:00
aliases = ["link.html"]
+++

## f:link.action

Dieser ViewHelper wird überwiegend innerhalb von Extension-Templates verwendet, aber es ist auch denkbar von einem `FLUIDTEMPLATE` basierten Template auf ein Extensionbasiertes Template zu verlinken. Dann aber müsst ihr alle Parameter bzgl. Extensionname, Plugin, Controller und Action angeben.

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
<f:link.action action="show">Zeige Details</f:link.action>
```

## f:link.email

Ein ViewHelper um eine E-Mail-Adresse zu verlinken

| Parameter | Erklärung |
|-----------|-----------|
| email | Die zu verlinkende E-Mail-Adresse |

### Beispiel

```html
<f:link.email email="meine@mailadresse.tld" />
```

## f:link.external

Nutzt diesen ViewHelper, um auf externe Seiten zu verweisen. Wenn ihr bei `uri` einen vollständigen Link angebt, also inkl. `http://` oder `ftp://`, dann braucht ihr `defaultScheme` nicht zu setzen. `defaultScheme` ist nur dafür da, falls ihr lieber einfach nur `www.sfroemken.de` als `uri` eingeben wollt. Dann merkt dieser ViewHelper, dass ihm was fehlt und ergänzt diese URL mit dem Standard-Schema `http://`.

| Parameter | Erklärung |
|-----------|-----------|
| uri | Die URL zu der externen Zielseite |
| defaultScheme | Hier sind alle gültigen Schemas für Links erlaubt wie z.B. `ftp` oder `https`. Standard: `http` |

### Beispiel

```html
<f:link.external uri="www.sfroemken.de">Externer Link zu meiner Seite</f:link.external>
```

## f:link.page

Mit diesem ViewHelper erstellt Ihr einen Link, der auf eine andere Seite verweist.

| Parameter | Erklärung |
|-----------|-----------|
| pageUid | Auf welche Seiten-UID soll verlinkt werden |
| additionalParams | Fügt weitere Parameter der Zielseite an. Im Gegensatz zu arguments, können hiermit Variablen hinzugefügt werden, die nicht mit dem Extensionnamen vorangestellt werden. |
| pageType | Auf welche Seitentyp ID soll verlinkt werden. |
| noCache | Verhindert das Caching der aufzurufenden Seite |
| noCacheHash | Verhindert, dass der cHash-Parameter nicht mit an die URL angehangen wird. |
| section | Auf welchen Anker soll die Zielseite springen (#anker) |
| linkAccessRestrictedPages | Normalerweise werden Links auf geschützte Seiten nicht erzeugt. Hier mit kann man die Link-Generation erzwingen. Standard: deaktiviert |
| absolute | Nach Aktivierung wird der Zielseite noch der Domainname und Pfad vorangestellt. Standard: deaktiviert |
| addQueryString | Falls der aktuellen Seite bereits Parameter über die URL mitgegeben wurden, könnt ihr hier nun entscheiden, ob diese Parameter auch mit auf die Zielseite übergeben werden. |
| argumentsToBeExcludedFromQueryString | Falls ihr `addQueryString` aktiviert habt, aber einen oder zwei bestimmte Parameter wieder entfernen wollt, dann tragt ihr hier diese Parameter ein. |

### Beispiel

```html
<f:link.page pageUid="134">Linkname für die Seite 134</f:link.page>
```
