+++
title = "Fluid und Datenbank"
date = 2024-01-09T20:22:50+01:00
aliases = ["fluid-und-datenbank.html"]
+++

Dieses Tutorial beschreibt eine wirklich sehr einfache Datenbankanbindung, die sich zwar an die Beispielextension `blog_example` anlehnt, sich aber nicht an das DDD hält. Das hat einen Grund: Ich werde hier die Vorgehensweise so vor die Wand fahren, das ihr eigentlich das Plugin komplett neu schreiben müsstet. Ich hoffe, dass dadurch sehr deutlich wird, wie wichtig die Verzeichnisstruktur und Namensgebung in Extbase/Fluid-basierten Extension ist.

Wie ich schon im ersten Tutorial erwähnt habe, brauchen wir uns Dank Extbase nicht mehr groß um Datenbankabfragen und all dem Kram kümmern, dafür gibt es fertige Klassen, die die Arbeit für uns übernehmen. Eine solche Klasse ist zum Beispiel: Tx_Extbase_Persistence_Repository. Wenn ihr euch mal den Konstruktor dieser Klasse anschaut, werdet ihr ein queryFactory-Objekt finden. Dieses Objekt kann sich anhand unserer Extension- und Variablennamen einen Tabellennamen zusammenbauen, um dann auf die Daten zugreifen zu können.

Die Klasse Tx_Extbase_Persistence_Repository baut nicht die Verbindung zur Datenbank auf. Sie enthält nur Methoden, um Daten aufzulisten, zu bearbeiten, löschen und Datensätze zu finden. Die Verbindung zur Datenbank bauen andere Klassen innerhalb von Extbase auf.

## Inject Repository

In den früheren TYPO3-Versionen 4.3 und 4.4 mussten die Repositories über die Methoden `initializeAction` und t3lib_div::makeInstance im Controllerobjekt zur Verfügung gestellt werden. Das ist mit dem Erscheinen von TYPO3 4.5.* vorbei. Die initializeAction gibt es zwar immer noch, aber benötigte Objekte können nun nahezu automatisiert instanziiert werden. Dazu gibt es in Extbase den Objektmanager, der mithilfe von sogenannten ReflectionClasses eine Kopie unseres Controllerobjektes erzeugt und nun auslesen kann, welche Methoden in unserem Objekt vorhanden sind. Wenn eine Methode mit inject anfängt, dann wird überprüft, ob der erste Methodenparameter ein gültiger Klassenname ist. Wenn ja, wird von dieser Klasse ein Objekt instanziiert und an den Parameter zurückgegeben. Schauen wir uns eine solche Methode in unserer Controllerklasse mal an:

```php
<?php
class Tx_Sffluid_Controller_BlablaController extends Tx_Extbase_MVC_Controller_ActionController 
{
    protected $customerRepository;
    
    /**
     * @param Tx_Sffluid_Domain_Repository_CustomerRepository
     */
    public function injectCustomerRepository(Tx_Sffluid_Domain_Repository_CustomerRepository $customerRepository) 
    {
        $this->customerRepository = $customerRepository;
    }
    
    public function testblablaAction() 
    {
        $this->view->assign('customers', $this->customerRepository->findAll());
    }
}
```

Ihr seht: Der erste Parameter der inject-Methode heißt $customerRepository und ist vom Typ Tx_Sffluid_Domain_Repository_CustomerRepository. Wieso habe ich mich für dieses Namen entschieden?

## Der extbase_kickstarter bezeichnet die Dateien auch so :-)

Laut DDD wollen wir eine strikte Trennung zwischen Model, Repository, TS und vielen Anderen erreichen. Die Verzeichnisstruktur gibt vor, dass die Repositories hier zu liegen haben. Wir könnten die Klasse auch Tx_Sffluid_customerRepository bezeichnen, aber dann muss auch diese Datei entsprechend umbenannt werden und in das Rootverzeichnis von Sffluid verschoben werden, da der Objektmanager die Datei nicht finden und kein Objekt von der Klasse erzeugen kann. Dadurch würden wir aus dem Prinzip von DDD ausbrechen und eine unsaubere Extension programmieren.
Der Objektmanager erstellt nun aus diesem Typ/Klassennamen ein Objekt und übergibt es an $customerRepository. Dieses wiederum stellen wir mithilfe von $this allen Methoden innerhalb unseres Controllerobjekts zur Verfügung.

In unserer TestblablaAction fordern wir das Repository nun auf alle Daten mithilfe von findAll() aus der customer-Tabelle zu holen und übergeben diese an das Fluidtemplate.

Naja, schön wär's: Im Frontend erscheint diese Fehlermeldung:

```
Class Tx_Sffluid_Domain_Repository_CustomerRepository does not exist
```

