+++
title = "Validator"
date = 2024-01-09T20:23:17+01:00
aliases = ["validator.html"]
+++

Dank Extbase braucht ihr nicht mehr für jedes Feld eine Überprüfungsfunktion schreiben, sondern könnt auf vorgegebene Überprüfungstools von Extbase zugreifen. Diese werden direkt in den Kommentaren zur jeweiligen Objekteigenschaft angegeben.

Für die Dateien im Domain/Model-Verzeichnis schaut es folgendermaßen aus:

```php
/**
 * The blog's title.
 *
 * @var string
 * @validate StringLength(minimum = 1, maximum = 80)
 */
protected $title = '';
```

Mehrere Validators werden kommasepariert angegeben.

Folgende Validators sind bereits verfügbar:

## AbstractComposite

Noch nicht getestet

## AbstractObject

Noch nicht getestet

## Abstract

Noch nicht getestet

## Alphanumeric

Gibt TRUE zurück, wenn der übergebene Wert ein String ist (is_string) UND, ob nur die Buchstaben A-Z und die Zahlen 0-9 enthalten sind. Sonderzeichen wie äöü geben ein false zurück.

## Conjunction

Ist nicht direkt ein echter Validator. Conjunction gibt TRUE zurück wenn bei Angabe von mehreren Validators ALLE Validators ein TRUE zurückgegeben haben (AND).

## DateTime

Gibt TRUE zurück, wenn der übergebene Wert einen gültigen Datums und Zeitwert enthält. Überprüft wird mit "instanceof('DateTime')

## Disjunction

Ist nicht direkt ein echter Validator. Disjunction gibt TRUE zurück wenn bei Angabe von mehreren Validators EIN Validator TRUE zurückgegeben haben (OR).

## EmailAddress

Gibt TRUE zurück, wenn der übergebene Wert eine gültige E-Mail-Adresse ist. Allerdings werden Sonderzeichen wie äöü, wie sie hier in Deutschland auch gültig sind NICHT überprüft. Solche E-Mail-Adressen werden dann als ungültig zurückgegeben.

## Float

Gibt TRUE zurück, wenn der übergebene Wert vom Typ Float ist. Gibt auch TRUE zurück, wenn der Wert String ist, aber wie Float aufgebaut ist: preg_match('/^[0-9.e+-]+$/', $value)

## GenericObject

Noch nicht getestet

## Integer

Gibt TRUE zurück, wenn der übergebene Wert vom Typ Integer ist.

## NotEmpty

Gibt TRUE zurück, wenn der übergebene Wert nicht leer ist. Wenn FALSE, dann wird nochmals differenziert zwischen '' und NULL.

## NumberRange

Gibt TRUE zurück, wenn der übergebene Wert eine Zahl ist UND zwischen den angegebenen Werten liegt.

## Number

Gibt TRUE zurück, wenn der übergebene Wert numerisch ist (PHP: is_numeric)

## Raw

Gibt TRUE zurück. Kein Wenn und kein Aber. Wird die Funktion zum Überprüfen aufgerufen wird sofort ein TRUE zurückgegeben. Hab keine Ahnung wann und wofür man das brauchen könnte.

## RegularExpression

Gibt TRUE zurück, wenn der übergebene Wert eine regular Expression enthält UND diese mit dem Value übereinstimmt

## StringLength

Gibt TRUE zurück, wenn der übergebene Wert von der Länge her zwischen die beiden Längenangaben passt. Gibt FALSE zurück, wenn die maximum-Angabe kleiner ist als das Minimum.

## String

Gibt TRUE zurück, wenn der übergebene Wert vom Typ String ist (PHP: is_string)

## Text

Gibt TRUE zurück, wenn der übergebene Wert ein Text ist. Gibt FALSE zurück, wenn der Text XML-Tags enthält.
