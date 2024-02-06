+++
title = "Glossar"
date = 2024-01-09T20:57:58+01:00
aliases = ["glossar.html"]
+++

Ich habe die Tage einen Glossar gesucht, mit dem man Datensätze aus der Tabelle fe_users anzeigen lassen kann. Allerdings können die Glossars im TYPO3-Ter nur mit den Extension eigenen Datensätzen umgehen.

Also habe ich mir Dank TemplaVoila einen eigenen Glossar gebastelt. Dazu braucht ihr einfach eine HTML-Vorlage in der nur ein <div>blabla</div> gemapped wird. Den Inhalt erledigt komplett mein Script. Legt das Mapping bitte als TypoScriptObjectPath an.

Da es sich hierbei um ein Script handelt, dass auf eine GET-Variable zugreift, habe ich noch den CROP-Befehl verwendet. Damit können Hacker in die URL reinkrickeln was sie wollen, das Script kürzt den Befehl auf ein Zeichen ab...alles dahinter wird rigeros abgeschnitten.

Hier das Script:

```typo3_typoscript
page.10 = LOAD_REGISTER
page.10.glossar.data = GPvar:tx_glossar_pi1|glossar
page.10.glossar.crop = 1

temp.alphabet = COA
temp.alphabet {
  1 = TEXT
  1.value = A
  1.wrap = |
  1.typolink {
    parameter = {$GLOSSAR_PAGE}
    useCacheHash = 1
    ATagParams = class="internal-link"
    additionalParams < temp.alphabet.1.value
    additionalParams.wrap = &tx_glossar_pi1[glossar]=|
  }

  2 < .1
  2.value = B
  2.typolink.additionalParams < temp.alphabet.2.value
  3 < .1
  3.value = C
  3.typolink.additionalParams < temp.alphabet.3.value
  4 < .1
  4.value = D
  4.typolink.additionalParams < temp.alphabet.4.value
  5 < .1
  5.value = E
  5.typolink.additionalParams < temp.alphabet.5.value
  6 < .1
  6.value = F
  6.typolink.additionalParams < temp.alphabet.6.value
  7 < .1
  7.value = G
  7.typolink.additionalParams < temp.alphabet.7.value
  8 < .1
  8.value = H
  8.typolink.additionalParams < temp.alphabet.8.value
  9 < .1
  9.value = I
  9.typolink.additionalParams < temp.alphabet.9.value
  10 < .1
  10.value = J
  10.typolink.additionalParams < temp.alphabet.10.value
  11 < .1
  11.value = K
  11.typolink.additionalParams < temp.alphabet.11.value
  12 < .1
  12.value = L
  12.typolink.additionalParams < temp.alphabet.12.value
  13 < .1
  13.value = M
  13.typolink.additionalParams < temp.alphabet.13.value
  14 < .1
  14.value = N
  14.typolink.additionalParams < temp.alphabet.14.value
  15 < .1
  15.value = O
  15.typolink.additionalParams < temp.alphabet.15.value
  16 < .1
  16.value = P
  16.typolink.additionalParams < temp.alphabet.16.value
  17 < .1
  17.value = Q
  17.typolink.additionalParams < temp.alphabet.17.value
  18 < .1
  18.value = R
  18.typolink.additionalParams < temp.alphabet.18.value
  19 < .1
  19.value = S
  19.typolink.additionalParams < temp.alphabet.19.value
  20 < .1
  20.value = T
  20.typolink.additionalParams < temp.alphabet.20.value
  21 < .1
  21.value = U
  21.typolink.additionalParams < temp.alphabet.21.value
  22 < .1
  22.value = V
  22.typolink.additionalParams < temp.alphabet.22.value
  23 < .1
  23.value = W
  23.typolink.additionalParams < temp.alphabet.23.value
  24 < .1
  24.value = X
  24.typolink.additionalParams < temp.alphabet.24.value
  25 < .1
  25.value = Y
  25.typolink.additionalParams < temp.alphabet.25.value
  26 < .1
  26.value = Z
  26.typolink.additionalParams < temp.alphabet.26.value
  27 < .1
  27.value = 0
  27.typolink.additionalParams < temp.alphabet.27.value
  28 < .1
  28.value = 1
  28.typolink.additionalParams < temp.alphabet.28.value
  29 < .1
  29.value = 2
  29.typolink.additionalParams < temp.alphabet.29.value
  30 < .1
  30.value = 3
  30.typolink.additionalParams < temp.alphabet.30.value
  31 < .1
  31.value = 4
  31.typolink.additionalParams < temp.alphabet.31.value
  32 < .1
  32.value = 5
  32.typolink.additionalParams < temp.alphabet.32.value
  33 < .1
  33.value = 6
  33.typolink.additionalParams < temp.alphabet.33.value
  34 < .1
  34.value = 7
  34.typolink.additionalParams < temp.alphabet.34.value
  35 < .1
  35.value = 8
  35.typolink.additionalParams < temp.alphabet.35.value
  36 < .1
  36.value = 9
  36.typolink.additionalParams < temp.alphabet.36.value
}

lib.glossar = COA
lib.glossar {
  10 = TEXT
  10.value = <div class="glossar-alphabet">
  20 < temp.alphabet
  30 = TEXT
  30.value = </div>
  40 = COA
  40 {
    10 = CONTENT
    10.stdWrap.ifEmpty = <div class="glossar-eintrag">{$GLOSSAR_NICHTS_GEFUNDEN}</div>
    10 {
      table = {$GLOSSAR_TABELLE}
      select {
        pidInList = {$GLOSSAR_FEUSER}
        andWhere.data = register:glossar
        andWhere.wrap = name LIKE "|%"
        andWhere.ifEmpty = A
        selectFields = name, first_name, last_name
        orderBy = name
      }

      renderObj = COA
      renderObj {
        10 = TEXT
        10.value = <div class="glossar-eintrag">
        20 = TEXT
        20 {
          required = 1
          wrap = <div class="glossar-name">|</div>
          field = name
        }

        30 = TEXT
        30 {
          required = 1
          wrap = <div class="glossar-vorname"><strong>Vorname: </strong>|</div>
          field = first_name
        }

        40 = TEXT
        40 {
          required = 1
          wrap = <div class="glossar-nachname"><strong>Nachname: </strong>|</div>
          field = last_name
        }

        50 = TEXT
        50.value = </div>
      }
    }
  }
}
```

Konfigurieren lässt sich das Script mit folgenden Konstanten:
page ist die aktuelle Seite (UID) auf der sich das Script befindet.
fe_user ist die UID des Sysfolders in dem sich die FE-User befinden
tabelle ist die anzufragende Tabelle
nichts gefunden ist der Text, wenn kein Eintrag gefunden wurde.

Mit ein bisschen geschick lässt sich das Script damit an nahe zu alle Tabellen anpassen.

GLOSSAR_PAGE = 8
GLOSSAR_FEUSER = 35
GLOSSAR_TABELLE = fe_users
GLOSSAR_NICHTS_GEFUNDEN = Leider keinen Eintrag mit dem gewählten Buchstaben gefunden.

Das sollte es gewesen sein.

Wenn ihr eine andere Tabelle auswählt, dann müsst ihr im Script noch die Ausgabe der einzelenen Felder verändern. Aber jeder, der sich mit dem rederObj auskennt, sollte keine Probleme damit haben und in meinem Script ist ja schon ein größeres Beispiel enthalten.
