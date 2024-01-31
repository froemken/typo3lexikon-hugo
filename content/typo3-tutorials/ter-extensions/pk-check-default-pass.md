+++
title = "pk_check_default_pass"
date = 2024-01-09T20:52:16+01:00
aliases = ["pk-check-default-pass.html"]
+++

## Wenn sich der User mit einem Standardpasswort anmeldet

Die Extension `pk_check_default_pass` prüft bei der Anmeldung eines Frontendusers, ob dieser sich mit einem Standardpasswort anmeldet, dass vom Administrator in der Extensionkonfiguration vorgegeben wurde. Falls das Passwort übereinstimmt, dann wird der User auf eine vorgegebene Seite weitergeleitet, wo er dann sein Passwort ändern kann.

Die Idee finde ich gut, aber die Umsetzung lässt zu wünschen übrig. Auch wenn die Extension als `example` im Repository gelistet ist möchte ich hier trotzdem ein paar Nachteile aufführen:

- Die Extension beinhaltet keine Dokumentation
- Fehlermeldungen sind nicht mehrsprachenfähig (hardcoded)
- Man kann nur ein Standardpasswort vorgeben. Eine Liste von Standardpasswörter ist nicht möglich.
- Man wird zwar auf eine andere Seite weitergeleitet, aber man muss sich schon selbst darum kümmern, dass sich dort ein Editierungsformular befindet oder das EDIT-Formular von z.B. sr_feuser_register eingebunden hat.
- Die Extension felogin bietet einen HOOK an, mit dem man das Passwort überprüfen könnte. Dieser wird hier nicht verwendet und es wird "umständlich" überprüft, ob ein Header schon gesendet wurde oder nicht.
