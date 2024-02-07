+++
title = "Value for M was not f"
date = 2024-01-09T20:46:18+01:00
aliases = ["value-for-m-was-not-found-as-module.html"]
+++

## Value "" for "M" was not found

Diese Fehlermeldung hatte ich heute als ich meine neue Extension programmiert hatte. Es lag daran, dass ich "action" in meinem Form-Tag falsch verlinkt habe. Im Backend verweisen die Formulare auf mod.php. Für die Formulare reicht das aber noch nicht.

Es muss heißen:

```html
<form action="mod.php?M=tools_txsflangchangeM1" method="post">...
```

Ihr dürft also nicht vergessen Euren Extensionnamen mit anzugeben...
