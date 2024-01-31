+++
title = "Einbinden des RTEs"
linkTitle = "RTE einbinden"
date = 2024-01-09T20:51:29+01:00
aliases = ["formhandler-rte-einbinden.html"]
+++

Das ist derzeit noch keine schöne Lösung aber es geht.

### HTML

```html
<!-- ###TEMPLATE_FORM1### begin -->
###ERROR###
<div id="formhandler_contact_form">
    ###ADDITIONALJS_PRE###
    <form method="post" action="###REL_URL###" onsubmit="###ADDITIONALJS_SUBMIT###"> 
        <fieldset>
            <legend>###LLL:legend###</legend>
            <input type="hidden" name="formhandler[submitted]" value="1" />
            <div class="row"> 
                ###error_firstname###
                <label for="firstname">###LLL:firstname### ###required_firstname###</label>
                <input type="text" name="formhandler[firstname]" id="firstname" value="###value_firstname###"/> 
                <div class="clear"></div>
            </div>
            <div class="row">
                ###error_lastname### 
                <label for="lastname">###LLL:lastname### ###required_lastname###</label>
                <input type="text" name="formhandler[lastname]" id="lastname" value="###value_lastname###"/>
                <div class="clear"></div>
            </div>
            <div class="row"> 
                ###error_email### 
                <label for="email">###LLL:email### ###required_email###</label>
                <input type="text" name="formhandler[email]" id="email" value="###value_email###"/>
                <div class="clear"></div>
            </div>
            <div class="row">###FORM_RTE_ENTRY###</div>
            <div class="row">
                <p>###LLL:required_fields###</p>
                <input type="submit" value="###LLL:submit###" ###submit_nextStep###/>
            </div>
        </fieldset>
    </form>
    ###ADDITIONALJS_POST###
</div>

<!-- ###TEMPLATE_FORM1### end -->
<!-- ###TEMPLATE_EMAIL_USER_PLAIN### begin -->
Einen freundlichen Gruß an ###value_firstname### ###value_lastname###, Sie haben soeben das Kontaktformular unserer Website ausgefüllt. Vielen Dank für Ihre Anfrage. Wir werden uns zeitnah bei ihnen melden. Alle besten Wünsche! Ihr Obere-Nahe-Webteam
<!-- ###TEMPLATE_EMAIL_USER_PLAIN### end -->
<!-- ###TEMPLATE_EMAIL_USER_HTML### begin -->
<p>Einen freundlichen Gruß an ###value_firstname### ###value_lastname###,</p>
<p>Sie haben soeben das Kontaktformular unserer Website ausgefüllt. Vielen Dank für ihre Anfrage. Wir werden uns zeitnah bei Ihnen melden.</p>
<p>
    Alle besten Wünsche!<br />
    Ihr Obere-Nahe-Webteam
</p>
<!-- ###TEMPLATE_EMAIL_USER_HTML### end -->
<!-- ###TEMPLATE_EMAIL_ADMIN_PLAIN### begin -->
Hallo Admin, ###value_firstname### ###value_lastname### (###value_email###) hat soeben das Kontaktformular ausgefüllt. Folgende Nachricht wurde hinterlegt: ###value_text### Der Webmaster
<!-- ###TEMPLATE_EMAIL_ADMIN_PLAIN### end -->
<!-- ###TEMPLATE_EMAIL_ADMIN_HTML### begin -->
<p>Hallo Admin,</p>
<p>###value_firstname### ###value_lastname### (###value_email###) hat soeben das Kontaktformular ausgefüllt.</p>
<p>
    Folgende Nachricht wurde hinterlegt:<br>
    ###value_text###
</p>
<p>Der Webmaster</p>
<!-- ###TEMPLATE_EMAIL_ADMIN_HTML### end -->
<!-- ###TEMPLATE_SUBMITTEDOK### begin -->
<table>
    <tr>
        <td>###LLL:firstname###</td>
        <td>###value_firstname###</td>
    </tr> 
    <tr>
        <td>###LLL:lastname###</td> 
        <td>###value_lastname###</td>
    </tr>
    <tr>
        <td>###LLL:email###</td> 
        <td>###value_email###</td>
    </tr> 
    <tr>
        <td>###LLL:text###</td> 
        <td>###value_text###</td>
    </tr>
</table>
<!-- ###TEMPLATE_SUBMITTEDOK### end -->
```

### TypoScript

