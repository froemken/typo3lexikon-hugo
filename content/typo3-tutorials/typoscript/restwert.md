+++
title = "Restwert"
date = 2024-01-09T20:58:16+01:00
aliases = ["restwert.html"]
+++

Beispiel für die Restwertberechnung (Modulo):

```typo3_typoscript
10 = LOAD_REGISTER
10.intRest.stdWrap.dataWrap = {register:tx_templavoila_pi1.sectionPos}%4
10.intRest.prioriCalc = 1
11 = LOAD_REGISTER
11.addClass = last
11.addClass.noTrimWrap = | ||
11.addClass.if.isFalse.data = register:intRest
20 = IMAGE
20.file.import = uploads/tx_templavoila/
20.file.import.current = 1
20.file.import.listNum = 0
20.file.width = 230
20.stdWrap.dataWrap = <div class="bildMitLink{register:addClass}">|</div>
20.stdWrap.typolink.parameter.field = field_link
30 = RESTORE_REGISTER
```
