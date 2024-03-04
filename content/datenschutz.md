+++
title = "Datenschutz"
date = 2024-03-05T00:03:28+01:00
slug = "datenschutz"
weight = 5
aliases = ["datenschutz.html"]
+++

## Erträge

Diese Webseite ist eine rein private Webseite und verfolgt keine Gewinnbringung. Auf meiner Webseite findet ihr keine (bezahlte) Werbung.

## Cookies

Die Webseite verwendet keine Cookies und auch keine Trackingcookies.

## Kontakt

Meine Webseite basiert auf rein statischen HTML Inhalten (Hugo IO). Damit ist die Realisierung eines Kontaktformulars nicht möglich. Auf der Kontakt-Seite findet ihr einen Link zu einem Google-Formular. Einen entsprechenden Hinweis, dass ihr nach Klick auf den Link auf einem anderen Service landet, habe ich hinterlegt.

Wie lange ich eure Mail aufbewahre, kann ich euch nicht sagen. Die Mail landet in meinem privaten Postfach (Google Server) und dort gehe ich grob jedes Jahr einmal durch und lösche Mails nach eigenem Ermessen.

Ihr wollt nichts mit Google zu tun haben? Dann kontaktiert mich bitte über [Slack TYPO3](https://typo3.slack.com/). Die Slack-Einrichtung findet ihr in der [TYPO3 Dokumentation](https://typo3.org/community/meet/chat-slack).

## Hosting

Meine Webseite wird derzeit von [jweiland.net](https://jweiland.net) gehostet. Serverstandort: `Köln`. Die Matomo-Integration liegt ebenfalls bei [jweiland.net](https://jweiland.net), hier jedoch im Cloud Hosting Tarif mit Serverstandort: `Frankfurt`.

Warum? Das Wildcard-Zertifikat kostet ca. 160 EUR/Jahr. Alternativ würde die spezifische Absicherung der Matomo-Subdomain nochmals 36 EUR/Jahr kosten. Für ein reines Freizeit-Projekt ist mir das zu viel.

## Protokoll

Ich verwende [Matomo](https://matomo.org/), um herauszufinden, woher die Aufrufe kommen. Sind es mehr Aufrufe von Suchergebnissen, sozialen Medien oder direkte Aufrufe?

Tracking:
: Es interessiert mich nicht, wie ihr euch über meine Webseite bewegt. Ich freue mich einfach nur, dass ihr da seid. Darum habe ich das Tracking mittels Cookie in Matomo vollständig deaktiviert.

IP-Adresse:
: Die IP-Adresse hilft, um herauszufinden, aus welchem Land ihr kommt. Aber dafür brauche ich nicht die vollständige IP-Adresse. Ich habe Matomo so konfiguriert, dass nur die ersten beiden Oktetten (die ersten 2 Bytes) protokolliert werden.

Anonymisierte Verweise:
: Mittels HTTP Referrer kann analysiert werden von welcher Seite ihr kommt. Da mich nur die Seite interessiert und nicht was ihr dort gemacht oder eingestellt habt, habe ich Matomo so konfiguriert, dass jegliche Query-Parameter (In einer URL alles hinter dem Pfad beginnend mit `?`) entfernt wird.

Webserver Logs:
: Der Hoster bietet das Protokollieren der Webseitenzugriffe für einen Zeitraum von 1 Woche an. Da in diesem Protokoll vollständige IP-Adressen enthalten sind, und sich diese Daten auch nicht anonymisieren lassen, habe ich die Hoster eigene Protokollierung vollständig abgeschaltet.