```typo3_typoscript
includeLibs.rte = fileadmin/libs/rte.php
plugin.Tx_Formhandler.settings.predef.singlestep_example {
	# Common configuration
	name = Kontaktformular
	debug = 0
	addErrorAnchors = 1
	markers.ADDITIONALJS_PRE = USER
	markers.ADDITIONALJS_PRE.userFunc = user_rte->insertRTE
	markers.ADDITIONALJS_PRE.markerName = additionaljs_pre
	markers.ADDITIONALJS_POST = USER
	markers.ADDITIONALJS_POST.userFunc = user_rte->insertRTE
	markers.ADDITIONALJS_POST.markerName = additionaljs_post
	markers.ADDITIONALJS_SUBMIT = USER
	markers.ADDITIONALJS_SUBMIT.userFunc = user_rte->insertRTE
	markers.ADDITIONALJS_SUBMIT.markerName = additionaljs_submit
	markers.FORM_RTE_ENTRY = USER
	markers.FORM_RTE_ENTRY.userFunc = user_rte->insertRTE
	markers.FORM_RTE_ENTRY.markerName = form_rte_entry

	templateFile = fileadmin/templates/formhandler/contact.html
	langFile.1 = fileadmin/templates/formhandler/contact.xml
	cssFile.1 = fileadmin/templates/formhandler/contact.css
	formValuesPrefix = formhandler

	# HTML wrapping by validation error
	errorListTemplate {
		totalWrap = <ul>|</ul>
		singleWrap = <li class="error">|</li>
	}
	singleErrorTemplate {
		singleWrap = <span class="error">|</span><br />
	}

	# Validators configuration
	validators {
		1 {
			class = Tx_Formhandler_Validator_Default
			config {
				fieldConf {
					firstname {
						errorCheck.1 = required
						errorCheck.2 = maxLength
						errorCheck.2.value = 50
					}
					lastname {
						errorCheck.1 = required
						errorCheck.2 = maxLength
						errorCheck.2.value = 50
					}
					email {
						errorCheck.1 = required
						errorCheck.2 = email
					}
				}
			}
		}
	}

	# Finishers configuration
	finishers {
		1.class = Tx_Formhandler_Finisher_Mail
		1.config {
			limitMailsToUser = 5
			admin {
				to_email = c.hess@alsbald.com
				to_name = Carsten Heß
				subject = Kontaktformular Obere-Nahe
				sender_email = webmaster@obere-nahe.de
				sender_name = Webmaster
				htmlEmailAsAttachment = 0
			}
			user {
				to_email = email
				to_name = lastname
				subject = Ihre Anfrage von Obere-Nahe
				sender_email = webmaster@obere-nahe.de
				sender_name = Webmaster
				replyto_email = firma@sfroemken.de
				replyto_name = TEXT
				replyto_name.value = Stefan Frömken
				htmlEmailAsAttachment = 0
		 	}
		}
		2.class = Tx_Formhandler_Finisher_Mail
		2.config {
			admin {
				to_email = firma@sfroemken.de
				to_name = Stefan Frömken
				subject = Kontaktformular Obere-Nahe
				sender_email = webmaster@obere-nahe.de
				sender_name = Webmaster
				htmlEmailAsAttachment = 0
			}
		}
		3.class = Tx_Formhandler_Finisher_Redirect
		3.config {
			redirectPage = 11
			correctRedirectUrl = 0
		}
	}
}
```

### rte.php:

```php
<?php
require_once(t3lib_extMgm::extPath('rtehtmlarea') . 'pi2/class.tx_rtehtmlarea_pi2.php');
class user_rte {
	var $RTEObj;
}
var $docLarge = 0;
var $RTEcounter = 0;
var $formName;
var $additionalJS_initial = '';
// Initial JavaScript to be printed before the form (should be in head, but cannot due to IE6 timing bug)
var $additionalJS_pre = array();
// Additional JavaScript to be printed before the form (works in Mozilla/Firefox when included in head, but not in IE6)
var $additionalJS_post = array();
// Additional JavaScript to be printed after the form
var $additionalJS_submit = array();
// Additional JavaScript to be executed on submit
var $PA = array(
    'itemFormElName' => '',
    'itemFormElValue' => '',
);
var $specConf = array(
    'rte_transform' => array(
        'parameters' => array('mode' => 'ts_css')
    )
);
var $thisConfig = array();
var $RTEtypeVal = 'text';
var $thePidValue;
public function insertRTE($content, $conf) {
    $this->postvars = t3lib_div::_POST('formhandler');
    if (!$this->RTEObj)
        $this->RTEObj = t3lib_div::makeInstance('tx_rtehtmlarea_pi2');
        if ($this->RTEObj->isAvailable()) {
            $this->RTEcounter++;
            $this->table = 'tx_kesearch_index';
            $this->field = 'content';
            $this->formName = 'formhandler-contact';
            $this->PA['itemFormElName'] = 'tx_kesearch_index[content]';
            $this->PA['itemFormElValue'] = $this->postvars['content'];
            $this->thePidValue = $GLOBALS['TSFE']->id;
            $RTEItem = $this->RTEObj->drawRTE($this, 'tx_kesearch_index', 'content', $row=array(), $this->PA, $this->specConf, $this->thisConfig, $this->RTEtypeVal, '', $this->thePidValue);
            switch($conf['markerName']) {
                case 'additionaljs_pre':
                    $marker = $this->additionalJS_initial.'
                        <script type="text/javascript">' . implode(chr(10), $this->additionalJS_pre).'
                        </script>';
                    break;
                case 'additionaljs_post':
                    $marker = '
                        <script type="text/javascript">' . implode(chr(10), $this->additionalJS_post).'
                        </script>';
                    break;
                case 'additionaljs_submit':
                    $marker = implode(';', $this->additionalJS_submit);
                    break;
                case 'form_rte_entry':
                    $marker = $RTEItem;
                    break;
            }
        }
        if ($this->RTEObj->isAvailable()) {
            $pageTSConfig = $GLOBALS['TSFE']->getPagesTSconfig();
            $RTEsetup = $pageTSConfig['RTE.'];
            $this->thisConfig = $RTEsetup['default.'];
            $this->thisConfig = $this->thisConfig['FE.'];
            $dataArray['content'] = $this->RTEObj->transformContent('db',$dataArray['content'], 'tx_kesearch_index', 'content', $dataArray, $this->specConf, $this->thisConfig, '', $this->thePidValue);
        }
        $insert = $GLOBALS['TYPO3_DB']->exec_INSERTquery('tx_kesearch_index', $dataArray);
        return $marker;
    }
}
```
