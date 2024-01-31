+++
title = "filefill"
date = 2024-01-09T20:51:22+01:00
aliases = ["filefill.html"]
+++

Kennt Ihr das? Die LIVE-Seite und deren Verzeichnisse fileadmin und uploads sind so utopisch voll, dass ihr kein Bock oder Platz habt all diese Dateien für die lokale Entwicklung herunterzuladen? Hier hilft die Extension `filefill` von Nicole Cordes. Alle Dateien, die für einen Request, sei es Backend oder Frontend fehlen, werden vom LIVE-Server heruntergeladen und lokal eingebunden und stehen somit für den nächsten Request direkt zur Verfügung.

## Installation

```shell
composer require ichhabrecht/filefill
```

## Konfiguration

Diese Extension erzeugt einen neuen Tab File Fill für lokale Datei-Storages, die ihr auf der Rootseite (PID 0) findet. Siehe zum Beispiel den fileadmin-Storage. Auf diesem Tab könnt ihr Filefill explizit aktivieren.

Damit Filefill weiß von wo die Dateien herunterzuladen sind, habt Ihr weiter unten 3 Möglichkeiten:

- `Single Domain`: Hier könnt Ihr die Domain Eurer LIVE-Seite inkl. http:// oder https:// anlegen.
- `Domain Records`: Aktiviert die Checkbox. Somit wird der sys_domain-Datensatz der aktuellen Seite ausgelesen und diese Domain(s) als Ursprungsseite für das Herunterladen der Bilder von der Remoteseite verwendet.
- `Placeholder`: Das finde ich auch ne pfiffige Lösung. Anstatt mit den Bildern von der LIVE-Seite zu arbeiten, könnt Ihr mit dieser Variante zufällige Bilder von dem Webservice placeholder.com verwenden.

Alternativ könnt Ihr die Einstellungen der Extension auch per `AdditionalConfiguration.php` vornehmen.

Damit die Extension `filefill` beim Deployment auf LIVE nicht aktiv ist, empfehle ich den Einsatz der `runtimeActivatedExtensions`.
