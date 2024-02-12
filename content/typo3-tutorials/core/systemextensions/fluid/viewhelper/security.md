+++
title = "Security"
date = 2024-01-09T20:27:43+01:00
aliases = ["security.html"]
+++

## f:security.ifAuthenticated

Dieser ViewHelper beinhaltet keine Parameter. Er prüft einfach nur, ob ein User im Frontend angemeldet ist oder nicht. Wie schon bei dem `f:if` ViewHelper wird bei Gültigkeit der Inhalt zwischen den Tags oder, wenn vorhanden, der Inhalt aus dem `f:then` ViewHelper angezeigt. Falls gewünscht, kann auch der `f:else` ViewHelper angegeben werden, der immer dann angezeigt werden soll, falls der User nicht angemeldet ist.

### Beispiel WENN->DANN->SONST

```html
<f:security.ifAuthenticated>
    <f:then>
        Will kommen im Club der angemeldeten Benutzer
    </f:then>
    <f:else>
        Sie muessen sich zuvor anmelden
    </f:else>
</f:security.ifAuthenticated>
```

### Beispiel, wenn angemeldet

```html
<f:security.ifAuthenticated>
    Hier kommt der Inhalt hin, den nur angemeldete Benutzer sehen.
</f:security.ifAuthenticated>
```

## f:security.ifHasRole

Dieser ViewHelper prüft, ob ein im Frontend angemeldeter User, der im Parameter `role` angehörigen Benutzergruppe angehört. Wie schon bei dem `f:if` ViewHelper wird bei Gültigkeit der Inhalt zwischen den Tags oder, wenn vorhanden, der Inhalt aus dem `f:then` ViewHelper angezeigt. Falls gewünscht, kann auch der `f:else` ViewHelper angegeben werden, der immer dann angezeigt werden soll, falls der User nicht der angegebenen Benutzergruppe angehört.

### Parameter

| Parameter | Erklärung |
|-----------|-----------|
| role | Entweder der Gruppenname (Groß- und Kleinschreibung beachten) oder die Gruppen-UID |

### Beispiel WENN->DANN->SONST

```html
<f:security.ifHasRole role="Redakteur">
    <f:then>
        Sie dürfen diese Daten ändern
    </f:then>
    <f:else>
        Als Editor haben Sie kein Recht, diesen Datensatz zu bearbeiten
    </f:else>
</f:security.ifHasRole>
```

### Beispiel, wenn angemeldet

```html
<f:security.ifHasRole role="2">
    Willkommen in der Gruppe der FE-Administratoren
</f:security.ifHasRole>
```
