+++
title = "MathUtility"
date = 2024-01-09T20:17:34+01:00
slug = "MathUtility"
aliases = ["mathutility.html"]
+++

## forceIntegerInRange

Prüft, ob ein gegebener Zahlenwert sich in einem Bereich von bis befindet. Falls nicht, dann wird die Zahl auf die Werte Min oder Max angepasst.

Parameter
: **theInt**: Die Zahl, die geprüft werden soll
: **min**: Wie klein darf theInt sein? Wenn die Zahl kleiner ist als min, dann wird theInt auf den Wert von min gesetzt und als Ergebnis zurückgeliefert.
: **max**: Wie groß darf theInt sein? TYPO3 gibt hier einen Defaultwert von 2000000000 an. Diesen solltet Ihr nach Möglichkeit nicht übersteigen. Auf 32bit-Systemen können Int-Werte bis zu 4294967296 Zahlen darstellen, jedoch vergessen viele das Vorzeichen-Bit, weshalb die größtmögliche Zahl 2147483648 ist. Die 2000000000 steht dort also nur zur besseren Lesbarkeit. Wenn eine Zahl größer als max ist, wird theInt auf den Wert von max gesetzt und zurückgeliefert.
: **defaultValue**: Falls die Konvertierung von theInt fehlschlägt, könnt Ihr mit defaultValue einen Wert angeben, der an theInt weitergegeben und zurückgeliefert wird.

## convertToPositiveInteger

Ich empfinde diesen Methodennamen sehr verwirrend, denn negative Werte werden nicht in die gleiche positive Zahl konvertiert, sondern einfach auf 0 gesetzt. -234 wird also zu 0, während 234 bei 234 bleibt. Eine sinnvolle Anwendung könnte z.B. die Verarbeitung der Seiten-UIDs sein. Somit würden negative Seiten immer auf die Seiten-UID 0 weiterleiten und die Startseite anzeigen.

Parameter
: **theInt**: Ein Wert, der im positiven Falle 1zu1 zurückgeliefert wird und im negativen Falle auf 0 gesetzt wird.

## canBeInterpretedAsInteger

Natürlich kann ich einen Text wie 123test einfach nach INT konvertieren und erhalte als Ergebnis 123. Diese Methode hier geht jedoch einen Schritt weiter und prüft, ob das Ergebnis nach einer weiteren Konvertierung zurück nach String immernoch dem Ursprungswert 123test entspricht. Beispiel: 123test -> Konv. nach INT -> 123 -> Konv. nach STRING -> "123" != "123test".

Parameter
: **var**: Ein Zahlenwert oder ein String. Andere Werte wie Objekte und Arrays liefern sofort false zurück

## canBeInterpretedAsFloat

Ähnlich wie canBeInterpretedAsInteger. Prüft, ob der übergebene Wert als Float interpretiert werden kann. Da hier auch Stringwerte erlaubt sind, wird die Überprüfung mithilfe von preg_match realisiert.

Parameter
: **var**: Ein Zahlenwert oder ein String. Andere Werte wie Objekt oder Array liefern sofort false zurück.

## calculateWithPriorityToAdditionAndSubtraction

Diese Methode kann einfache Mathematische Berechnungen ausführen. Erlaubt sind + - * / ^ und %. Cool an dieser Methode ist, dass sie sogar die Punkt-vor-Strichrechnung berücksichtigt.

Parameter
: **string**: Ein Wert wie 3 + 5 * 7 der mit 38 quittiert wird.

## calculateWithParentheses

Diese Methode arbeitet eng mit calculateWithPriorityToAdditionAndSubtraction zusammen und kann zusätzlich mit Klammerrechnung umgehen.

Parameter
: **string**: Ein Wert wie 3 + 5 * 2 - (2 * 6) würde mit 1 quittiert werden

## isIntegerInRange

Es wird nur geprüft, ob sich eine Zahl in einem Bereich mit Werten von bis befindet. Wenn nicht, dann wird false zurückgeliefert.

Parameter
: **value**: Die Zahl, die geprüft werden soll
: **minimum**: Wie klein darf die Zahl sein?
: **maximum**: Wie groß darf die Zahl maximal sein? Hier gibt es keinen Defaultwert wie bei forceIntergerInRange. Ihr müsst also selbst darauf achten auf 32-bit-Systemen keinen Wert größer 2147483648 anzugeben.
