+++
title = "Zugriff auf fe_users"
date = 2024-01-09T20:23:43+01:00
aliases = ["zugriff-auf-fe-users.html", "zugriff-auf-die-fe-users.html"]
+++

Wenn wir mit Extbase auf die `fe_users` Tabelle von TYPO3 zugreifen wollen, dann bringt dieses Framework ein entsprechendes Model und Repository mit. Neben dem Model für `fe_users` ist auch ein Model und ein Repository für die `fe_groups` vorhanden.

Die Dateien findet ihr in dem Verzeichnis: `typo3/sysext/extbase/Classes/Domain` und dann jeweils Model und Repository.

In unserem Beispiel wollen wir alle User mit dem jeweiligen username und name in einer Tabelle auflisten. Da ein Model und ein Repository bereits Dank `extbase` vorhanden sind benötigen wir diese beiden Ordner nicht. Es sei denn wir hätten in unserer Extension noch andere Tabellen.

Wie immer fangen wir in der `ext_localconf.php` an und definieren unseren Controller User und die Action list:

```php
<?php
if (!defined ('TYPO3_MODE')) die ('Access denied.');

Tx_Extbase_Utility_Extension::configurePlugin(
    $_EXTKEY,
    'Pi1',
    [
        'User' => 'list',
    ]
);
```

Dann geht's weiter in unseren Controller. Wir haben in den früheren Tutorials gelernt, dass der Dateiname des Controllers auch immer den Namen des Controllers enthalten muss. Wir erstellen und öffnen also die `UserController.php`. Im Gegensatz zu den vorigen Tutorials verweisen wir nun nicht auf ein Repository in unserer eigenen Extension, sondern geben hier bei `initializeAction`, das Repository aus der Extbase-Extension an. Die Datei könnte wie folgt aussehen:

```php
<?php
class Tx_Sffluid_Controller_UserController extends Tx_Extbase_MVC_Controller_ActionController 
{
    /**
     * @var Tx_Extbase_Domain_Repository_FrontendUserRepository
     */
    protected $userRepository;
    
    /**
     * Initializes the current action
     *
     * @return void
     */
    public function initializeAction() 
    {
        $this->userRepository = t3lib_div::makeInstance('Tx_Extbase_Domain_Repository_FrontendUserRepository');
    }
    
    /**
     * List action for this controller. Displays a list of users
     *
     * @return string The rendered view
     */
    public function listAction() 
    {
        $this->view->assign('users', $this->userRepository->findAll());
    }
}
```

Nun brauchen wir uns nur noch, um das Template zu kümmern. Zu finden unter `Resources/Private/Templates` und dann den Ordner mit unserem Controllernamen User anlegen. In der `ext_localconf.php` haben wir die Action `list` angegeben. Demnach müssen wir hier die Datei `List.html` anlegen.

Um eine tabellarische Liste zu erhalten habe ich diesen Code hier. Allerdings will ich hier auch auf das Thema wenn->dann->sonst eingehen und habe mir gedacht, wenn kein Name des Users hinterlegt ist, dann soll No name in der Tabelle erscheinen:

```html
<f:for each="{users}" as="user">
    <f:if condition="{user.name}">
        <f:then>
        </f:then>
        <f:else>
        </f:else>
    </f:if>
</f:for>

Username
Name of user

{user.username}{user.name}No Name
```

Vergesst anschließend nicht den Ausgangspunkt im Plugin selbst auf den Sysfolder der FE-Users zu setzen. Außerdem sollten schon ein paar Datensätze angelegt sein.

## fe_users und neue Felder

Durch die Installation von weiteren Extensions wie der `sr_feuser_register` werden der Tabelle `fe_users` schon mal weitere Felder hinzugefügt. In unserem Falle will ich hier nur auf die Felder `first_name` und `last_name` eingehen.

Die Verwendung der Tx_Extbase_Domain_Model_FrontendUser.php hilft uns nun nicht mehr, da dort diese Felder nicht enthalten sind.

Wir fangen wie immer erst mal mit der `ext_localconf.php` an:

```php
<?php
if (!defined ('TYPO3_MODE')) die ('Access denied.');

Tx_Extbase_Utility_Extension::configurePlugin(
    $_EXTKEY,
    'Pi1',
    [
        'User' => 'list',
    ]
);
```

Hier hat sich nichts geändert. Auch in der dazugehörenden UserController.php hat sich nur wenig geändert. Anstatt wie im vorigen Tutorial auf die Tx_Extbase_Domain_Model_FronendUser zu verweisen, müssen wir nun wieder auf ein Domainmodel verweisen, das sich innerhalb unserer Extension befindet:

