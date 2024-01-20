+++
title = "TYPO3 Hoster"
date = 2024-01-09T20:59:55+01:00
slug = "typo3-hoster"
aliases = ["typo3.hoster.html", "typo3-provider.html"]
+++

Auf dieser Seite stelle ich Euch diverse Hosting Unternehmen vor, die unter anderem Hosting für TYPO3 anbieten oder sich sogar voll und ganz auf TYPO3 Hosting konzentriert haben. Es geht hier nicht darum, wer ist besser, wer hat mehr Speicher, wessen Server laufen mit weniger Unterbrechungen. Es geht hier darum zu vermitteln, wie gut sich TYPO3 bei den Hostern betreiben lässt. Schlussendlich müsst ihr selbst einen Hoster für eure spezifischen Anforderungen finden.

TYPO3 Hosting anzubieten und zu bewerben und dann keinen CronJob für das scheduler Modul oder gar kein GraphicsMagick/ImageMagick anzubieten empfinde ich als sehr dreist. Dementsprechend: nehmt es mir bitte nicht übel, wenn ich meinem Frust darüber hier äußere.

Die folgende Aufstellung ist sehr Entwickler lastig und kann unerfahrene Interessierte durchaus mehr irritieren als nützen. Bitte nehmt Euch im Zweifel nen IT Kollegen zur Hand bevor ihr diese Liste durcharbeitet.

## punkt.de GmbH

Das Team von punkt.de GmbH habe ich erstmals auf den TYPO3 DevDays in Nürnberg kennen gelernt. An deren Infostand wurde schnell klar, dass es sich hier nicht um das klassische Shared Hosting handelt, sondern um professionelle VPS und dedicated Server. Das Besondere an deren Serverkonfiguration: Beide Varianten sind managed! Heißt: Du als Entwickler oder Kunde brauchst Dir keine Sorgen um Updates und Sicherheit zumachen. Das übernimmt punkt.de für Dich.

Ein ganzer Server, sei es virtuell oder dediziert, ist nichts für Anfänger! Solr z.B. ist vorinstalliert, aber den Solr Core müsst ihr schon selbst einrichten. Der Nginx liefert nach Bestellung eine Welcome-Seite aus. Es ist Eure Aufgabe den Nginx so zu konfigurieren, dass er Eure TYPO3 Seite ausliefert. Dateiberechtigungen, Let's Encrypt, Fail2ban, DNS, FTP, Datenbanken, Email (MTA), all das liegt in Eurer Hand und zwar ohne Konfigurationsoberfläche. Ihr wollt eine Domain konnektieren? Dann wendet Euch an den Support. Ihr wollt einen MySQL/MariaDB Benutzer anlegen, dann viel Spaß mit den GRANT-Befehlen auf der MariaDB-Shell.

Der Support steht Euch bei Fragen gerne zur Verfügung und solltet Ihr es mal nicht hinbekommen einen Service so einzurichten, wie Ihr es Euch vorstellt, dann könnt Ihr die Aufgabe auch gerne mit minutengenauer Abrechnung an den Support übergeben.

