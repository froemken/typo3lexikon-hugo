+++
title = "Crashkurs"
date = 2024-01-09T20:23:00+01:00
aliases = ["crashkurs.html"]
+++

Wie sagt man so schön: Quick&Dirty und ohne Warum und Aber. Ein ähnliches Beispiel mit vielen Erklärungen findet Ihr hier.

Ich habe Euch in diesem Crashkurs alle Namen, die in anderen Scripten auch wieder vorkommen, farblich hervorgehoben. Wenn also ein Controller Fluid in blau erscheint, dann ist auch der Verzeichnisname für's Template blau. So wisst Ihr immer welche Namen wo zusammen hängen.

1. Fluid und exbase im Extmanager aktivieren

2. Extension mit dem kickstarter erstellen. Kein Plugin anlegen. Evtl. noch die gewünschten Sprachen aktivieren. Extkey (z.B. sffluid) vergeben. Gewünschte Tabelle erstellen z.B. tx_sffluid_domain_model_fluid. Das domain_model sollte auf jeden Fall im Namen enthalten sein, sonst halten wir uns nicht an DDD und haben keine saubere Extension. Gewünschte Spalten anlegen z.B. firstname und lastname.

3. In ext_tables.php hinzufügen:

   ```php
   Tx_Extbase_Utility_Extension::registerPlugin(
       $_EXTKEY,
       'Pi1',
       'Mein erstes Fluid-Template'
   );
   ```

4. `ext_localconf.php` erstellen mit folgendem Inhalt:

   ```php
   <?php
   if (!defined ('TYPO3_MODE')) die ('Access denied.');

   Tx_Extbase_Utility_Extension::configurePlugin(
       $_EXTKEY,
       'Pi1',
       [
           'Fluid' => 'index',
       ]
   );
   ```

5. Folgende Ordnerstruktur anlegen:

   - Classes
   --- Controller
   --- Domain
   ----- Model
   ----- Repository
   - Resources
   --- Private
   ----- Templates
   ------- Fluid

6. Datei FluidController.php im Ordner Controller anlegen:

   ```php
   <?php
   class Tx_Sffluid_Controller_FluidController extends Tx_Extbase_MVC_Controller_ActionController 
   {
       protected $fluidRepository;

       public function initializeAction() 
       {
           $this->fluidRepository = t3lib_div::makeInstance('Tx_Sffluid_Domain_Repository_FluidRepository');
       }
    
       public function indexAction() 
       {
           $this->view->assign('fluid', $this->fluidRepository->findAll());
       }
   }
   ```

7. Datei FluidRepository.php im Ordner Repository anlegen:

   ```php
   <?php
   class Tx_Sffluid_Domain_Repository_FluidRepository extends Tx_Extbase_Persistence_Repository 
   {
   }
   ```

8. Datei Fluid.php im Ordner Model anlegen. Hier für jede Tabellenspalte eine get-Funktion erzeugen:

   ```php
   <?php
   class Tx_Sffluid_Domain_Model_Fluid extends Tx_Extbase_DomainObject_AbstractEntity 
   {
       protected $firstname;
       protected $lastname;
   
       public function getFirstname() {
           return $this->firstname;
       }
   
       public function getLastname() {
           return $this->lastname;
       }
   }
   ```

9. index.html im Ordner Fluid erstellen:

   ```html
   Das sind meine Kunden

   <f:for each="{fluid}" as="daten">
       {daten.firstname} {daten.lastname}
   </f:for>
   ```

10. Einen Sysfolder anlegen. UID merken.

11. Clear Cache (Alle Caches löschen)

12. Seite mit unserem Plugin erstellen und im Ausgangspunkt den Sysfolder von Punkt 10 eintragen. Speichern. Clear Cache

13. Fertig (siehe Frontend)