```php
<?php
class Tx_Sffluid_Controller_UserController extends Tx_Extbase_MVC_Controller_ActionController 
{
    /**
     * @var Tx_Extbase_Domain_Repository_FrontendUserRepository
     */
    protected $userRepository;
    
    /**
     * Initializes the current action
     *
     * @return void
     */
    public function initializeAction() 
    {
        $this->userRepository = t3lib_div::makeInstance('Tx_Sffluid_Domain_Repository_UserRepository');
    }
    
    /**
     * List action for this controller. Displays a list of users
     *
     * @return string The rendered view
     */
    public function listAction() 
    {
        $this->view->assign('users', $this->userRepository->findAll());
    }
}
```

Könner werden jetzt sagen: "Moment mal, in den anderen Tutorials musste ich bei einem solchen Domainmodelnamen doch auch eine Tabelle mit dem gleichen Namen anlegen." Jup, da gebe ich euch erstmal recht, aber in Extbase gibt es die Möglichkeit einen Domainmodelnamen auf einen anderen Tabellennamen zu verlinken. Geht dazu mal in den Template->Objektbrowser und öffnet unter Selectbox=SETUP mal den Pfad: `config.tx_extbase.persistence.classes`. Hier seht ihr, dass schon zwei Klassen nämlich die für den fe_user und die für die fe_groups vorhanden sind. Am besten lässt sich solch ein Mapping im Info/modify-Bereich im Template-Setup realisieren:

```typo3_typoscript
config.tx_extbase.persistence.classes {
  Tx_Sffluid_Domain_Model_User {
    mapping {
      tableName = fe_users
    }
  }
}
```

Mit diesem TS habe ich unser User-Domainmodel mit der Tabelle fe_users verlinkt/gemapped.

Zurück in unserer Extension müssen wir die Verzeichnisse Model und Repository im Verzeichnis Classes/Domain anlegen. Im Verzeichnis Repository muss sich laut `ext_localconf.php` die Datei UserRepository.php befinden. Wie immer mit nur sehr wenig Inhalt:

```php
<?php
class Tx_Sffluid_Domain_Repository_UserRepository extends Tx_Extbase_Persistence_Repository 
{
}
```

Im Verzeichnis Model muss sich eine php-Datei mit dem Controllernamen User befinden: User.php:

```php
<?php
class Tx_Sffluid_Domain_Model_User extends Tx_Extbase_Domain_Model_FrontendUser 
{
    /**
     * @var string
     */
    protected $firstName;
    
    /**
     * @var string
     */
    protected $last_name;
    
    /**
     * Sets the firstName value
     *
     * @param string $firstName
     * @return void
     * @api
     */
    public function setFirstName($firstName) {
        $this->firstName = $firstName;
    }
    
    /**
     * Returns the firstName value
     *
     * @return string
     */
    public function getFirstName() {
        return $this->firstName;
    }
    
    /**
     * Sets the last_name value
     *
     * @param string $last_name
     * @return void
     */
    public function setLast_name($last_name) {
        $this->last_name = $last_name;
    }
    
    /**
     * Returns the last_name value
     *
     * @return string
     */
    public function getLast_name() {
        return $this->last_name;
    }
}
```

Ich habe in dieser Datei beide Tabellenspalten anders angegeben. Zum einen beinhalten unsere beiden Tabellenspalten einen Unterstrich first_name und last_name, also will ich hier im Script versuchen, den last_name mit der Funktion getLast_name zu holen. Auf der anderen Seite heißt es bei Extbase immer, man solle Variablennamen als UpperCamel definieren: Also jedes Wort wird am Anfang großgeschrieben. Demnach gibt es keine Unterstriche mehr und ich habe die Tabellenspalte first_name mal in dieses UpperCamelCase gebracht: FirstName.

Wir müssen nun noch unser Template anpassen und schon können wir feststellen welche Variante in der User.php richtig ist:

`Resources/Private/Templates/User/List.html`

```html
<f:for each="{users}" as="user">
    <f:if condition="{user.name}">
      <f:then>
        
      </f:then>
      <f:else>
        
      </f:else>
    </f:if>
    
</f:for>

Username
Name of user
Vorname
Nachname

    {user.username}{user.name}No Name{user.firstName}
    {user.last_name}
```
  
Nach dem Speichern und ClearCache könnt ihr nun sehr schön sehen, dass die UpperCamelCase-Variante gewonnen hat. Nachdem ihr die User.php und das Templatedatei entsprechend veränder habt, wird Euer Ergebnis so aussehen, wie oben rechts im Bild.
