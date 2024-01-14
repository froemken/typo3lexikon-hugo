+++
title = "Test Prepared Statements"
date = 2024-01-09T21:01:57+01:00
slug = "test-prepared-statements"
+++

Ich hatte hier mal eine Extension gebaut, die ein paar hundert Datensäte live mit und ohne Prepared Statements abgefragt hat. Es wurde deutlich, dass es keinen großen Unterschied gab. Ja, es wurde sogar so irre, dass der Test, der zuerst ausgeführt wurde, grundsätzlich länger dauerte als der darauf folgende Test. Nicht wirklich repräsentativ. Ich habe daraufhin die Extension hier deinstalliert.
