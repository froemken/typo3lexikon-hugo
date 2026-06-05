+++
title = "Dependency Injection"
date = 2025-10-18T13:43:23+01:00
slug = "dependency-injection"
aliases = ["dependency-injection.html"]
+++

## Ein Leben ohne DI

PHP Projekte ohne Dependency Injection kann man gut an der Verwendung von `require` oder `include` Statements erkennen. Diese Statements werden genutzt, um die benötigten Klassen-Dateien zu laden. Anschließend werden die Klassen mittels `new` Instanziiert. Für kleinere Projekte völlig legitim. Aber je mehr das Projekt wächst, desto schwieriger wird es, später mal eine Klasse an eine andere Position zu verschieben.

```php
<?php

require_once 'Classes/Some/Service.php';

$service = new Some\Service();
```

Vielleicht kennt ihr auch solche Konstrukte:

```php
<?php

require_once 'Classes/Service/MailService.php';
require_once 'Classes/Service/LogService.php';
require_once 'Classes/Provider/ApiProvider.php';

$service = new Provider\ApiProvider(
    new Service\MailService(),
    new Service\LogService()
);
```

Hier ist der ApiProvider fest verdrahtet mit dem MailService und dem LogService. Hier kann niemand mehr den MailServer oder den LogService austauschen. Für kleinere Projekte mag das auch kein Problem sein, aber wenn Du später mehr mit UnitTests arbeiten (Mocks, Stubs), oder Deine eigene Community mehr Möglichkeiten anbieten möchtest eigene Ideen in Deine Programmierung mit einfließen zu lassen, solltest Du zumindest mal über eine Dependency Injection Implementation nachdenken.

## Was ist Dependency Injection?

Erklärung von KI: "Dependency Injection (DI) ist ein Software-Design-Pattern, das die Abhängigkeiten (Dependencies) einer Klasse von außen bereitstellt, anstatt sie innerhalb der Klasse selbst zu erstellen oder zu verwalten. Dies fördert die Entkopplung von Komponenten und erleichtert das Testen, Warten und Erweitern von Software."

Alles richtig, aber lasst mich das mal anhand eines Beispieles erklären.