Natürlich bekommt Ihr einen VPS für ein paar Euro in jeder Ecke, aber dabei handelt es sich oft um Massentourismus auf dem Hauptserver, was zu enormen Performanceeinbußen eines jeden VPS führt. Je weniger VPS ein Hauptserver verwalten muss, desto teurer, aber auch schneller der VPS. Das Portfolio für Server ohne Updatesorgen findet Ihr auf der Subdomain [infrastructure.punkt.de](https://infrastructure.punkt.de/de/produkte/proserver.html) und beginnt mit dem "Virtual S" zu einem Preis von 46,41 Euro (Stand: 19.11.2021).

## dogado GmbH

Auf meiner Suche nach TYPO3 Hostern bin ich neulich über dogado GmbH gestolpert. Das Paket Web M 3.0 für 6,99 € bzw. 7,99 € bei monatlicher Abrechnung (Stand: 25.03.2023) bietet 150 GB Speicherplatz, der gleichermaßen für Web als auch Email verwendet wird. 512 MB für PHP Memory Limit sind ordentlich, jedoch beachtet das RAM Limit von 2 GB. Wenn ihr wirklich knapp 500 MB Ram pro Prozess verbratet, dann ist nach dem 4ten gleichzeitigen Request halt Ende. 170% CPU Limit ist arg schwammig. Euch stehen halt so 85% von 2 CPU Cores zur Verfügung. Wenn dann mal ImageMagick und die Datenbank etwas länger brauchen, dann war's das mit der Auslieferung der Webseite. Haltet Eure Requests klein, ladet kleine Bilddateien hoch und erstellt Indexe für die Datenbanktabellen. Üblicherweise kann man in Plesk über Resourcennutzung selbst einsehen, ob und wann man an die vorgegebenen Limits gestoßen ist. Bei Dogado ist dies leider nicht möglich. Auch die Statistiken von Plesk lassen sich nicht aufrufen, filtern und sortieren. Die Logs/Statistiken lassen sich nur von einem vorgegebenen Pfad herunterladen. 30 MB/s IO für lesen und schreiben sollten auch hier für den Normalgebraucht völlig OK sein. Bei viel Bildmaterial und auch bei Auslagerung des TYPO3 Caches auf die Platte, kann ein Aufräumen (Clear Cache) schonmal etwas länger als gewöhnlich brauchen.

Bei meinen Tests konnte das CSS und JS meiner Seite nicht geladen werden. Ich habe das Compression Level auf 9 gestellt und der Browser hat nicht die Information über komprimierte Daten erhalten. Ich vermute, dass in dem Paket mod_deflate fehlt. Aber immerhin ist der HTTP/2 Standard on Board. Ich kann bestätigen, dass die Auslieferung meiner Webseite wirklich schnell von statten geht. Auch das TYPO3 Backend läuft schnell. Die Seiten-Icons im Seitenbaum sind instant da.

Auf der Shell hinkt das System jedoch. Nach Anmeldung per SSH wird man mit einem lieblosen Prompt begrüßt. Der aktuelle Pfad ist darin nicht enthalten. Klar, kann man einstellen, aber da muss man schon wissen, wie das geht. Hinzu kommt nen 2-Zeiler an Warnungen:

```shell
-bash: nodenv: command not found
ls: cannot access /var/lib/rbenv/versions: No such file or directory
```

Diese Meldung landet natürlich auch für jeden Remotezugriff, den Euer Deployment veranstaltet, im Protokoll. Macht voll viel Spaß zwischen diesen 70 Warnungen die wahren Fehler zu finden. Narf. Das weckt nicht gerade Vertrauen und lässt in mir das Gefühl aufkommen, dass sich die Techniker nicht mit dem System auskennen, wobei ich aber auch sagen muss: Das was die Techniker da mit Plesk (visuell) veranstaltet haben, ist der Hammer!

Composer ist nicht vorinstalliert. Zwar ist mit "rsync", "wget" und "curl" alles da, um Dateien herunterzuladen, aber gerade in Bezug auf Composer nervt die voreingestellte PHP 5.6er Fassung schon arg. Mit einer ".php-version" Datei habe ich es nicht geschafft die PHP Version auf der Shell umzustellen. Erst mit SymLinks und PATH Anpassungen lief es dann endlich. Etwas mehr Wohlfühlaroma wäre hier schon schön.

Erfreulich ist die Datenbank-Performance. InnoDB Buffer ist mit 4 GB völlig ausreichend. Obwohl der Server schon seit einem Monat läuft, sind auf dem Server, auf dem sich mein Paket befindet, gerade erst 700 MB davon belegt. Eine Hit Ration von 100% sieht man auch nicht alle Tage.

## webgo GmbH

Aufgrund einer Empfehlung eines TYPO3 Kollegen habe ich mir das SSD Webhosting Profi bestellt. Dies bekommt ihr derzeit für monatlich 11,95 € (Stand: 26.03.2023). Achtung: Es gibt eine Einrichtungsgebühr von 14,95 € und die Abrechnung erfolgt nicht monatlich, sondern im Quartal.

TYPO3 empfiehlt mindestens 256 MB PHP Memory Limit. Mit 512 MB PHP Memory Limit seid ihr mit diesem Paket also gut versogt. 50 GB SSD Speicher empfinde ich schon fast knapp, da Web, DB und auch Emails sich diesen Speicherplatz teilen müssen.

Der Ersteindruck in das Kundenmenü hat mich erschrocken und erinnert mich an Designs von vor 15 Jahren. Auf der linken Seite sind so 200 Pixel breite Menüpunkte in denen der Text drin steht. Ihr müsst mit der Maus genau den Text treffen, ansonsten öffnet sich der Menüpunkt nicht. Bekanntes Problem, dass ich so, und schon gar nicht bei einem modernen Hoster, erwartet hätte wieder zu sehen.

Die Verbindung per SSH gestaltete sich schwierig. Nach dem ersten Login habe ich mich schon gefreut, dass das .ssh Verzeichnis bereits existiert. Also authorized_keys Datei angelegt, public Key rein, aber die Anmeldung über publickey wollte einfach nicht gelingen. Vom Server kommt nur Fehler type: 51 zurück. Nach knapp 40 Minuten Diskussion mit dem Support (am Sonntag! Respekt!) kam dann die Bestätigung: Die individuellen authorized_keys Dateien der Benutzer werden nicht eingelesen. Der Public Key ist entweder über Chat oder Support-Ticket zuvor zu registrieren.

Das Deployment klappte ganz gut. So Befehle wie "wget", "curl", "rsync", "chmod", "chgrp", "chown", "ps", "setfacl" sind alle da. Der letzte Fehler hing an einer alten PHP 7.1, die per default auf der Shell eingerichtet war. Im Kundenmenü kann man die PHP-Version nur global einstellen. Damit war dann mein Problem auf der Shell zwar gelöst, aber ich brauche einen Hoster, wo ich die PHP-Version individuell pro Domain/Subdomain einstellen kann. Das ist hier bei WebGO leider nicht möglich und somit habe ich den Test leider abbrechen müssen.

## netcup GmbH

Ich bin wirklich lange Fan von Netcup gewesen. Schon sehr früh sind die auf diese hammer geilen AMD EPYC 7702 Prozessoren umgestigen und konnten damit gerade im VPS Bereich unfassbar performante und doch sehr günstige Preise anbieten. Nagelt mich jetzt nicht fest, aber ich glaub es waren 6 dedizierte Cores, 16 GB RAM und 320 GB Platte für um die 15 Euro. Mittlerweile sind wir bei 16,89 € (Stand: 25.03.2023) monatlich bei jährlicher Laufzeit.

Seit ca. 2021 muss bei denen intern irgendwas passiert sein. Auf einmal kosten die Server 20,27 € (Stand: 25.03.2023) bei monatlicher Kündigungsfrist. Also über 3 Euro mehr. Dabei ist völlig ungewiss, wo der Server steht. Deutschland/Nürnberg oder Österreich/Wien. Wer den Server bevorzugt in Deutschland hosten will, packt nochmal 2,50 € monatlich oben drauf. Womit wir dann bei Standort Nürnberg und monatlicher Kündigungsfrist dann bei 23,31 € (Stand: 25.03.2023) angekommen sind.

Da ich nicht die Zeit für Serververwaltung und Wartung habe, habe ich mir noch das Webhosting 4000 Paket angeschaut. Für 6,52 € (Stand: 25.03.2023) im Monat mit 6 inklusiv Domains, 500 GB SSD Speicherplatz und nem PHP Memory Limit von 512 MB ein sehr guter Preis. Aber Achtung: Die 500 GB werden zu je 250 GB auf Web- und Email-Speicherplatz aufgeteilt! Aktuell wird PHP 7.2 bis 8.2 unterstützt. Nochmal Achtung: Die Abrechnungsperiode sind 12 Monate! Da steht zwar überall monatlich, aber nach der Buchungs bekommst halt ne Rechnung über 78,24 € für das komplette Jahr.

Für Freunde von Standalone TYPO3 Installationen bestimmt ein super Angebot, aber den Composer Freunden rate ich derzeit von diesem Angebot ab. Die Bash ist halbherzig eingerichtet. Nen Prompt in dem man nicht mal den aktuellen Pfad sieht. Klar, kann man sich einrichten, wenn man sich auskennt. "chgrp", "setfacl", "ps", "rsync" alles nicht vorhanden. "composer" muss man sich auch selbst installieren. "curl" ist auch nicht vorhanden, aber man kann sich wenigstens mit "wget" behelfen. Am schlimmsten finde ich, dass man den open_basedir nicht auf "none" einstellen kann. Gerade bei composer Installationen liegt TYPO3 im "public" Ordner und aufgrund dieser open_basedir Beschränkung kann die "vendor/autoload.php" im übergeordneten Ordner nicht geladen werden.

## Strato

Mein "Liebling" unter den Providern. Immer wieder Testsieger, super Performance und und und. Ich verstehe es nicht und ich kann diese Aussagen nicht bestätigen. Ich habe einen Kunden bei Strato und das Surfen über diese Webseite ist eine Katastrophe. Nach einem Telefonat mit dem Support habe ich erfahren, dass der Webauftritt des Kunden noch auf einem alten Server liegt und wir mithilfe eines Update-Konfigurators auf einen der neueren Servern umziehen können. Nach dem Umzug war die Webseite wirklich sehr viel schneller, aber jetzt über die Monate hinweg wurde sie wieder langsamer. Ich werd einfach das Gefühl nicht los, dass zu viele Webauftritte auf einem Server liegen.

Laut einem [FAQ-Eintrag](https://www.strato.de/faq/article/238.html) ist seit 30.01.2012 ImageMagick auch in den günstigeren Paketen PowerWeb Basic für gerade mal 5,90 € verfügbar. Ein attraktiver Preis, aber solange die Performance nicht stimmt definitiv nicht empfehlenswert.

23.01.2013: Auf Grund einer Urlaubsvertretung habe ich heute ein TYPO3-Projekt einer anderen Agentur betreut. Problem war, dass die Webseite auf dem Stratoserver jede Woche nicht mehr erreichbar war. Meldung: Filesize exceeded und das obwohl noch über 1GB frei sind. Problem: Strato verbietet Dateien größer 30MB. Der Deprecation-Log war aktiviert und erreichte nach 1 Woche immer diese 30MB-Grenze. Das Löschen dieser Datei hilft nicht, es müssen auch noch die temporären Dateien in var/locks gelöscht werden.

26.03.2023: Ich habe meine Seite mal für ein paar Monate über einen VPS mit Plesk, 6 vCores, 16 GB RAM, 500 GB Festplatte für 9 € monatlich (Stand: 26.03.2023) laufen lassen. Achtung: Bei monatlicher Kündungsfrist sind wir bei 10 € pro Monat. Dennoch, wenn es in die Abendstunden rein geht, dann schläft das TYPO3 Backend schier ein. Speicherzeiten von 12-15 Sekunden sind dann keine Seltenheit. Tagsüber, sofern man daran denkt open_basedir auf None zu stellen, wird die Seite in gut 180ms ausgeliefert. Die Domainpreise empfinde ich für einen so großen Hoster wie Strato als Wucher: 12 € pro Jahr (Stand: 26.03.2023). Eine saubere Integration vom Kundenmenü zum Plesk gibt es auch nicht. DNS Einstellungen müssen im Kundenmenü gemacht werden. Änderungen in Plesk haben keine Auswirkung.

## IONOS SE

IONOS ist das ausgegliederte Webhosting- und Cloudhosting-Geschäft von 1&1. Mit neuen Produkten am Markt wird es Zeit sich diese mal anzuschauen. Rausgesucht habe ich mir das Webhosting Plus für 10 € im Monat (Stand: 26.03.2023). Achtung: Es wird eine Einrichtungsgebühr von 10 Euro fällig und wenn ihr eine monatliche Kündigung wünscht, dann kostet Euch das Paket 11 € pro Monat.

200 GB Speicherplatz sind wirklich ordentlich. Vorteil: Dieser Speicherplatz wird nicht mit dem Speicherplatz von Emails und Datenbanken vermischt. Jede Email-Adresse und jede Datenbank hat ihre eigenen 2 GB Speicher. Aber auch hier Achtung: In diesem Paket ist nur EINE Email-Adresse kostenfrei enthalten. Weitere Email-Adressen müssen hinzugekauft werden. Beachtet auch das Limit der Datenbank. Wenn Euch da die sys_log voll läuft, dann war's das mit der Webseitenauslieferung. Behaltet da also bitte ein Auge drauf. Nach Rücksprache mit dem Support befindet sich in dem Paket keine SSD. Diese sind den Server-Paketen vorenthalten. Somit wird Eure Webseite von Festplatten mit drehender Scheibe ausgeliefert. Den Lesezugriff muss ich loben, aber der Schreib- bzw. Löschzugriff ist ein Problem. Das Löschen eines Deployment Releases mit 20.000 Dateien dauert gut 35 Sekunden.

Eine weitere Besonderheit ist die Beschränkung auf 262.144 Dateien bzw. Inodes. Mehr Dateien geht nicht und da hilft auch kein Update auf Premium oder Ultimate. Begründung: Damit wird z.B. sichergestellt, dass die Backups sauber durchlaufen. Für 1-2 Projekte ist die Anzahl der Dateien völlig ausreichend. Passend also, dass in diesem Paket gleich 2 Domains inkl. 2 SSL Zertifikate bereits inkludiert sind. Das Paket besitzt kein Let's Encypt! Für jede weitere Domain, die ihr hier verknüpft, müsst ihr bei Bedarf ein weiteres SSL Zertifikat hinzukaufen. Wenn ihr mit Deployer arbeitet, dann habt ihr immer so 3-5 Releases pro Projekt auf dem Server. Jedes Release dürfte so 20.000-30.000 Dateien haben. Bei mehr als 2 Projekten wird es knapp mit den Inodes. Ohne Deployment sollten weitere Domains/Projekte kein Problem darstellen.

Das PHP Memory Limit liegt mit 640 MB weit über dem Durchschnitt. In Summe stehen Euch bis zu 9 GB RAM zur Verfügung. Heißt: Selbst wenn ihr pro PHP Prozess 500 MB benötigt (und das ist schon viel), dann sind immer noch 18 gleichzeitige Verbindungen möglich, bevor der nächste Webseitenbesucher einen Fehler bekommt. Mich wunderte die Angabe "1 CPU". Wenn da nun ein Quad Core drin ist, dann wären ja doch wieder nur 4 gleichzeitige Verbindungen möglich. Ich habe mal /proc/cpuinfo analysiert. Da ist nen Xeon Gold CPU mit 26 Cores und über 100 logischen Cores drinne. Also alles weit über 18. Da ist genug Luft drinne.

Der InnoDB Buffer ist mit 12 GB ordentlich groß und ne Hit Ratio von 99,9% ist lobenswert.

Auf der IONOS Webseite, noch im Header-Bereich, wird auf PHP 8.2 mit JIT Compiler hingewiesen. Dabei handelt es sich um eine Erweiterung des OpCaches. Auch "PHP mit OpCache" wird in der Produktbeschreibung aufgeführt. Jedoch erscheint beides nicht in der Ausgabe von phpinfo(); Nach Rücksprache mit dem Support hieß es, das muss pro Domain individuell aktiviert werden. Im FAQ habe ich einen Eintrag gefunden, wie und wo man eine eigene php.ini Datei erstellen kann. Ich habe kurzerhand OpCache selbst manuell aktiviert, jedoch konnte ich keinen Performance-Boost ausmachen. Diese Seite beschreibt das Problem und lagert die kompilierten PHP Dateien auf der Festplatte aus: [OpCode Caching manuell einschalten](https://www.netzzoom.de/tutorials_howtos/5778-empfehlung-1und1-opcode-caching-einschalten/). Diese Änderung gab bei mir knapp 50 % mehr Geschwindigkeit.

## jweiland.net

jweiland.net ist seit Unternehmensgründung in 2003 rein auf TYPO3 spezialisiert und tritt im Rahmen des TYPO3 Camps Stuttgart sogar selbst als Veranstalter auf. Darüber hinaus sponsert der Inhaber Jochen Weiland diverse TYPO3 Camps, teilweise auch das Barcamp in Stuttgart und ist auf vielen Camps persönlich anzutreffen. Treu deren Motto "We share Know-how" stellt jweiland.net nahezu alle programmierten TYPO3 Extensions kostenfrei der Community zur Verfügung und die Webseite beinhaltet ein großes Sortiment an TYPO3 Artikeln.

Seit 2020 setzt jweiland.net auf ausfallsichere Cloud Server provided by AWS. Falls wer bei "AWS" kurz zusammenschreckt: Keine Sorge, eure Daten und die Server sind und bleiben in Deutschland. Die Tarife beginnen mit dem Cloud BASIS Paket, das ihr für 22 EUR/Monat bekommt (Stand: 13.07.2023). Darin enthalten sind 30 GB super schneller NVMe SSD Speicher, PHP 5.6 - 8.2, MySQL 5.6, MariaDB 10.3, kostenlose Let's Encrypt SSL Zertifikate und unlimited Traffic. Die PHP Version lässt sich pro Domain und sogar pro Sub-Domain individuell einstellen. Mit ein paar Handgriffen könnt ihr sogar die PHP Version auf der Shell konfigurieren.

Programme wie SSH, GIT, Composer, Rsync, ImageMagick und GraphicsMagick stehen euch und TYPO3 zur Verfügung. Als professioneller TYPO3 Hoster dürfen CronJobs nicht fehlen. Diese lassen sich minütlich ausführen und können Befehle ausführen, eine URL abrufen oder auch PHP-Skripte ausführen.

Ich empfinde die Informationen zu den Tarifen und im Bestellprozess sehr dürftig. Wie viel RAM stehen in meinem gewählten Tarif max. zur Verfügung? Wie viel RAM stehen jedem PHP Prozess zur Verfügung? Was bedeutet 100 % Leistung? Kann ich die php.ini individuell bearbeiten? Wie viele CronJobs, MySQL/MariaDB Datenbanken und Subdomains kann ich anlegen? Ist eine de-Domain kostenfrei enthalten? Steht OpCache und APCu zur Verfügung? Wie sind die Supportzeiten? Ist ein DDOS Schutz vorhanden? Können DNS Einstellungen selbst verwaltet werden?

Erst nach der Bestellungen kommt man dann an weitere Informationen dran: Über das Plesk Modul "Ressourcennutzung" sieht man eine Beschränkung des Tarifes auf 2 GB Arbeitsspeicher und eine Beschränkung der SSD Zugriffe auf 1024 IOPS. DNS Server-Einstellungen lassen sich ändern und hinzufügen. Es kann eine kleine Auswahl der wichtigsten php.ini Werten wie max_execution_time, memory_limit, enable_opcache und open_basedir eingestellt werden.

Wer mehr Leistung braucht kann nahtlos in den Cloud PREMIUM Tarif für 33 EUR/Monat (Stand: 13.07.2023) mit dann 4 GB RAM und 200% Leistung oder in den Tarif Cloud POWER für 88 EUR/Monat (Stand: 13.07.2023) mit dann 400% Leistung wechseln. Der Vorteil der größeren Tarife ist der zusätzlich zur Verfügung gestellte Solr-Kern, den man beim Mitbewerb häufig zusätzlich bezählen muss.

## Domainfactory

Über Domainfactory kann ich persönlich wenig schreiben. Viele meiner TYPO3-Kollegen schwören auf die Server von Domainfactory und auch die Server von jweiland.net sind bei Domainfactory gehostet.

Falls jemand mehr Erfahrung im Bereich Webhosting bei Domainfactory hat, kann er mich gerne über mein Kontaktformular erreichen.

Ich habe Domainfactory mal angerufen und in Erfahrung bringen können, dass TYPO3 ab den [ManagedHosting Pro-Tarifen](https://order.df.eu/germany/bestellung.php?clearsession=1&globalaction=hosting&addtarif2wk=df-11-1-managedhosting-pro-min&action=reconfigure) läuft. Domainfactory empfiehlt dieses Paket über "Anpassen" auf "3-Sterne-Performance" hochzurüsten. Damit liegen wir bei einem monatlichen Preis von 24,95 € (Stand 12.08.2012). Das erscheint auf den ersten Blick extremst hoch, aber in diesem Paket sind 50GB Webspace, 50GB Mailspace, 1000 MySQL-Datenbanken und ein SSD-Cache enthalten mit dem die Webseite nochmal so richtig Schub bekommt.

Der Support war sehr ruhig, gelassen und ist intensiv auf meine Fragen eingegangen.

Folgende Information wurde mir von einem TYPO3-Experten eingesendet:

Persönlich bin ich sehr begeistert davon, topp zufrieden (egal ob mit Shared Hosting oder Managed Servern). Sehr schnell und zuverlässig, *sehr* seltene Ausfälle, dann aber offene Kommunikation über status.df.eu. Support ist tagsüber kostenlos per Telefon erreichbar, Nachts per Mail - sehr schnell und kompetent.

## Mittwald

Mittwald ist von Beginn an treuer Begleiter und auch Sponsor vieler TYPO3 Camps und hat seine Arme vor ein paar Jahren sehr weit ausgebreitet und bietet nun nicht mehr nur Hosting für TYPO3, sondern auch Contao, Shopware, Drupal, Matomo, Joomla und vielem mehr an.

Im Februar 2019 versuchte Mittwald mit "Spaces" den ersten Schritt in die Cloud. So schnell, wie das Produkt auf den den Markt kam, so verschwand es auch wieder. Seit Mitte 2023 stehen neue Produkte im Regal, die sich an den alten Produktnamen anlehnen: proSpace und Space-Server. Dabei handelt es sich um eine eigens in Deutschland, auf eigenen Servern gehostete Kubernetes Infrastruktur mit CEPH Storage-Cluster. Grob gesagt: Euer Projekt liegt in einem eigenen Container, abgeschnitten von anderen Kunden, die Euch die Performance klauen. Mittwald treibt es damit sogar soweit, dass selbst die Datenbank in einem eigenen Container, mit dem von MySQL bevorzugten XFS Dateisystem zur Verfügung gestellt wird (Ausnahme ist hier der proSpace lite). Diese Trennung von Services hat enorme Performancegewinne gegenüber Hostern, wo der Webserver und die Datenbank auf ein und derselben Partition (Festplatte) liegen.

Der proSpace lite fängt mit 10 EUR/Monat ohne USt. (Stand: 12. Juli 2023) an und bietet in der minimalen Konfiguration 1 vCPU, 1 GB RAM und 20 GB Festplatte. Die gleichzeitigen PHP-FPM Prozesse sind hier auf 20 limitiert (über den Support änderbar). Bei 1 GB RAM stehen somit theoretisch pro PHP Prozess gerade mal 50 MB zur Verfügung. Das reicht für wirklich kleine Seiten ohne viel Schnickschnack. Wenn man bedenkt, dass TYPO3 256 MB pro PHP Prozess empfiehlt, wäre bei 4 gleichzeitigen Anfragen dieser Tarif theoretisch schon zu 100% ausgereizt.

Wenn ihr merkt, dass die Leistung für Euren Tarif nicht ausreicht, könnt ihr jederzeit die Anzahl vCPU in Verbindung mit mehr RAM hinzubuchen. So kosten 2 vCPU, 4 GB RAM und 60 GB Festplatte dann 30 EUR/Monat ohne USt (Stand: 12. Juli 2023). Dieser Wechsel erfolgt völlig nahtlos und ohne Downtime.

Wenn ihr jedoch noch mehr Leistung braucht, solltet ihr zu den proSpace Tarifen (ohne lite) greifen. 4 vCPU, 16 GB RAM und 260 GB Speicher liegen hier jedoch schon bei 126 EUR/Monat ohne Ust. (Stand: 12. Juli 2023).

Alle proSpace Tarife bieten von Haus aus schon Let's Encrypt SSL Zertifikate, DDOS Erkennung, unlimited Traffic, OpCache, APCu, unlimited CronJobs (konfigurierbar bis auf die Minute) und vieles mehr an. Alte PHP Versionen kleiner 7.4 und MySQL Versionen kleiner 5.6 werden jedoch nicht mehr unterstützt. Der Support steht Euch telefonisch, wie auch per Mail 24/7 zur Verfügung. Achtet bitte darauf, dass die Preise netto sind und dass alle Daten von Backups, Email, Webspeicher und Datenbank in Eurer gewählten Speichergröße Platz finden müssen.

Ich persönlich finde die Wortwahl 1 vCPU etwas irreführend. Habe ich hier zu einen gewissen prozentualen Rahmen Zugriff auf alle Kerne einer CPU mit 8, 16 oder gar 32 Kernen? Oder ist hier eher 1 vCore gemeint? Habe ich bei 1 vCore 2 Kerne, wegen dem Hyperthreading? Ich kann bei den Space-Servern dedizierte Resourcen einkaufen, jedoch bleibt die Eingabemaske still bei virtuellen CPUs. MariaDB und PostgreSQL stehen nicht zur Verfügung und Solr muss über ein eigenes Paket hinzugebucht werden. Es steht nur der Apache Webserver zur Verfügung. Nginx ist in der [Roadmap](https://www.mittwald.de/roadmap) (Stand: 12. Juli 2023) noch nicht gelistet.

## Hetzner

Ohne viel Werbung machen zu wollen, weiß wohl mittlerweile jeder Webadministrator, dass die Server von Hetzner wirklich super und echt schnell sind. Ich weiß nicht seit wann, aber grade heute (Stand: 20. Juli 2010) habe ich gesehen, dass Hetzner seine Webpakete TYPO3-fähig gemacht hat.

Das günstigste [Angebot](http://www.hetzner.de/de/hosting/produkte_webspace/level4/) liegt bei 4,90 € pro Monat (Stand: 20. Juli 2010). Allerdings vermisse ich hier wenigstens einen Cronjob, um die neue Systemextension "scheduler" ans Laufen zu bekommen. Diese Anfrage habe ich gerade an den Support gesenden. Bin mal auf die Antwort gespannt.

Wer den CronJob auf jeden Fall benötigt muss auf das teurere Paket "Level 9" wechseln. Dort sind 10 CronJobs enthalten und das Paket ist für 9,90 € zu haben.
Da alle in meinem Kunden und Bekanntenkreis direkt die Managed Server haben, kann ich hier nicht sagen, wie gut oder wie schnell die Webpaket sind. Auch Fragen bzgl. TYPO3 werden hier als reiner Provider wohl eher nicht geklärt werden können.

Update (Stand: 20. Juli 2010):

Ich habe heute folgende Antwort erhalten:

> Wenn Sie die 'scheduler' als Extension nutzen möchten, so ist dies kein Paket der Standard Installation. Daher müssten Sie dafür auf den nächst höheren Accounttyp wechseln

Naja, vielleicht ist bei denen noch nicht die Info angekommen, dass der scheduler seit 4.3.0 mit in die Systemextensions aufgenommen wurde.

## HostEurope

Ich war wirklich sehr lange Kunde von Hosteurope. Damals (wirklich damals) war Hosteurope mit der erste Hostinganbieter, der auch das IMAP für eMail-Konten anbietete. Das Paket: [WebPack L](http://www.hosteurope.de/produkte/WebPack) ist immer weiter entwickelt worden und kostete über Jahre hinweg immer 4,99 € im Monat (Stand: 09.03.2010). Heute (Stand: 12.08.2012) kostet das Paket 5,99 € und von ursprünglichen 250 MB Webspace ist es bis heute auf stattliche 5GB angestiegen.

Ich habe den Vertrieb von HostEurope angerufen und mir eine Empfehlung geben lassen für ein TYPO3-Paket. Die Antwort fiel auf das oben beschriebene WebPack 4.0 L. Diese Antwort wunderte mich etwas, denn nach meinen Informationen ist auf diesen Paketen kein ImageMagick verfügbar und muss erst mithilfe der [statischen imagemagick-Dateien](http://lists.typo3.org/pipermail/typo3-german/2010-January/065202.html) vom Kunden selbst hoch geladen und in TYPO3 konfiguriert werden. Auch ein CronJob für die Systemextension "scheduler" fehlt völlig. Als ich das angesprochen habe, wurden meine Befürchtungen bestätigt mit "Wenn Sie unbedingt ImageMagick brauchen, dann müssen Sie schon auf den [Virtual Managed Server L Basic](http://www.hosteurope.de/produkt/Virtual-Server-Managed-L) für 14,99 € (Stand: 12.08.2012) umsteigen".

Gerade heute (30.10.2012) habe ich wieder mit einem HostEurope Paket zu tun gehabt und festgestellt, dass in den WebPacks kein InnoDB möglich ist.
