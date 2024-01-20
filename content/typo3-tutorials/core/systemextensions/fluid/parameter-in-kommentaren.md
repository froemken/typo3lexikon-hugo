+++
title = "Parameter in Kommentaren"
date = 2024-01-09T20:25:39+01:00
aliases = ["parameter-in-kommentaren.html"]
+++

Sowas habe ich noch nicht erlebt. In Extbase können, und teilweise müssen bestimmte Parameter bzgl.  Eigenschaften, Methoden und Klassen in den jeweiligen Kommentaren hinterlegt sein.

So gibt es z.B. solche Fehlermeldung

{{% notice style="note" title="Error" icon="skull-crossbones" %}}
#1251730702: The value must be of type "StefanFroemken\Sffluid\Domain\Model\Customer", but was of type "array".
{{% /notice %}}

wenn bestimmte Eigenschaften nicht sauber mit Parametern im Kommentar deklariert wurden.

## Klassen Parameter:

### @scope (singleton, prototype, session)

`singleton` ist der Defaultwert und erlaubt nur die einmalige Instanziierung einer Klasse während `prototype` eine mehrmalige Instanziierung erlaubt. `session` ist mir noch unbekannt, aber in einem Kommentar habe ich mal was von manuell gelesen. Evtl. muss hier die Klasse manuell instanziiert werden, aber das will ich hier nicht 100%ig bestätigen.

### @entity

Definiert, dass diese Klasse eine Einheit nach dem Prinzip des DDD ist und dass sie dauerhaft bestehen kann. Solche Einheiten bringen Ihren eigenen Identifier mit und können so auch bei gleichem Inhalt differenziert werden.

### @copyright und @license

Hier weiß ich nicht, ob die wirklich noch durch irgendwelche Funktionen von Extbase fließen oder nicht. Derzeit glaube ich, dass es ganz einfache Kommentare sind.

### @version $Id:$

Ich habe immer wieder gesehen, dass `@version` in Verbindung mit diesem `$Id:$` gebracht wird. Auch hier bin ich mir nicht 100%ig sicher, aber entweder wird hier jeder gefundenen Klasse eine eindeutige ID zugewiesen, oder jedem Objekt dieser Klasse wird eine eindeutige ID zugewiesen.

## Eigenschaften Parameter

### @var (string, integer oder sogar ein komplettes Objekt (Domain/Model))

Gibt an, welchen Typs die Eigenschaft an.

### @identity

Gibt an, dass diese Eigenschaft der Identifier ist. Sollte in Verbindung mit dem Parameter entity der Klassenparameter genutzt werden.

### @validate (StringLength, Text, Float, ...)

Hier findet ihr eine [detaillierte Auflistung](https://www.typo3lexikon.de/typo3-tutorials/core/systemextensions/extbase/validators.html)

## Methoden Parameter

### @dontvalidate (Variable)

Dieser Parameter wurde implementiert, um Schleifen zwischen editAction und updateAction zu vermeiden. Ohne diesen Parameter würden sich die beiden Methoden ständig gegenseitig aufrufen, wenn Validate-Parameter angegeben sind. Dieser Parameter sollte demnach immer bei den editActions mit angegeben werden

## @param und @return

Hier weiß ich nicht, ob die wirklich noch durch irgendwelche Funktionen von Extbase fließen oder nicht. Derzeit glaube ich, dass es ganz einfache Kommentare sind.
