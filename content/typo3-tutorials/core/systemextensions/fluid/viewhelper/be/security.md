+++
title = "Security"
date = 2024-01-09T20:28:26+01:00
aliases = ["security.html", "/typo3-tutorials/extensions/fluid/fluid-dokumentation/be/security.html"]
+++

## f:be.security.ifAuthenticated

Dieser ViewHelper hat keine Parameter und prüft nur, ob ein Backenduser sich authentifiziert hat oder nicht. Je nachdem wird der Inhalt aus dem `f:then` oder `f:else` ViewHelper geladen.

Dieser ViewHelper wird überwiegend aus dem Frontend heraus benötigt. So könnt ihr bestimmen, welche Inhalte nur angezeigt werden, wenn ein User im BE angemeldet ist. Ich denke, Frontend-Bearbeitung wäre eine gute Anlaufstelle für diesen ViewHelper.

### Beispiel WENN->DANN->SONST

```html
<f:be.security.ifAuthenticated>
    <f:then>
        Sie haben das Recht diesen Inhalt zu bearbeiten
    </f:then>
    <f:else>
        Sie muessen sich zuvor anmelden
    </f:else>
</f:be.security.ifAuthenticated>
```

### Beispiel, wenn angemeldet

```html
<f:be.security.ifAuthenticated>
    Hier kommt der Inhalt hin, den nur angemeldete Backend-Benutzer sehen.
</f:be.security.ifAuthenticated>
```

## f:be.security.ifHasRole

{{% badge style="green" icon="angle-double-up" %}}TYPO3 4.5{{% /badge %}}

Dieser ViewHelper prüft, ob ein im Backend angemeldeter User der im Parameter role angegebenen Benutzergruppe angehört. Wie schon bei dem f:if-ViewHelper wird bei Gültigkeit der Inhalt zwischen den Tags oder, wenn vorhanden, der Inhalt aus dem f:then-ViewHelper angezeigt. Falls gewünscht, kann auch der f:else-ViewHelper angegeben werden, der immer dann angezeigt werden soll, falls der User nicht der angegebenen Benutzergruppe angehört.

| Parameter | Erklärung |
|-----------|-----------|
| role | Entweder der Gruppenname (Groß- und Kleinschreibung beachten) oder die Gruppen-UID |

## Beispiel WENN->DANN->SONST

```html
<f:be.security.ifHasRole role="Administrator">
    <f:then>
        Sie dürfen diese Daten ändern
    </f:then>
    <f:else>
        Du nix Admin
    </f:else>
</f:be.security.ifHasRole>
```

### Beispiel, wenn angemeldet

```html
<f:be.security.ifHasRole role="2">
    Willkommen in der Gruppe der BE-Administratoren
</f:be.security.ifHasRole>
```