Extbase hat versucht unseren Parametertypen aus der Injectmethode aufzulösen...aber hat die dazugehörige Datei nicht gefunden. Diese müssen wir erstmal anlegen.

typo3conf/ext/sffluid/Classes/Domain/Repository/CustomerRepository.php

```php
<?php
class Tx_Sffluid_Domain_Repository_CustomerRepository extends Tx_Extbase_Persistence_Repository 
{
}
```

Ein erneuter Blick ins Frontend zeigt uns noch immer unser "Hallo, wie geht's". Wir müssen jetzt noch unser Fluidtemplate anpassen, um die Daten anzeigen zu können.

## Änderung am Fluidtemplate

Lasst uns mal unser Template überarbeiten und im Frontend begutachten:

```html
Das sind meine Kunden:
{customers}
```

Und wieder werden wir mit einer Fehlermeldung begrüßt:

```
Cannot cast object of type "Tx_Extbase_Persistence_QueryResult" to string.
```

Hierzu muss man wissen, dass jede Zeile unserer Tabelle als Objekt übergeben wird. Da bei einer Datenbankabfrage auch mal mehrere Zeilen zurückgegeben werden können, sammelt Extbase diese Zeilenobjekte in einem sogenannten Objectstorage (Tx_Extbase_Persistence_QueryResult). Das ist grob gesagt eine Art Array mit den enthaltenen Zeilenobjekten. Denn dieses QueryResultobjekt kann innerhalb von Fluid auch wie ein Array behandelt werden und auch mit for-Schleifen durchlaufen werden. Bauen wir uns also mal eine For-Schleife in unser Template rein:

```html
Das sind meine Kunden:
<f:for each="{customers}" as="customer">
    {customer}
</f:for>
```

Na Happy Birthday, schon wieder eine Fehlermeldung:

```
Could not find class definition for name "Tx_Sffluid_Domain_Model_Customer". This could be caused by a mis-spelling of the class name in the class definition.
```

Kurz: Extbase konnte die Klasse mit dem Namen Tx_Sffluid_Domain_Model_Customer nicht finden. Entweder hat man sich verschrieben oder es gibt diese Klasse wirklich nicht. In unserem Fall gibt es sie nicht.

## Das Model

Ich sag mal so: Das Model beschreibt, ähnlich der TCA bei TYPO3, den Aufbau unserer customer-Tabelle. Wurde eine Tabellenspalte nicht im Model angegeben, ist diese auch nicht per Fluid und Controller erreichbar. Alle Tabellenspalten, die in Fluid und Controller erreichbar sein sollen MÜSSEN auch in der TCA definiert worden sein. Gerade so Tabellenspalten wie tstamp, crdate und so sind nicht in der TCA enthalten. Hier müsst ihr mithilfe der ext_tables die TCA selbst erweitern, um auf diese Tabellenspalten zugreifen zu können.

Anhand der Fehlermeldung oben wissen wir nun welche Datei fehlt und wo sie abzuspeichern ist. Legen wir also folgende Datei an:

typo3conf/ext/sffluid/Classes/Domain/Model/Customer.php

```php
<?php
class Tx_Sffluid_Domain_Model_Customer extends Tx_Extbase_DomainObject_AbstractEntity 
{
    /**
     * @var string $vorname
     */
    protected $vorname;
    
    /**
     * @var string $nachname
     */
    protected $nachname;
    
    /**
     * @return string vorname
     */
    public function getVorname() {
        return $this->vorname;
    }
    
    /**
     * @return string nachname
     */
    public function getNachname() {
        return $this->nachname;
    }
}
```

Hier muss für jede Tabellenspalte die Variable deklariert werden als protected und dazu mindestens die dazugehörige get-Methode. Nach dem get wird der nächste Buchstabe IMMER großgeschrieben. Solltet ihr in euren Spaltennamen Unterstriche verwendet haben, dann müssen diese in den get-Methoden entfernt werden und das erste Zeichen NACH dem Unterstrich auch wieder großgeschrieben werden. Beispiel: Spaltenname first_name wird zu getFirstName.

Später, wenn ihr Daten von z.B. Formularen auch wieder abspeichern wollt, dann benötigt ihr hier im Model auch set-Methoden, aber dafür gibt's ein eigenes Kapitel.

Speichern wir die Datei mal ab, löschen den Cache und schauen uns das Ergebnis im Frontend an:

```
#1247602160: Table 'typo3.tx_sffluid_domain_model_customer' doesn't exist: SELECT COUNT(*) FROM tx_sffluid_domain_model_customer
```

Ich weiß nicht, wie es euch geht, aber so nach und nach fangen die Fehlermeldungen an zu nerven, oder?

Ganz zu Anfang dieses Kapitels habe ich euch gesagt, dass ich dieses Projekt vor die Wand fahren werde und nun stehen wir genau da. Dadurch, dass wir unsere Tabellen mit dem alten kickstarter erstellt haben, wurden die Tabellennamen nicht nach den Vorstellung des DDD gewählt. DDD wünscht sich nun einen Tabellennamen ala tx_sffluid_domain_model_customer. Vom kickstarter wurde aber eine tx_sffluid_customer erstellt.

Folgende Möglichkeiten:

- Am besten wär's gewesen wir hätten die Tabelle mit dem extbase_builder erstellt. Dieser Builder erstellt automatisch Tabellen in diesem Format.
- Wir bauen uns die Tabellen selbst mithilfe der ext_tables.sql (nur was für Profis)
- Das hier ist nicht wirklich eine Lösung. Könnte zwar funktionieren, aber wer euch damit erwischt wird nur den Kopf schütteln und Selbstmord begehen: ihr benennt die Repository- und die Modelklasse um (auch die Dateien) und entfernt den Part mit _domain_model_ und _domain_repository_ und verschiebt diese beiden in das Rootverzeichnis von sffluid. In der Controllerklasse müsste noch der Typ angepasst werden. Wie schon gesagt: Für eine solche Lösung wird man erschossen :-)
- Diese Lösung hier werden wir anwenden: Extbase kann mithilfe von TypoScript gesagt werden, welcher Klassenname auf welche Tabelle gemapped werden soll. Kurz: Alle Anfragen an unsere Klasse Tx_Sffluid_Domain_Model_Customer können mithilfe des Mappings auf die Tabelle tx_sffluid_customer verlinkt werden.

## Mappen von Klassen zu Tabellen

Erstellt nun folgende Datei: `typo3conf/ext/sffluid/Configuration/TypoScript/setup.txt`

```typo3_typoscript
plugin.tx_sffluid {
  persistence {
    classes {
      Tx_Sffluid_Domain_Model_Customer {
        mapping {
          tableName = tx_sffluid_customer
        }
      }
    }
  }
}
```

Zusätzliche Zeile in ext_tables.php

```php
t3lib_extMgm::addStaticFile($_EXTKEY, 'Configuration/TypoScript', 'Sffluid table mapping');
```

Fügt nun in eurem Template das statische Template noch hinzu, löscht den Cache in schaut euch nochmals das Frontend an. Endlich mal keine Fehlermeldung mehr, aber Daten werden uns trotzdem nicht angezeigt.

## Abschließende Arbeiten

Erstellen wir nun einen Sysfolder, in dem wir 2-3 unserer Kundendatensätze anlegen und schauen uns nochmal das Frontend an. Wie irgendwie schon erwartet erscheinen unsere Daten nicht. Das liegt daran, dass im Hintergrund folgender SQL-Befehl ausgeführt wird:

```sql
SELECT tx_sffluid_customer.*
FROM tx_sffluid_customer
WHERE tx_sffluid_customer.deleted=0
AND tx_sffluid_customer.hidden=0
AND tx_sffluid_customer.pid IN (0)
```

Extbase sucht unsere Datensätze also auf der Rootseite. Ich denke bevor wir all unsere Datensätze verschieben, sollten wir lieber Extbase mitteilen, wo die Datensätze zu finden sind. Geht dazu in unser Plugin, klickt auf den Tab Verwalten und tragt dort unter Datensatzsammlung noch den Sysfolder ein und schaut euch nochmal das Frontend an:

```html
Das sind meine Kunden: Tx_Sffluid_Domain_Model_Customer:1 Tx_Sffluid_Domain_Model_Customer:2
```

Naja, nach Kundendaten sieht das ja nun nicht aus.

Ich hatte ja weiter oben erzählt, dass alle Tabellenzeilen als Objekt übergeben werden und in einem ObjectStorage abgespeichert werden. Dieses ObjectStorage durchlaufen wir Dank unserer for-Schleife im Template und übergeben jede Zeile an die Variable customer. Schaut ruhig nochmal ins Template rein. customer beinhaltet nun also ein komplettes Objekt, das eine Tabellenzeile widerspiegelt. Dank dieser Meldung hier oben sehen wir nun, dass ein Objekt der Klasse Tx_Sffluid_Domain_Model_Customer mit der UID 1 und 2 gefunden wurde.

Erinnert ihr euch noch wie wir dieser Klasse ein paar get-Methoden verpasst haben? Jede get-Methode kann innerhalb von Fluid mithilfe der Punktnotation aufgerufen werden. Ändern wir unser Template also nachmals ein wenig ab:

```html
Das sind meine Kunden:
<f:for each="{customers}" as="customer">
    {customer.vorname} {customer.nachname}
</f:for>
```
