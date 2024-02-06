+++
title = "Auszug TSFE"
date = 2024-01-09T20:58:10+01:00
aliases = ["auszug-tsfe.html"]
+++

Ich habe mir mal die Mühe gemacht dieses riesige Array hier auszugeben. Vielleicht wird einem dann klarer wie viele Daten pro Seitenaufruf verarbeitet werden. Ursprünglich hatte ich den Auszug hier komplett aufgelistet, aber teilweise wurde er auf Grund der Datenmenge nur noch zur Hälfte angezeigt. Ich habe den Auszug hier nun überarbeitet und ein paar größere Arrays entfernt.

```php
tslib_fe Object
(
 [id] => 34
 [type] => 0
 [idParts] => Array
 (
 [0] => 34
 )

 [cHash] => 
 [no_cache] => 
 [rootLine] => Array
 (
 [1] => Array
 (
 [pid] => 5
 [uid] => 34
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => stdWrap
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => 
 [storage_pid] => 0
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 
 [tx_templavoila_to] => 0
 [tx_templavoila_next_ds] => 
 [tx_templavoila_next_to] => 0
 )

 [0] => Array
 (
 [pid] => 0
 [uid] => 5
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => Stefan Test
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => mod.web_list.alternateBgColors = 1
mod.wizards.newContentElement.renderMode = tabs
 [storage_pid] => 6
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 2
 [tx_templavoila_to] => 2
 [tx_templavoila_next_ds] => 2
 [tx_templavoila_next_to] => 2
 )

 )

 [page] => Array
 (
 [uid] => 34
 [pid] => 5
 [t3ver_oid] => 0
 [t3ver_id] => 0
 [t3ver_wsid] => 0
 [t3ver_label] => 
 [t3ver_state] => 0
 [t3ver_stage] => 0
 [t3ver_count] => 0
 [t3ver_tstamp] => 0
 [t3ver_swapmode] => 0
 [t3ver_move_id] => 0
 [t3_origuid] => 0
 [tstamp] => 1265709791
 [sorting] => 512
 [deleted] => 0
 [perms_userid] => 2
 [perms_groupid] => 0
 [perms_user] => 31
 [perms_group] => 27
 [perms_everybody] => 0
 [editlock] => 0
 [crdate] => 1265707096
 [cruser_id] => 2
 [hidden] => 0
 [title] => stdWrap
 [doktype] => 1
 [TSconfig] => 
 [storage_pid] => 0
 [is_siteroot] => 0
 [php_tree_stop] => 0
 [tx_impexp_origuid] => 0
 [url] => 
 [starttime] => 0
 [endtime] => 0
 [urltype] => 1
 [shortcut] => 0
 [shortcut_mode] => 0
 [no_cache] => 0
 [fe_group] => 
 [subtitle] => 
 [layout] => 0
 [target] => 
 [media] => 
 [lastUpdated] => 0
 [keywords] => 
 [cache_timeout] => 0
 [newUntil] => 0
 [description] => 
 [no_search] => 0
 [SYS_LASTCHANGED] => 1265719637
 [abstract] => 
 [module] => 
 [extendToSubpages] => 0
 [author] => 
 [author_email] => 
 [nav_title] => 
 [nav_hide] => 0
 [content_from_pid] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [alias] => 
 [l18n_cfg] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 
 [tx_templavoila_to] => 0
 [tx_templavoila_next_ds] => 
 [tx_templavoila_next_to] => 0
 [tx_templavoila_flex] => <?xml version="1.0" encoding="utf-8" standalone="yes" ?>blabla
 )

 [contentPid] => 34
 [originalShortcutPage:protected] => 
 [sys_page] => t3lib_pageSelect Object
 (
 [urltypes] => Array
 (
 [0] => 
 [1] => http://
 [2] => ftp://
 [3] => mailto:
 [4] => https://
 )

 [where_hid_del] => AND pages.deleted=0 AND pages.hidden=0 AND pages.starttime<=1265720340 AND (pages.endtime=0 OR pages.endtime>1265720340) AND NOT pages.t3ver_state>0 AND pages.doktype<200
 [where_groupAccess] => AND (pages.fe_group='' OR pages.fe_group IS NULL OR pages.fe_group='0' OR (pages.fe_group LIKE '%,0,%' OR pages.fe_group LIKE '0,%' OR pages.fe_group LIKE '%,0' OR pages.fe_group='0') OR (pages.fe_group LIKE '%,-1,%' OR pages.fe_group LIKE '-1,%' OR pages.fe_group LIKE '%,-1' OR pages.fe_group='-1'))
 [sys_language_uid] => 0
 [versioningPreview] => 
 [versioningWorkspaceId] => 0
 [workspaceCache] => Array
 (
 )

 [error_getRootLine] => 
 [error_getRootLine_failPid] => 0
 )

 [jumpurl] => 
 [pageNotFound] => 0
 [domainStartPage] => 

 [MP] => 
 [RDCT] => 
 [page_cache_reg1] => 0
 [siteScript] => index.php?id=34
 [fe_user] => tslib_feUserAuth Object
 (
 [session_table] => fe_sessions
 [name] => fe_typo_user
 [get_name] => ftu
 [user_table] => fe_users
 [username_column] => username
 [userident_column] => password
 [userid_column] => uid
 [lastLogin_column] => lastlogin
 [enablecolumns] => Array
 (
 [deleted] => deleted
 [disabled] => disable
 [starttime] => starttime
 [endtime] => endtime
 )

 [formfield_uname] => user
 [formfield_uident] => pass
 [formfield_chalvalue] => challenge
 [formfield_status] => logintype
 [formfield_permanent] => permalogin
 [security_level] => normal
 [auth_include] => 
 [auth_timeout_field] => 6000
 [lifetime] => 0
 [sessionDataLifetime:protected] => 86400
 [sendNoCacheHeaders] => 0
 [getFallBack] => 1
 [getMethodEnabled] => 1
 [usergroup_column] => usergroup
 [usergroup_table] => fe_groups
 [groupData] => Array
 (
 )

 [TSdataArray] => Array
 (
 [0] => 
 )

 [userTS] => Array
 (
 )

 [userTSUpdated] => 0
 [showHiddenRecords] => 0
 [sesData] => Array
 (
 )

 [sesData_change] => 0
 [userData_change] => 0
 [sessionDataTimestamp:protected] => 
 [global_database] => 
 [gc_time] => 6000
 [gc_probability] => 1
 [writeStdLog] => 
 [writeAttemptLog] => 
 [hash_length] => 32
 [lockIP] => 2
 [lockHashKeyWords] => useragent
 [warningEmail] => 
 [warningPeriod] => 3600
 [warningMax] => 3
 [checkPid] => 1
 [checkPid_value] => 0
 [id] => 56427d924d4871e05bb89f9b617588f0
 [cookieId] => 94f4f4cca53b924984091b3c10560bc4
 [loginFailure] => 
 [loginSessionStarted] => 
 [get_URL_ID] => 
 [newSessionID] => 1
 [forceSetCookie] => 
 [dontSetCookie] => 
 [challengeStoredInCookie] => 
 [loginType] => FE
 [svConfig] => 
 [writeDevLog] => 
 [is_permanent] => 1
 [user] => 
 )

 [loginUser] => 0
 [gr_list] => 0,-1
 [beUserLogin] => 1
 [workspacePreview] => 
 [loginAllowedInBranch] => 1
 [loginAllowedInBranch_mode] => 
 [ADMCMD_preview_BEUSER_uid] => 0
 [fePreview] => 
 [showHiddenPage] => 0
 [showHiddenRecords] => 0
 [simUserGroup] => 0
 [TYPO3_CONF_VARS] => Array
 (
 [GFX] => Array
 (
 [image_processing] => 1
 [thumbnails] => 1
 [thumbnails_png] => 0
 [noIconProc] => 
 [gif_compress] => 1
 [imagefile_ext] => gif,jpg,jpeg,tif,bmp,pcx,tga,png,pdf,ai
 [gdlib] => 1
 [gdlib_png] => 1
 [gdlib_2] => 1
 [im] => 1
 [im_path] => /usr/X11R6/bin/
 [im_path_lzw] => /usr/bin/
 [im_version_5] => gm
 [im_negate_mask] => 0
 [im_imvMaskState] => 0
 [im_no_effects] => 1
 [im_v5effects] => -1
 [im_mask_temp_ext_gif] => 1
 [im_mask_temp_ext_noloss] => miff
 [im_noScaleUp] => 
 [im_combine_filename] => combine
 [im_noFramePrepended] => 
 [im_stripProfileCommand] => +profile '*'
 [im_useStripProfileByDefault] => 1
 [jpg_quality] => 100
 [enable_typo3temp_db_tracking] => 1
 [TTFLocaleConv] => 
 [TTFdpi] => 96
 [png_truecolor] => 1
 )

 [SYS] => Array
 (
 [sitename] => New TYPO3 site
 [compat_version] => 4.3
 [encryptionKey] => 37c675372ea9769ee082cc1dd13200703ef56971aa2845b93cf18a8b89cbf26ebf57f1b0d9542b5ecd54d4b9405043e6
 [cookieDomain] => 
 [cookieSecure] => 0
 [cookieHttpOnly] => 
 [doNotCheckReferer] => 
 [recursiveDomainSearch] => 
 [devIPmask] => 192.168.*.*
 [sqlDebug] => 1
 [enable_DLOG] => 
 [ddmmyy] => d-m-y
 [hhmm] => H:i
 [USdateFormat] => 
 [loginCopyrightWarrantyProvider] => 
 [loginCopyrightWarrantyURL] => 
 [loginCopyrightShowVersion] => 
 [curlUse] => 
 [curlProxyServer] => 
 [curlProxyTunnel] => 
 [curlProxyUserPass] => 
 [form_enctype] => multipart/form-data
 [textfile_ext] => txt,html,htm,css,tmpl,js,sql,xml,csv,php,php3,php4,php5,php6,phpsh,inc
 [contentTable] => tt_content
 [T3instID] => N/A
 [binPath] => 
 [binSetup] => 
 [t3lib_cs_convMethod] => 
 [t3lib_cs_utils] => 
 [no_pconnect] => 
 [multiplyDBfieldSize] => 1
 [setDBinit] => SET NAMES utf8;
SET SESSION character_set_server=utf8;
 [dbClientCompress] => 
 [setMemoryLimit] => 0
 [forceReturnPath] => 
 [serverTimeZone] => 1
 [systemLog] => 
 [systemLogLevel] => 0
 [enableDeprecationLog] => 1
 [maxFileNameLength] => 60
 [UTF8filesystem] => 1
 [lockingMode] => simple
 [reverseProxyIP] => 
 [reverseProxyHeaderMultiValue] => none
 [reverseProxyPrefix] => 
 [reverseProxySSL] => 
 [reverseProxyPrefixSSL] => 
 [useCachingFramework] => 
 [displayErrors] => 1
 [productionExceptionHandler] => t3lib_error_ProductionExceptionHandler
 [debugExceptionHandler] => t3lib_error_DebugExceptionHandler
 [errorHandler] => t3lib_error_ErrorHandler
 [errorHandlerErrors] => 6135
 [exceptionalErrors] => 4341
 [enable_errorDLOG] => 0
 [enable_exceptionDLOG] => 0
 [syslogErrorReporting] => 6135
 [belogErrorReporting] => 6135

 )

 [EXT] => Array
 (
 [noEdit] => 1
 [allowGlobalInstall] => 
 [allowLocalInstall] => 1
 [allowSystemInstall] => 
 [em_wsdlURL] => typo3.org/wsdl/tx_ter_wsdl.php
 [em_mirrorListURL] => repositories.typo3.org/mirrors.xml.gz
 [requiredExt] => cms,lang,sv
 [excludeForPackaging] => (CVS|\..*|.*~|.*\.bak)
 [extCache] => 1
 [extList] => extbase,css_styled_content,version,tsconfig_help,context_help,extra_page_cm_options,impexp,sys_note,tstemplate,tstemplate_ceditor,tstemplate_info,tstemplate_objbrowser,tstemplate_analyzer,func_wizards,wizard_crpages,wizard_sortpages,lowlevel,install,belog,beuser,aboutmodules,setup,taskcenter,info_pagetsconfig,viewpage,rtehtmlarea,t3skin,t3editor,reports,static_info_tables,recycler,scheduler,test_scheduler,indexed_search,about,cshmanual,opendocs,simulatestatic,sfjquery,mydashboard,sfwidgets,fluid,div2007,templavoila,t3quixplorer,security_check,kickstarter,sfpinger,sfstdwrap
 [extList_FE] => extbase,css_styled_content,install,rtehtmlarea,t3skin,static_info_tables,test_scheduler,indexed_search,simulatestatic,sfjquery,mydashboard,sfwidgets,fluid,div2007,templavoila,t3quixplorer,security_check,kickstarter,sfpinger,sfstdwrap
 [extConf] => Array
 (
 )

 )

 [BE] => Array
 (
 [unzip_path] => 
 [diff_path] => diff
 [fileadminDir] => fileadmin/
 [RTEenabled] => 1
 [RTE_imageStorageDir] => uploads/
 [staticFileEditPath] => fileadmin/static/
 [lockRootPath] => 
 [userHomePath] => 
 [groupHomePath] => 
 [userUploadDir] => 
 [fileCreateMask] => 0644
 [folderCreateMask] => 0755
 [createGroup] => 
 [warning_email_addr] => 
 [warning_mode] => 
 [lockIP] => 4
 [sessionTimeout] => 3600
 [IPmaskList] => 
 [lockBeUserToDBmounts] => 1
 [lockSSL] => 0
 [lockSSLPort] => 0
 [enabledBeUserIPLock] => 1
 [loginSecurityLevel] => 
 [showRefreshLoginPopup] => 
 [adminOnly] => 0
 [disable_exec_function] => 0
 [usePHPFileFunctions] => 1
 [compressionLevel] => 0
 [maxFileSize] => 10240
 [forceCharset] => utf-8
 [dontSetCookie] => 
 [additionalAbsRefPrefixDirectories] => 
 [IPmaskMountGroups] => Array
 (
 )

 [get_url_id_token] => #get_URL_ID_TOK#
 [content_doktypes] => 1,2,5,7
 [enable_mount_pids] => 1
 [pageOverlayFields] => uid,title,subtitle,nav_title,media,keywords,description,abstract,author,author_email,url,urltype,shortcut,shortcut_mode
 [hidePagesIfNotTranslatedByDefault] => 
 [eID_include] => Array
 (
 [tx_cms_showpic] => EXT:cms/tslib/showpic.php
 [tx_install_ajax] => EXT:install/mod/class.tx_install_ajax.php
 [rtehtmlarea_spellchecker] => EXT:rtehtmlarea/pi1/class.tx_rtehtmlarea_pi1.php
 [sfjquery-contact] => EXT:sfjquery/ajax/contact.php
 )

 [disableNoCacheParameter] => 
 [workspacePreviewLogoutTemplate] => 
 [XCLASS] => Array
 (
 )

 )


 [SC_OPTIONS] => Array
 (
 [GLOBAL] => Array
 (
 )

 [t3lib/class.t3lib_tsfebeuserauth.php] => Array
 (
 [frontendEditingController] => Array
 (
 [default] => t3lib/class.t3lib_frontendedit.php:t3lib_frontendedit
 )

 )

 [errors] => Array
 (
 [exceptionHandler] => t3lib_error_DebugExceptionHandler
 [exceptionalErrors] => 4341
 )

 [ext/install] => Array
 (
 [compat_version] => Array
 (
 )

 [update] => Array
 (
 )

 )

 [t3lib/class.t3lib_tcemain.php] => Array
 (
 [processDatamapClass] => Array
 (
 [0] => EXT:cms/tslib/hooks/class.tx_cms_treelistcacheupdate.php:&tx_cms_treelistCacheUpdate
 [tx_indexedsearch] => EXT:indexed_search/class.crawler.php:&tx_indexedsearch_crawler
 [templavoila] => EXT:templavoila/class.tx_templavoila_tcemain.php:tx_templavoila_tcemain
 )

 [processCmdmapClass] => Array
 (
 [0] => EXT:cms/tslib/hooks/class.tx_cms_treelistcacheupdate.php:&tx_cms_treelistCacheUpdate
 [tx_indexedsearch] => EXT:indexed_search/class.crawler.php:&tx_indexedsearch_crawler
 [templavoila] => EXT:templavoila/class.tx_templavoila_tcemain.php:tx_templavoila_tcemain
 )

 [moveRecordClass] => Array
 (
 [0] => EXT:cms/tslib/hooks/class.tx_cms_treelistcacheupdate.php:&tx_cms_treelistCacheUpdate
 [templavoila] => EXT:templavoila/class.tx_templavoila_tcemain.php:tx_templavoila_tcemain
 )

 )

 [typo3/index.php] => Array
 (
 [loginFormHook] => Array
 (
 [sv] => EXT:sv/class.tx_sv_loginformhook.php:tx_sv_loginformhook->getLoginFormTag
 )

 [loginScriptHook] => Array
 (
 [sv] => EXT:sv/class.tx_sv_loginformhook.php:tx_sv_loginformhook->getLoginScripts
 )

 )

 [additionalBackendItems] => Array
 (
 [cacheActions] => Array
 (
 [clearRTECache] => EXT:rtehtmlarea/hooks/clearrtecache/class.tx_rtehtmlarea_clearcachemenu.php:&tx_rtehtmlarea_clearcachemenu
 )

 )


 [tslib/class.tslib_fe.php] => Array
 (
 [pageIndexing] => Array
 (
 [0] => EXT:indexed_search/class.indexer.php:tx_indexedsearch_indexer
 )

 [headerNoCache] => Array
 (
 [tx_indexedsearch] => EXT:indexed_search/hooks/class.tx_indexedsearch_tslib_fe_hook.php:&tx_indexedsearch_tslib_fe_hook->headerNoCache
 )

 [configArrayPostProc] => Array
 (
 [simulatestatic] => EXT:simulatestatic/class.tx_simulatestatic.php:&tx_simulatestatic->hookInitConfig
 )

 [checkAlternativeIdMethods-PostProc] => Array
 (
 [simulatestatic] => EXT:simulatestatic/class.tx_simulatestatic.php:&tx_simulatestatic->hookCheckAlternativeIDMethods
 )

 )

 [t3lib/class.t3lib_tstemplate.php] => Array
 (
 [linkData-PostProc] => Array
 (
 [simulatestatic] => EXT:simulatestatic/class.tx_simulatestatic.php:&tx_simulatestatic->hookLinkDataPostProc
 )

 )

 [ext/mydashboard/class.tx_mydashboard_widgetmgm.php] => Array
 (
 [addWidget] => Array
 (
 [serverinfo] => EXT:mydashboard/widgets/class.tx_mydashboard_serverinfo.php:tx_mydashboard_serverinfo
 [userstats] => EXT:mydashboard/widgets/class.tx_mydashboard_userstats.php:tx_mydashboard_userstats
 [rssfeed] => EXT:mydashboard/widgets/class.tx_mydashboard_rssfeed.php:tx_mydashboard_rssfeed
 [sysnotepad] => EXT:mydashboard/widgets/class.tx_mydashboard_sysnotepad.php:tx_mydashboard_sysnotepad
 [latestnews] => EXT:mydashboard/widgets/class.tx_mydashboard_latestnews.php:tx_mydashboard_latestnews
 [sfhddinfo] => EXT:sfwidgets/widgets/class.tx_sfwidgets_hddinfo.php:tx_sfwidgets_hddinfo
 [sfresetqp] => EXT:sfwidgets/widgets/class.tx_sfwidgets_resetqp.php:tx_sfwidgets_resetqp
 )

 )

 [t3lib/class.t3lib_userauthgroup.php] => Array
 (
 [recordEditAccessInternals] => Array
 (
 [templavoila] => EXT:templavoila/class.tx_templavoila_access.php:&tx_templavoila_access->recordEditAccessInternals
 )

 )

 )

 [EXTCONF] => Array
 (
 [rtehtmlarea] => Array
 (
 [version] => 1.8.7
 [defaultConfiguration] => Typical
 [enableDebugMode] => 0
 [enableCompressedScripts] => 0
 [enableDAMBrowser] => 0
 [plugins] => Array
 (
 [StatusBar] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/StatusBar/class.tx_rtehtmlarea_statusbar.php:&tx_rtehtmlarea_statusbar
 )

 [EditorMode] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/EditorMode/class.tx_rtehtmlarea_editormode.php:&tx_rtehtmlarea_editormode
 )

 [DefaultInline] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/DefaultInline/class.tx_rtehtmlarea_defaultinline.php:&tx_rtehtmlarea_defaultinline
 [addIconsToSkin] => 1
 )

 [BlockElements] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/BlockElements/class.tx_rtehtmlarea_blockelements.php:&tx_rtehtmlarea_blockelements
 [addIconsToSkin] => 0
 )

 [DefinitionList] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/DefinitionList/class.tx_rtehtmlarea_definitionlist.php:&tx_rtehtmlarea_definitionlist
 [addIconsToSkin] => 0
 )

 [BlockStyle] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/BlockStyle/class.tx_rtehtmlarea_blockstyle.php:&tx_rtehtmlarea_blockstyle
 )

 [CharacterMap] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/CharacterMap/class.tx_rtehtmlarea_charactermap.php:&tx_rtehtmlarea_charactermap
 [addIconsToSkin] => 0
 )

 [Acronym] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/Acronym/class.tx_rtehtmlarea_acronym.php:&tx_rtehtmlarea_acronym
 [addIconsToSkin] => 0
 [disableInFE] => 1
 )

 [UserElements] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/UserElements/class.tx_rtehtmlarea_userelements.php:&tx_rtehtmlarea_userelements
 [addIconsToSkin] => 0
 [disableInFE] => 1
 )

 [TextStyle] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/TextStyle/class.tx_rtehtmlarea_textstyle.php:&tx_rtehtmlarea_textstyle
 )

 [DefaultLink] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/DefaultLink/class.tx_rtehtmlarea_defaultlink.php:&tx_rtehtmlarea_defaultlink
 [addIconsToSkin] => 0
 )

 [TYPO3Link] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/TYPO3Link/class.tx_rtehtmlarea_typo3link.php:&tx_rtehtmlarea_typo3link
 [addIconsToSkin] => 0
 [disableInFE] => 1
 )

 [InsertSmiley] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/InsertSmiley/class.tx_rtehtmlarea_insertsmiley.php:&tx_rtehtmlarea_insertsmiley
 [addIconsToSkin] => 0
 [disableInFE] => 0
 )

 [Language] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/Language/class.tx_rtehtmlarea_language.php:&tx_rtehtmlarea_language
 [addIconsToSkin] => 0
 [disableInFE] => 0
 )

 [SpellChecker] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/SpellChecker/class.tx_rtehtmlarea_spellchecker.php:&tx_rtehtmlarea_spellchecker
 [addIconsToSkin] => 0
 [disableInFE] => 0
 [AspellDirectory] => /usr/bin/aspell
 [noSpellCheckLanguages] => ja,km,ko,lo,th,zh,b5,gb
 [forceCommandMode] => 0
 [dictionaryList] => en
 [defaultDictionary] => en
 )

 [FindReplace] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/FindReplace/class.tx_rtehtmlarea_findreplace.php:&tx_rtehtmlarea_findreplace
 [addIconsToSkin] => 0
 [disableInFE] => 0
 )

 [RemoveFormat] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/RemoveFormat/class.tx_rtehtmlarea_removeformat.php:&tx_rtehtmlarea_removeformat
 [addIconsToSkin] => 0
 [disableInFE] => 0
 )

 [DefaultClean] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/DefaultClean/class.tx_rtehtmlarea_defaultclean.php:&tx_rtehtmlarea_defaultclean
 )

 [TYPO3HtmlParser] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/TYPO3HtmlParser/class.tx_rtehtmlarea_typo3htmlparser.php:&tx_rtehtmlarea_typo3htmlparser
 [disableInFE] => 1
 )

 [QuickTag] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/QuickTag/class.tx_rtehtmlarea_quicktag.php:&tx_rtehtmlarea_quicktag
 [addIconsToSkin] => 0
 [disableInFE] => 0
 )

 [TableOperations] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/TableOperations/class.tx_rtehtmlarea_tableoperations.php:&tx_rtehtmlarea_tableoperations
 [addIconsToSkin] => 0
 [disableInFE] => 0
 )

 [AboutEditor] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/AboutEditor/class.tx_rtehtmlarea_abouteditor.php:&tx_rtehtmlarea_abouteditor
 [addIconsToSkin] => 0
 [disableInFE] => 0
 )

 [ContextMenu] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/ContextMenu/class.tx_rtehtmlarea_contextmenu.php:&tx_rtehtmlarea_contextmenu
 [addIconsToSkin] => 0
 [disableInFE] => 0
 )

 [UndoRedo] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/UndoRedo/class.tx_rtehtmlarea_undoredo.php:&tx_rtehtmlarea_undoredo
 [addIconsToSkin] => 0
 [disableInFE] => 0
 )

 [CopyPaste] => Array
 (
 [objectReference] => EXT:rtehtmlarea/extensions/CopyPaste/class.tx_rtehtmlarea_copypaste.php:&tx_rtehtmlarea_copypaste
 [addIconsToSkin] => 0
 [disableInFE] => 0
 [mozillaAllowClipboardURL] => addons.mozilla.org/firefox/downloads/latest/852/addon-852-latest.xpi
 )

 )

 [enableImages] => 0
 [enableAccessibilityIcons] => 0
 [allowStyleAttribute] => 0
 )

 [static_info_tables] => Array
 (
 [charset] => utf-8
 [tables] => Array
 (
 [static_territories] => Array
 (
 [label_fields] => Array
 (
 [0] => tr_name_##
 [1] => tr_name_en
 )

 [isocode_field] => Array
 (
 [0] => tr_iso_##
 )

 )

 [static_countries] => Array
 (
 [label_fields] => Array
 (
 [0] => cn_short_##
 [1] => cn_short_en
 )

 [isocode_field] => Array
 (
 [0] => cn_iso_##
 )

 )

 [static_country_zones] => Array
 (
 [label_fields] => Array
 (
 [0] => zn_name_##
 [1] => zn_name_local
 )

 [isocode_field] => Array
 (
 [0] => zn_code
 [1] => zn_country_iso_##
 )

 )

 [static_languages] => Array
 (
 [label_fields] => Array
 (
 [0] => lg_name_##
 [1] => lg_name_en
 )

 [isocode_field] => Array
 (
 [0] => lg_iso_##
 [1] => lg_country_iso_##
 )

 )

 [static_currencies] => Array
 (
 [label_fields] => Array
 (
 [0] => cu_name_##
 [1] => cu_name_en
 )

 [isocode_field] => Array
 (
 [0] => cu_iso_##
 )

 )

 )

 )

 [crawler] => Array
 (
 [procInstructions] => Array
 (
 [tx_indexedsearch_reindex] => Re-indexing
 )

 [cli_hooks] => Array
 (
 [tx_indexedsearch_crawl] => EXT:indexed_search/class.crawler.php:&tx_indexedsearch_crawler
 )

 )

 [indexed_search] => Array
 (
 [external_parsers] => Array
 (
 [pdf] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [doc] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [pps] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [ppt] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [xls] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [sxc] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [sxi] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [sxw] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [ods] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [odp] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [odt] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [rtf] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [txt] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [html] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [htm] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [csv] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [xml] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [jpg] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [jpeg] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 [tif] => EXT:indexed_search/class.external_parser.php:&tx_indexed_search_extparse
 )

 )

 [lowlevel] => Array
 (
 [cleanerModules] => Array
 (
 [tx_templavoila_unusedce] => Array
 (
 [0] => EXT:templavoila/class.tx_templavoila_unusedce.php:tx_templavoila_unusedce
 )

 )

 )

 [l10nmgr] => Array
 (
 [indexFilter] => Array
 (
 [tx_templavoila_usedCE] => Array
 (
 [0] => EXT:templavoila/class.tx_templavoila_usedce.php:tx_templavoila_usedCE
 )

 )

 )

 [kickstarter] => Array
 (
 [sections] => Array
 (
 [emconf] => Array
 (
 [classname] => tx_kickstarter_section_emconf
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_emconf.php
 [title] => General info
 [description] => Enter general information about the extension here: Title, description, category, author...
 [singleItem] => 1
 )

 [languages] => Array
 (
 [classname] => tx_kickstarter_section_languages
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_languages.php
 [title] => Setup languages
 [description] => Select the system languages you want to use in your extension. English is TYPO3's default language, therefore you don't need to select it anymore.
 [singleItem] => 1
 )

 [tables] => Array
 (
 [classname] => tx_kickstarter_section_tables
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_tables.php
 [title] => New Database Tables
 [description] => Add database tables which can be edited inside the backend. These tables will be added to the global TCA array in TYPO3.
 [image] => EXT:kickstarter/icons/cm.png
 )

 [fields] => Array
 (
 [classname] => tx_kickstarter_section_fields
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_fields.php
 [title] => Extend existing Tables
 [description] => Add custom fields to existing tables, such as the "pages", "tt_content", "fe_users" or "be_users" table.
 )

 [pi] => Array
 (
 [classname] => tx_kickstarter_section_pi
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_pi.php
 [title] => Frontend Plugins
 [description] => Create frontend plugins. Plugins are web applications running on the website itself (not in the backend of TYPO3). The default guestbook, message board, shop, rating feature etc. are examples of plugins.
 )

 [module] => Array
 (
 [classname] => tx_kickstarter_section_module
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_module.php
 [title] => Backend Modules
 [description] => Create backend modules. A module is normally recognized as the application behind one of the TYPO3 backend menuitems. Examples are the Web>Page, Web>List, User>Setup, Doc module etc. In a more loose sense, all applications integrated with existing module (see below) also belongs to the "module" category.
 )

 [modulefunction] => Array
 (
 [classname] => tx_kickstarter_section_modulefunction
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_modulefunction.php
 [title] => Integrate in existing Modules
 [description] => Extend existing modules with new function-menu items. Examples are extensions such as "User>Task Center, Messaging" which adds internal messaging to TYPO3. Or "Web>Info, Page TSconfig" which shows the Page TSconfiguration for a page. Or "Web>Func, Wizards, Sort pages" which is a wizard for re-ordering pages in a folder.
 )

 [cm] => Array
 (
 [classname] => tx_kickstarter_section_cm
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_cm.php
 [title] => Clickmenu items
 [description] => Adds a custom item to the clickmenus of database records. This is a very cool way to integrate small tools of your own in an elegant way!
 )

 [sv] => Array
 (
 [classname] => tx_kickstarter_section_sv
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_sv.php
 [title] => Services
 [description] => Create a Services class. With a Services extension you can extend TYPO3 (or an extension which use Services) with functionality, without any changes to the code which use that service.
 )

 [ts] => Array
 (
 [classname] => tx_kickstarter_section_ts
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_ts.php
 [title] => Static TypoScript code
 [description] => Adds static TypoScript Setup and Constants code - just like a static template would do.
 )

 [tsconfig] => Array
 (
 [classname] => tx_kickstarter_section_tsconfig
 [filepath] => EXT:kickstarter/sections/class.tx_kickstarter_section_tsconfig.php
 [title] => TSconfig
 [description] => Adds default Page-TSconfig or User-TSconfig. Can be used to preset options inside TYPO3.
 [singleItem] => 1
 )

 )

 )

 )

 [SVCONF] => Array
 (
 )

 )

 [TCAcachedExtras] => Array
 (
 [pages] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [be_users] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [be_groups] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [sys_filemounts] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [sys_language] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [tt_content] => Array
 (
 [l10n_mode] => Array
 (
 [header] => prefixLangTitle
 [bodytext] => prefixLangTitle
 )

 )

 [fe_users] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [fe_groups] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [sys_domain] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [pages_language_overlay] => Array
 (
 [l10n_mode] => Array
 (
 [title] => prefixLangTitle
 )

 )

 [sys_template] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [static_template] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [tx_rtehtmlarea_acronym] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [static_territories] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [static_countries] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [static_country_zones] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [static_languages] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [static_currencies] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [tx_sfjquery_scripts] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [tx_templavoila_tmplobj] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 [tx_templavoila_datastructure] => Array
 (
 [l10n_mode] => Array
 (
 )

 )

 )

 [tmpl] => t3lib_TStemplate Object
 (
 [tt_track] => 1
 [forceTemplateParsing] => 
 [matchAlternative] => Array
 (
 )

 [matchAll] => 0
 [parseEditorCfgField] => 0
 [backend_info] => 0
 [getFileName_backPath] => 
 [ext_constants_BRP] => 0
 [ext_config_BRP] => 0
 [ext_editorcfg_BRP] => 0
 [ext_regLinenumbers] => 
 [ext_regComments] => 
 [uplPath] => uploads/tf/
 [tempPath] => typo3temp/
 [menuclasses] => gmenu,tmenu,imgmenu,jsmenu
 [whereClause] => AND deleted=0 AND hidden=0 AND (starttime<=1265720340) AND (endtime=0 OR endtime>1265720340)
 [debug] => 0
 [allowedPaths] => Array
 (
 [0] => media/
 [1] => fileadmin/
 [2] => uploads/
 [3] => typo3temp/
 [4] => t3lib/fonts/
 [5] => typo3/ext/
 [6] => typo3/sysext/
 [7] => typo3/contrib/
 [8] => typo3conf/ext/
 )

 [simulationHiddenOrTime] => 0
 [loaded] => 1
 [setup] => Array
 (
 [config.] => Array
 (
 [extTarget] => _top
 [stat] => 1
 [stat_typeNumList] => 0,1
 [uniqueLinkVars] => 1
 [index_enable] => 1
 [index_externals] => 0
 [index_metatags] => 1
 [language] => de
 [spamProtectEmailAddresses] => 1
 )

 [plugin.] => Array
 (

 [tx_sfjquery_pi1] => USER
 [tx_sfjquery_pi1.] => Array
 (
 [includeLibs] => typo3conf/ext/sfjquery/pi1/class.tx_sfjquery_pi1.php
 [userFunc] => tx_sfjquery_pi1->main
 )

 [tx_templavoila_pi1] => USER
 [tx_templavoila_pi1.] => Array
 (
 [includeLibs] => typo3conf/ext/templavoila/pi1/class.tx_templavoila_pi1.php
 [userFunc] => tx_templavoila_pi1->main
 )

 [tx_sfstdwrap_pi1] => USER
 [tx_sfstdwrap_pi1.] => Array
 (
 [includeLibs] => typo3conf/ext/sfstdwrap/pi1/class.tx_sfstdwrap_pi1.php
 [userFunc] => tx_sfstdwrap_pi1->main
 )

 [tx_cssstyledcontent.] => Array
 (
 [_CSS_DEFAULT_STYLE] => 	/* Captions */
 )

 [tx_extbase.] => Array
 (
 [_CSS_DEFAULT_STYLE] => 	input.f3-form-error {
		background-color:#FF9F9F;
		border: 1px #FF0000 solid;
	}
 )

 )

 [lib.] => Array
 (
 )

 [includeLibs.] => Array
 (
 [tx_cssstyledcontent_pi1] => EXT:css_styled_content/pi1/class.tx_cssstyledcontent_pi1.php
 [tx_templavoila_pi1] => EXT:templavoila/pi1/class.tx_templavoila_pi1.php
 )

 [tt_content] => CASE
 [tt_content.] => Array
 (
 [key.] => Array
 (
 [field] => CType
 )

 [stdWrap.] => Array
 (
 )

 [header] => COA
 [header.] => Array
 (
 [10] => < lib.stdheader
 )

 [text] => COA
 [text.] => Array
 (
 [10] => < lib.stdheader
 ) (
 )

 [page] => PAGE
 [page.] => Array
 (
 [typeNum] => 0
 [10] => USER
 [10.] => Array
 (
 [userFunc] => tx_templavoila_pi1->main_page
 )

 )

 [resources] => 
 [sitetitle] => Stefan Test
 [types.] => Array
 (
 [0] => page
 )

 )

 [flatSetup] => Array
 ( 
 )

 [const] => Array
 (
 [_clear] => <img src="clear.gif" width="1" height="1" alt="" />
 [_blackBorderWrap] => <table border="0" bgcolor="black" cellspacing="0" cellpadding="1"><tr><td> | </td></tr></table>
 [_tableWrap] => <table border="0" cellspacing="0" cellpadding="0"> | </table>
 [_tableWrap_DEBUG] => <table border="1" cellspacing="0" cellpadding="0"> | </table>
 [_stdFrameParams] => frameborder="no" marginheight="0" marginwidth="0" noresize="noresize"
 [_stdFramesetParams] => border="0" framespacing="0" frameborder="no"
 )

 [config] => Array
 ( 
 )

 [constants] => Array
 (
 [0] => Sau viel Text
 [1] => # Clear out any constants in this reserved room!
styles.content >


 )

 [editorcfg] => Array
 (
 [0] => 
 )

 [hierarchyInfo] => Array
 (
 [0] => Array
 (
 [root] => 
 [next] => 
 [clConst] => 
 [clConf] => 
 [templateID] => ext_cssstyledcontent/static/
 [templateParent] => sys_4
 [title] => EXT:css_styled_content/static/
 [uid] => cssstyledcontent/static/
 [pid] => 
 [configLines] => 1816
 )

 [1] => Array
 (
 [root] => 
 [next] => 
 [clConst] => 
 [clConf] => 
 [templateID] => ext_extbase
 [templateParent] => sys_4
 [title] => extbase
 [uid] => extbase
 [pid] => 
 [configLines] => 33
 )

 [2] => Array
 (
 [root] => 
 [next] => 
 [clConst] => 
 [clConf] => 
 [templateID] => ext_indexedsearch
 [templateParent] => sys_4
 [title] => indexed_search
 [uid] => indexedsearch
 [pid] => 
 [configLines] => 109
 )

 [3] => Array
 (
 [root] => 
 [next] => 
 [clConst] => 
 [clConf] => 
 [templateID] => ext_templavoila
 [templateParent] => sys_4
 [title] => templavoila
 [uid] => templavoila
 [pid] => 
 [configLines] => 2
 )

 [4] => Array
 (
 [root] => 1
 [next] => 
 [clConst] => 1
 [clConf] => 2
 [templateID] => sys_4
 [templateParent] => 
 [title] => Main template: Stefan Test
 [uid] => 4
 [pid] => 5
 [configLines] => 54
 )

 )

 [hierarchyInfoToRoot] => Array
 (
 [0] => Array
 (
 [root] => 
 [next] => 
 [clConst] => 
 [clConf] => 
 [templateID] => ext_cssstyledcontent/static/
 [templateParent] => sys_4
 [title] => EXT:css_styled_content/static/
 [uid] => cssstyledcontent/static/
 [pid] => 
 [configLines] => 1816
 )

 [1] => Array
 (
 [root] => 
 [next] => 
 [clConst] => 
 [clConf] => 
 [templateID] => ext_extbase
 [templateParent] => sys_4
 [title] => extbase
 [uid] => extbase
 [pid] => 
 [configLines] => 33
 )

 [2] => Array
 (
 [root] => 
 [next] => 
 [clConst] => 
 [clConf] => 
 [templateID] => ext_indexedsearch
 [templateParent] => sys_4
 [title] => indexed_search
 [uid] => indexedsearch
 [pid] => 
 [configLines] => 109
 )

 [3] => Array
 (
 [root] => 
 [next] => 
 [clConst] => 
 [clConf] => 
 [templateID] => ext_templavoila
 [templateParent] => sys_4
 [title] => templavoila
 [uid] => templavoila
 [pid] => 
 [configLines] => 2
 )

 [4] => Array
 (
 [root] => 1
 [next] => 
 [clConst] => 1
 [clConf] => 2
 [templateID] => sys_4
 [templateParent] => 
 [title] => Main template: Stefan Test
 [uid] => 4
 [pid] => 5
 [configLines] => 54
 )

 )

 [nextLevel] => 0
 [rootId] => 5
 [rootLine] => Array
 (
 [0] => Array
 (
 [pid] => 0
 [uid] => 5
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => Stefan Test
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => mod.web_list.alternateBgColors = 1
mod.wizards.newContentElement.renderMode = tabs
 [storage_pid] => 6
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 2
 [tx_templavoila_to] => 2
 [tx_templavoila_next_ds] => 2
 [tx_templavoila_next_to] => 2
 )

 [1] => Array
 (
 [pid] => 5
 [uid] => 34
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => stdWrap
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => 
 [storage_pid] => 0
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 
 [tx_templavoila_to] => 0
 [tx_templavoila_next_ds] => 
 [tx_templavoila_next_to] => 0
 )

 )

 [absoluteRootLine] => Array
 (
 [1] => Array
 (
 [pid] => 5
 [uid] => 34
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => stdWrap
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => 
 [storage_pid] => 0
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 
 [tx_templavoila_to] => 0
 [tx_templavoila_next_ds] => 
 [tx_templavoila_next_to] => 0
 )

 [0] => Array
 (
 [pid] => 0
 [uid] => 5
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => Stefan Test
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => mod.web_list.alternateBgColors = 1
mod.wizards.newContentElement.renderMode = tabs
 [storage_pid] => 6
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 2
 [tx_templavoila_to] => 2
 [tx_templavoila_next_ds] => 2
 [tx_templavoila_next_to] => 2
 )

 )

 [outermostRootlineIndexWithTemplate] => 0
 [rowSum] => Array
 (
 [0] => Array
 (
 [0] => 4
 [1] => Main template: Stefan Test
 [2] => 1263990411
 )

 [1] => Array
 (
 [0] => cssstyledcontent/static/
 [1] => EXT:css_styled_content/static/
 [2] => 
 )

 [2] => Array
 (
 [0] => extbase
 [1] => extbase
 [2] => 
 )

 [3] => Array
 (
 [0] => indexedsearch
 [1] => indexed_search
 [2] => 
 )

 [4] => Array
 (
 [0] => templavoila
 [1] => templavoila
 [2] => 
 )

 )

 [resources] => 
 [sitetitle] => Stefan Test
 [sections] => Array
 (
 [41428859d35690400a615c4ebfa39dc3] => [loginUser = *]
 )

 [sectionsMatch] => Array
 (
 )

 [clearList_const] => Array
 (
 [0] => ext_cssstyledcontent/static/
 [1] => ext_extbase
 [2] => ext_indexedsearch
 [3] => ext_templavoila
 [4] => sys_4
 )

 [clearList_setup] => Array
 (
 [0] => ext_cssstyledcontent/static/
 [1] => ext_extbase
 [2] => ext_indexedsearch
 [3] => ext_templavoila
 [4] => sys_4
 )

 [clearList_editorcfg] => Array
 (
 )

 [parserErrors] => Array
 (
 [constants] => Array
 (
 )

 [config] => Array
 (
 )

 )

 [setup_constants] => Array
 (
 )

 [fileCache] => Array
 (
 [14ca418a9f8b534a68de9e18d42481fd] => typo3/sysext/css_styled_content/pi1/class.tx_cssstyledcontent_pi1.php
 [d68d1cef015bfbba4613f144d425890e] => typo3conf/ext/templavoila/pi1/class.tx_templavoila_pi1.php
 [45af982164f6bf5663b461a547bcfbfd] => typo3conf/ext/sfstdwrap/pi1/class.tx_sfstdwrap_pi1.php
 )

 [frames] => Array
 (
 )

 [MPmap] => 
 )

 [cacheTimeOutDefault] => 0
 [cacheContentFlag] => 0
 [cacheExpires] => 0
 [isClientCachable] => 
 [all] => Array
 (
 [all] => Array
 (
 [41428859d35690400a615c4ebfa39dc3] => [loginUser = *]
 )

 [match] => 
 [rootLine] => Array
 (
 [0] => Array
 (
 [pid] => 0
 [uid] => 5
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => Stefan Test
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => mod.web_list.alternateBgColors = 1
mod.wizards.newContentElement.renderMode = tabs
 [storage_pid] => 6
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 2
 [tx_templavoila_to] => 2
 [tx_templavoila_next_ds] => 2
 [tx_templavoila_next_to] => 2
 )

 [1] => Array
 (
 [pid] => 5
 [uid] => 34
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => stdWrap
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => 
 [storage_pid] => 0
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 
 [tx_templavoila_to] => 0
 [tx_templavoila_next_ds] => 
 [tx_templavoila_next_to] => 0
 )

 )

 [rowSum] => Array
 (
 [0] => Array
 (
 [0] => 4
 [1] => Main template: Stefan Test
 [2] => 1263990411
 )

 [1] => Array
 (
 [0] => cssstyledcontent/static/
 [1] => EXT:css_styled_content/static/
 [2] => 
 )

 [2] => Array
 (
 [0] => extbase
 [1] => extbase
 [2] => 
 )

 [3] => Array
 (
 [0] => indexedsearch
 [1] => indexed_search
 [2] => 
 )

 [4] => Array
 (
 [0] => templavoila
 [1] => templavoila
 [2] => 
 )

 )

 )

 [sPre] => page
 [pSetup] => Array
 (
 [typeNum] => 0
 [10] => USER
 [10.] => Array
 (
 [userFunc] => tx_templavoila_pi1->main_page
 )

 )

 [newHash] => ef5b65e7d9c9aaf3cab46fe1c314d151
 [getMethodUrlIdToken] => 
 [no_CacheBeforePageGen] => 
 [tempContent] => 
 [forceTemplateParsing] => 
 [cHash_array] => Array
 (
 )

 [hash_base] => a:7:{s:3:"all";a:4:{s:3:"all";a:1:{s:32:"41428859d35690400a615c4ebfa39dc3";s:15:"[loginUser = *]";}s:5:"match";N;s:8:"rootLine";a:2:{i:0;a:27:{s:3:"pid";s:1:"0";s:3:"uid";s:1:"5";s:9:"t3ver_oid";s:1:"0";s:10:"t3ver_wsid";s:1:"0";s:11:"t3ver_state";s:1:"0";s:14:"t3ver_swapmode";s:1:"0";s:5:"title";s:11:"Stefan Test";s:5:"alias";s:0:"";s:9:"nav_title";s:0:"";s:5:"media";s:0:"";s:6:"layout";s:1:"0";s:6:"hidden";s:1:"0";s:9:"starttime";s:1:"0";s:7:"endtime";s:1:"0";s:8:"fe_group";s:0:"";s:16:"extendToSubpages";s:1:"0";s:7:"doktype";s:1:"1";s:8:"TSconfig";s:986:"mod.web_list.alternateBgColors = 1
mod.wizards.newContentElement.renderMode = tabs
";s:11:"storage_pid";s:1:"6";s:11:"is_siteroot";s:1:"0";s:9:"mount_pid";s:1:"0";s:12:"mount_pid_ol";s:1:"0";s:13:"fe_login_mode";s:1:"0";s:17:"tx_templavoila_ds";s:1:"2";s:17:"tx_templavoila_to";s:1:"2";s:22:"tx_templavoila_next_ds";s:1:"2";s:22:"tx_templavoila_next_to";s:1:"2";}i:1;a:27:{s:3:"pid";s:1:"5";s:3:"uid";s:2:"34";s:9:"t3ver_oid";s:1:"0";s:10:"t3ver_wsid";s:1:"0";s:11:"t3ver_state";s:1:"0";s:14:"t3ver_swapmode";s:1:"0";s:5:"title";s:7:"stdWrap";s:5:"alias";s:0:"";s:9:"nav_title";s:0:"";s:5:"media";s:0:"";s:6:"layout";s:1:"0";s:6:"hidden";s:1:"0";s:9:"starttime";s:1:"0";s:7:"endtime";s:1:"0";s:8:"fe_group";s:0:"";s:16:"extendToSubpages";s:1:"0";s:7:"doktype";s:1:"1";s:8:"TSconfig";s:0:"";s:11:"storage_pid";s:1:"0";s:11:"is_siteroot";s:1:"0";s:9:"mount_pid";s:1:"0";s:12:"mount_pid_ol";s:1:"0";s:13:"fe_login_mode";s:1:"0";s:17:"tx_templavoila_ds";s:0:"";s:17:"tx_templavoila_to";s:1:"0";s:22:"tx_templavoila_next_ds";s:0:"";s:22:"tx_templavoila_next_to";s:1:"0";}}s:6:"rowSum";a:5:{i:0;a:3:{i:0;s:1:"4";i:1;s:26:"Main template: Stefan Test";i:2;s:10:"1263990411";}i:1;a:3:{i:0;s:24:"cssstyledcontent/static/";i:1;s:30:"EXT:css_styled_content/static/";i:2;N;}i:2;a:3:{i:0;s:7:"extbase";i:1;s:7:"extbase";i:2;N;}i:3;a:3:{i:0;s:13:"indexedsearch";i:1;s:14:"indexed_search";i:2;N;}i:4;a:3:{i:0;s:11:"templavoila";i:1;s:11:"templavoila";i:2;N;}}}s:2:"id";i:34;s:4:"type";i:0;s:7:"gr_list";s:4:"0,-1";s:2:"MP";s:0:"";s:5:"cHash";a:0:{}s:15:"domainStartPage";N;}
 [pagesTSconfig] => 
 [additionalHeaderData] => Array
 (
 )

 [additionalJavaScript] => Array
 (
 )

 [additionalCSS] => Array
 (
 )

 [JSeventFuncCalls] => Array
 (
 [onmousemove] => Array
 (
 )

 [onmouseup] => Array
 (
 )

 [onkeydown] => Array
 (
 )

 [onkeyup] => Array
 (
 )

 [onkeypress] => Array
 (
 )

 [onload] => Array
 (
 )

 [onunload] => Array
 (
 )

 )

 [JSCode] => 
 [JSImgCode] => 
 [divSection] => 
 [defaultBodyTag] => <body>
 [debug] => 
 [intTarget] => 
 [extTarget] => _top
 [fileTarget] => 
 [MP_defaults] => Array
 (
 )

 [spamProtectEmailAddresses] => 1
 [absRefPrefix] => 
 [absRefPrefix_force] => 0
 [compensateFieldWidth] => 
 [lockFilePath] => fileadmin/
 [ATagParams] => 
 [sWordRegEx] => 
 [sWordList] => 
 [linkVars] => 
 [excludeCHashVars] => 
 [displayEditIcons] => 
 [displayFieldEditIcons] => 
 [sys_language_uid] => 0
 [sys_language_mode] => 
 [sys_language_content] => 0
 [sys_language_contentOL] => 
 [sys_language_isocode] => 
 [applicationData] => Array
 (
 )

 [register] => Array
 (
 [SYS_LASTCHANGED] => 1265719637
 [tx_templavoila_pi1.parentRec.uid] => 34
 [tx_templavoila_pi1.parentRec.pid] => 5
 [tx_templavoila_pi1.parentRec.t3ver_oid] => 0
 [tx_templavoila_pi1.parentRec.t3ver_id] => 0
 [tx_templavoila_pi1.parentRec.t3ver_wsid] => 0
 [tx_templavoila_pi1.parentRec.t3ver_label] => 
 [tx_templavoila_pi1.parentRec.t3ver_state] => 0
 [tx_templavoila_pi1.parentRec.t3ver_stage] => 0
 [tx_templavoila_pi1.parentRec.t3ver_count] => 0
 [tx_templavoila_pi1.parentRec.t3ver_tstamp] => 0
 [tx_templavoila_pi1.parentRec.t3ver_swapmode] => 0
 [tx_templavoila_pi1.parentRec.t3ver_move_id] => 0
 [tx_templavoila_pi1.parentRec.t3_origuid] => 0
 [tx_templavoila_pi1.parentRec.tstamp] => 1265709791
 [tx_templavoila_pi1.parentRec.sorting] => 512
 [tx_templavoila_pi1.parentRec.deleted] => 0
 [tx_templavoila_pi1.parentRec.perms_userid] => 2
 [tx_templavoila_pi1.parentRec.perms_groupid] => 0
 [tx_templavoila_pi1.parentRec.perms_user] => 31
 [tx_templavoila_pi1.parentRec.perms_group] => 27
 [tx_templavoila_pi1.parentRec.perms_everybody] => 0
 [tx_templavoila_pi1.parentRec.editlock] => 0
 [tx_templavoila_pi1.parentRec.crdate] => 1265707096
 [tx_templavoila_pi1.parentRec.cruser_id] => 2
 [tx_templavoila_pi1.parentRec.hidden] => 0
 [tx_templavoila_pi1.parentRec.title] => stdWrap
 [tx_templavoila_pi1.parentRec.doktype] => 1
 [tx_templavoila_pi1.parentRec.TSconfig] => 
 [tx_templavoila_pi1.parentRec.storage_pid] => 0
 [tx_templavoila_pi1.parentRec.is_siteroot] => 0
 [tx_templavoila_pi1.parentRec.php_tree_stop] => 0
 [tx_templavoila_pi1.parentRec.tx_impexp_origuid] => 0
 [tx_templavoila_pi1.parentRec.url] => 
 [tx_templavoila_pi1.parentRec.starttime] => 0
 [tx_templavoila_pi1.parentRec.endtime] => 0
 [tx_templavoila_pi1.parentRec.urltype] => 1
 [tx_templavoila_pi1.parentRec.shortcut] => 0
 [tx_templavoila_pi1.parentRec.shortcut_mode] => 0
 [tx_templavoila_pi1.parentRec.no_cache] => 0
 [tx_templavoila_pi1.parentRec.fe_group] => 
 [tx_templavoila_pi1.parentRec.subtitle] => 
 [tx_templavoila_pi1.parentRec.layout] => 0
 [tx_templavoila_pi1.parentRec.target] => 
 [tx_templavoila_pi1.parentRec.media] => 
 [tx_templavoila_pi1.parentRec.lastUpdated] => 0
 [tx_templavoila_pi1.parentRec.keywords] => 
 [tx_templavoila_pi1.parentRec.cache_timeout] => 0
 [tx_templavoila_pi1.parentRec.newUntil] => 0
 [tx_templavoila_pi1.parentRec.description] => 
 [tx_templavoila_pi1.parentRec.no_search] => 0
 [tx_templavoila_pi1.parentRec.SYS_LASTCHANGED] => 1265719637
 [tx_templavoila_pi1.parentRec.abstract] => 
 [tx_templavoila_pi1.parentRec.module] => 
 [tx_templavoila_pi1.parentRec.extendToSubpages] => 0
 [tx_templavoila_pi1.parentRec.author] => 
 [tx_templavoila_pi1.parentRec.author_email] => 
 [tx_templavoila_pi1.parentRec.nav_title] => 
 [tx_templavoila_pi1.parentRec.nav_hide] => 0
 [tx_templavoila_pi1.parentRec.content_from_pid] => 0
 [tx_templavoila_pi1.parentRec.mount_pid] => 0
 [tx_templavoila_pi1.parentRec.mount_pid_ol] => 0
 [tx_templavoila_pi1.parentRec.alias] => 
 [tx_templavoila_pi1.parentRec.l18n_cfg] => 0
 [tx_templavoila_pi1.parentRec.fe_login_mode] => 0
 [tx_templavoila_pi1.parentRec.tx_templavoila_ds] => 
 [tx_templavoila_pi1.parentRec.tx_templavoila_to] => 0
 [tx_templavoila_pi1.parentRec.tx_templavoila_next_ds] => 
 [tx_templavoila_pi1.parentRec.tx_templavoila_next_to] => 0
 [tx_templavoila_pi1.parentRec.tx_templavoila_flex] => <?xml version="1.0" encoding="utf-8" standalone="yes" ?>
 [tx_templavoila_pi1.parentRec.__SERIAL] => 5cdf3110159ec754582cd3d436cca8b4
 )

 [registerStack] => Array
 (
 )

 [cObjectDepthCounter] => 42
 [recordRegister] => Array
 (
 )

 [currentRecord] => tt_content:66
 [accessKey] => Array
 (
 )

 [imagesOnPage] => Array
 (
 )

 [lastImageInfo] => Array
 (
 )

 [uniqueCounter] => 0
 [uniqueString] => 43352a8978d5f18174a23d866fa44e26
 [indexedDocTitle] => stdWrap
 [altPageTitle] => 
 [pEncAllowedParamNames] => Array
 (
 )

 [baseUrl] => 
 [anchorPrefix] => 
 [cObj] => tslib_cObj Object
 ( 
 
 )

 [content] => 
 [clientInfo] => Array
 (
 [BROWSER] => net
 [VERSION] => 5
 [SYSTEM] => win
 [FORMSTYLE] => 1
 )

 [scriptParseTime] => 0
 [TCAloaded] => 0
 [csConvObj] => t3lib_cs Object
 (
 
 )

 [defaultCharSet] => iso-8859-1
 [renderCharset] => utf-8
 [metaCharset] => utf-8
 [localeCharset] => 
 [lang] => de
 [langSplitIndex] => 2
 [LL_labels_cache] => Array
 (
 )

 [LL_files_cache] => Array
 (
 )

 [pagesection_lockObj] => t3lib_lock Object
 (
 [method:protected] => simple
 [id:protected] => 4acfd65c934b556bcf9b40ed6cc0986b
 [resource:protected] => /var/www/test/typo3temp/locks/4acfd65c934b556bcf9b40ed6cc0986b
 [filepointer:protected] => 1
 [isAcquired:protected] => 1
 [loops:protected] => 150
 [step:protected] => 200
 )

 [pages_lockObj] => t3lib_lock Object
 (
 [method:protected] => simple
 [id:protected] => 162b40cabac9b1af3676162a15359e67
 [resource:protected] => /var/www/test/typo3temp/locks/162b40cabac9b1af3676162a15359e67
 [filepointer:protected] => 1
 [isAcquired:protected] => 1
 [loops:protected] => 150
 [step:protected] => 200
 )

 [pageRenderer:protected] => t3lib_PageRenderer Object
 (
 [compressJavascript:protected] => 
 [compressCss:protected] => 
 [removeLineBreaksFromTemplate:protected] => 
 [concatenateFiles:protected] => 
 [moveJsFromHeaderToFooter:protected] => 
 [csConvObj:protected] => t3lib_cs Object
 (
 [noCharByteVal] => 63
 [parsedCharsets] => Array
 (
 )

 [caseFolding] => Array
 (
 )

 [toASCII] => Array
 (
 )

 [twoByteSets] => Array
 (
 [ucs-2] => 1
 )

 [fourByteSets] => Array
 (
 [ucs-4] => 1
 [utf-32] => 1
 )

 [eucBasedSets] => Array
 (
 [gb2312] => 1
 [big5] => 1
 [euc-kr] => 1
 [shift_jis] => 1
 )

 [synonyms] => Array
 (
 [us] => ascii
 [us-ascii] => ascii
 [cp819] => iso-8859-1
 [ibm819] => iso-8859-1
 [iso-ir-100] => iso-8859-1
 [iso-ir-101] => iso-8859-2
 [iso-ir-109] => iso-8859-3
 [iso-ir-110] => iso-8859-4
 [iso-ir-144] => iso-8859-5
 [iso-ir-127] => iso-8859-6
 [iso-ir-126] => iso-8859-7
 [iso-ir-138] => iso-8859-8
 [iso-ir-148] => iso-8859-9
 [iso-ir-157] => iso-8859-10
 [iso-ir-179] => iso-8859-13
 [iso-ir-199] => iso-8859-14
 [iso-ir-203] => iso-8859-15
 [csisolatin1] => iso-8859-1
 [csisolatin2] => iso-8859-2
 [csisolatin3] => iso-8859-3
 [csisolatin5] => iso-8859-9
 [csisolatin8] => iso-8859-14
 [csisolatin9] => iso-8859-15
 [csisolatingreek] => iso-8859-7
 [iso-celtic] => iso-8859-14
 [latin1] => iso-8859-1
 [latin2] => iso-8859-2
 [latin3] => iso-8859-3
 [latin5] => iso-8859-9
 [latin6] => iso-8859-10
 [latin8] => iso-8859-14
 [latin9] => iso-8859-15
 [l1] => iso-8859-1
 [l2] => iso-8859-2
 [l3] => iso-8859-3
 [l5] => iso-8859-9
 [l6] => iso-8859-10
 [l8] => iso-8859-14
 [l9] => iso-8859-15
 [cyrillic] => iso-8859-5
 [arabic] => iso-8859-6
 [tis-620] => iso-8859-11
 [win874] => windows-874
 [win1250] => windows-1250
 [win1251] => windows-1251
 [win1252] => windows-1252
 [win1253] => windows-1253
 [win1254] => windows-1254
 [win1255] => windows-1255
 [win1256] => windows-1256
 [win1257] => windows-1257
 [win1258] => windows-1258
 [cp1250] => windows-1250
 [cp1251] => windows-1251
 [cp1252] => windows-1252
 [ms-ee] => windows-1250
 [ms-ansi] => windows-1252
 [ms-greek] => windows-1253
 [ms-turk] => windows-1254
 [winbaltrim] => windows-1257
 [koi-8ru] => koi-8r
 [koi8r] => koi-8r
 [cp878] => koi-8r
 [mac] => macroman
 [macintosh] => macroman
 [euc-cn] => gb2312
 [x-euc-cn] => gb2312
 [euccn] => gb2312
 [cp936] => gb2312
 [big-5] => big5
 [cp950] => big5
 [eucjp] => euc-jp
 [sjis] => shift_jis
 [shift-jis] => shift_jis
 [cp932] => shift_jis
 [cp949] => euc-kr
 [utf7] => utf-7
 [utf8] => utf-8
 [utf16] => utf-16
 [utf32] => utf-32
 [ucs2] => ucs-2
 [ucs4] => ucs-4
 )

 [lang_to_script] => Array
 (
 [ar] => arabic
 [bg] => cyrillic
 [bs] => east_european
 [cs] => east_european
 [da] => west_european
 [de] => west_european
 [es] => west_european
 [et] => estonian
 [eo] => unicode
 [eu] => west_european
 [fa] => arabic
 [fi] => west_european
 [fo] => west_european
 [fr] => west_european
 [ga] => west_european
 [ge] => unicode
 [gr] => greek
 [he] => hebrew
 [hi] => unicode
 [hr] => east_european
 [hu] => east_european
 [iw] => hebrew
 [is] => west_european
 [it] => west_european
 [ja] => japanese
 [kl] => west_european
 [ko] => korean
 [lt] => lithuanian
 [lv] => west_european
 [nl] => west_european
 [no] => west_european
 [nb] => west_european
 [nn] => west_european
 [pl] => east_european
 [pt] => west_european
 [ro] => east_european
 [ru] => cyrillic
 [sk] => east_european
 [sl] => east_european
 [sr] => cyrillic
 [sv] => west_european
 [sq] => albanian
 [th] => thai
 [uk] => cyrillic
 [vi] => vietnamese
 [zh] => chinese
 [ara] => arabic
 [bgr] => cyrillic
 [cat] => west_european
 [chs] => simpl_chinese
 [cht] => trad_chinese
 [csy] => east_european
 [dan] => west_european
 [deu] => west_european
 [dea] => west_european
 [des] => west_european
 [ena] => west_european
 [enc] => west_european
 [eng] => west_european
 [enz] => west_european
 [enu] => west_european
 [euq] => west_european
 [fos] => west_european
 [far] => arabic
 [fin] => west_european
 [fra] => west_european
 [frb] => west_european
 [frc] => west_european
 [frs] => west_european
 [geo] => unicode
 [glg] => west_european
 [ell] => greek
 [heb] => hebrew
 [hin] => unicode
 [hun] => east_european
 [isl] => west_euorpean
 [ita] => west_european
 [its] => west_european
 [jpn] => japanese
 [kor] => korean
 [lth] => lithuanian
 [lvi] => west_european
 [msl] => west_european
 [nlb] => west_european
 [nld] => west_european
 [nor] => west_european
 [non] => west_european
 [plk] => east_european
 [ptg] => west_european
 [ptb] => west_european
 [rom] => east_european
 [rus] => cyrillic
 [slv] => east_european
 [sky] => east_european
 [srl] => east_european
 [srb] => cyrillic
 [esp] => west_european
 [esm] => west_european
 [esn] => west_european
 [sve] => west_european
 [sqi] => albanian
 [tha] => thai
 [trk] => turkish
 [ukr] => cyrillic
 [albanian] => albanian
 [arabic] => arabic
 [basque] => west_european
 [bosnian] => east_european
 [bulgarian] => east_european
 [catalan] => west_european
 [croatian] => east_european
 [czech] => east_european
 [danish] => west_european
 [dutch] => west_european
 [english] => west_european
 [esperanto] => unicode
 [estonian] => estonian
 [faroese] => west_european
 [farsi] => arabic
 [finnish] => west_european
 [french] => west_european
 [galician] => west_european
 [georgian] => unicode
 [german] => west_european
 [greek] => greek
 [greenlandic] => west_european
 [hebrew] => hebrew
 [hindi] => unicode
 [hungarian] => east_european
 [icelandic] => west_european
 [italian] => west_european
 [latvian] => west_european
 [lettish] => west_european
 [lithuanian] => lithuanian
 [malay] => west_european
 [norwegian] => west_european
 [persian] => arabic
 [polish] => east_european
 [portuguese] => west_european
 [russian] => cyrillic
 [romanian] => east_european
 [serbian] => cyrillic
 [slovak] => east_european
 [slovenian] => east_european
 [spanish] => west_european
 [svedish] => west_european
 [that] => thai
 [turkish] => turkish
 [ukrainian] => cyrillic
 )

 [script_to_charset_unix] => Array
 (
 [west_european] => iso-8859-1
 [estonian] => iso-8859-1
 [east_european] => iso-8859-2
 [baltic] => iso-8859-4
 [cyrillic] => iso-8859-5
 [arabic] => iso-8859-6
 [greek] => iso-8859-7
 [hebrew] => iso-8859-8
 [turkish] => iso-8859-9
 [thai] => iso-8859-11
 [lithuanian] => iso-8859-13
 [chinese] => gb2312
 [japanese] => euc-jp
 [korean] => euc-kr
 [simpl_chinese] => gb2312
 [trad_chinese] => big5
 [vietnamese] => 
 [unicode] => utf-8
 [albanian] => utf-8
 )

 [script_to_charset_windows] => Array
 (
 [east_european] => windows-1250
 [cyrillic] => windows-1251
 [west_european] => windows-1252
 [greek] => windows-1253
 [turkish] => windows-1254
 [hebrew] => windows-1255
 [arabic] => windows-1256
 [baltic] => windows-1257
 [estonian] => windows-1257
 [lithuanian] => windows-1257
 [vietnamese] => windows-1258
 [thai] => cp874
 [korean] => cp949
 [chinese] => gb2312
 [japanese] => shift_jis
 [simpl_chinese] => gb2312
 [trad_chinese] => big5
 [albanian] => windows-1250
 [unicode] => utf-8
 )

 [locale_to_charset] => Array
 (
 [japanese.euc] => euc-jp
 [ja_jp.ujis] => euc-jp
 [korean.euc] => euc-kr
 [sr@Latn] => iso-8859-2
 [zh_cn] => gb2312
 [zh_hk] => big5
 [zh_tw] => big5
 )

 [charSetArray] => Array
 (
 [dk] => 
 [de] => 
 [no] => 
 [it] => 
 [fr] => 
 [es] => 
 [nl] => 
 [cz] => windows-1250
 [pl] => iso-8859-2
 [si] => windows-1250
 [fi] => 
 [tr] => iso-8859-9
 [se] => 
 [pt] => 
 [ru] => windows-1251
 [ro] => iso-8859-2
 [ch] => gb2312
 [sk] => windows-1250
 [lt] => windows-1257
 [is] => utf-8
 [hr] => windows-1250
 [hu] => iso-8859-2
 [gl] => 
 [th] => iso-8859-11
 [gr] => iso-8859-7
 [hk] => big5
 [eu] => 
 [bg] => windows-1251
 [br] => 
 [et] => iso-8859-4
 [ar] => iso-8859-6
 [he] => utf-8
 [ua] => windows-1251
 [jp] => shift_jis
 [lv] => utf-8
 [vn] => utf-8
 [ca] => iso-8859-15
 [ba] => iso-8859-2
 [kr] => euc-kr
 [eo] => utf-8
 [my] => 
 [hi] => utf-8
 [fo] => utf-8
 [fa] => utf-8
 [sr] => utf-8
 [sq] => utf-8
 [ge] => utf-8
 [ga] => 
 )

 [isoArray] => Array
 (
 [ba] => bs
 [br] => pt_BR
 [ch] => zh_CN
 [cz] => cs
 [dk] => da
 [si] => sl
 [se] => sv
 [gl] => kl
 [gr] => el
 [hk] => zh_HK
 [kr] => ko
 [ua] => uk
 [jp] => ja
 [vn] => vi
 )

 )

 [lang:protected] => de
 [title:protected] => 
 [charSet:protected] => 
 [favIcon:protected] => 
 [baseUrl:protected] => 
 [xmlPrologAndDocType:protected] => 
 [metaTags:protected] => Array
 (
 )

 [inlineComments:protected] => Array
 (
 )

 [headerData:protected] => Array
 (
 )

 [footerData:protected] => Array
 (
 )

 [titleTag:protected] => <title>|</title>
 [metaCharsetTag:protected] => <meta http-equiv="Content-Type" content="text/html; charset=|" />
 [htmlTag:protected] => <html>
 [headTag:protected] => <head>
 [baseUrlTag:protected] => <base href="|" />
 [iconMimeType:protected] => 
 [shortcutTag:protected] => <link rel="shortcut icon" href="%1$s"%2$s />
<link rel="icon" href="%1$s"%2$s />
 [jsInline:protected] => Array
 (
 )

 [extOnReadyCode:protected] => Array
 (
 )

 [cssInline:protected] => Array
 (
 )

 [bodyContent:protected] => 
 [templateFile:protected] => /var/www/test/typo3/sysext/cms/tslib/templates/tslib_page_frontend.html
 [jsLibraryNames:protected] => Array
 (
 [0] => prototype
 [1] => scriptaculous
 [2] => extjs
 )

 [addPrototype:protected] => 
 [addScriptaculous:protected] => 
 [addScriptaculousModules:protected] => Array
 (
 [builder] => 
 [effects] => 
 [dragdrop] => 
 [controls] => 
 [slider] => 
 )

 [addExtJS:protected] => 
 [addExtCore:protected] => 
 [extJSadapter:protected] => ext/ext-base.js
 [enableExtJsDebug:protected] => 
 [enableExtCoreDebug:protected] => 
 [extJStheme:protected] => 1
 [extJScss:protected] => 1
 [enableExtJSQuickTips:protected] => 
 [inlineLanguageLabels:protected] => Array
 (
 )

 [inlineSettings:protected] => Array
 (
 )

 [inlineJavascriptWrap:protected] => Array
 (
 [0] => <script type="text/javascript">
/*<![CDATA[*/
<!-- 

 [1] => // -->
/*]]>*/
</script>

 )

 [backPath] => typo3/
 [jsFiles:protected] => Array
 (
 )

 [jsFooterFiles:protected] => Array
 (
 )

 [jsFooterInline] => Array
 (
 )

 [jsLibs:protected] => Array
 (
 )

 [cssFiles:protected] => Array
 (
 )

 [inlineCssWrap] => Array
 (
 [0] => <style type="text/css">
/*<![CDATA[*/
<!-- 

 [1] => -->
/*]]>*/
</style>

 )

 )

 [pageCache:protected] => 
 [pageCacheTags:protected] => Array
 (
 )

 [config] => Array
 (
 [config] => Array
 (
 [extTarget] => _top
 [stat] => 1
 [stat_typeNumList] => 0,1
 [uniqueLinkVars] => 1
 [tx_extbase.] => Array
 (
 
 )

 [index_enable] => 1
 [index_externals] => 0
 [index_metatags] => 1
 [language] => de
 [spamProtectEmailAddresses] => 1
 [removeDefaultJS] => external
 [inlineStyle2TempFile] => 1
 [minifyJS] => 1
 [simulateStaticDocuments] => 
 [xhtmlDoctype] => 
 )

 [rootLine] => Array
 (
 [0] => Array
 (
 [pid] => 0
 [uid] => 5
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => Stefan Test
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => mod.web_list.alternateBgColors = 1
mod.wizards.newContentElement.renderMode = tabs
 [storage_pid] => 6
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 2
 [tx_templavoila_to] => 2
 [tx_templavoila_next_ds] => 2
 [tx_templavoila_next_to] => 2
 )

 [1] => Array
 (
 [pid] => 5
 [uid] => 34
 [t3ver_oid] => 0
 [t3ver_wsid] => 0
 [t3ver_state] => 0
 [t3ver_swapmode] => 0
 [title] => stdWrap
 [alias] => 
 [nav_title] => 
 [media] => 
 [layout] => 0
 [hidden] => 0
 [starttime] => 0
 [endtime] => 0
 [fe_group] => 
 [extendToSubpages] => 0
 [doktype] => 1
 [TSconfig] => 
 [storage_pid] => 0
 [is_siteroot] => 0
 [mount_pid] => 0
 [mount_pid_ol] => 0
 [fe_login_mode] => 0
 [tx_templavoila_ds] => 
 [tx_templavoila_to] => 0
 [tx_templavoila_next_ds] => 
 [tx_templavoila_next_to] => 0
 )

 )

 [mainScript] => index.php
 [FEData] => 
 [FEData.] => 
 [hash_base] => a:7:{s:3:"all";a:4:{s:3:"all";a:1:{s:32:"41428859d35690400a615c4ebfa39dc3";s:15:"[loginUser = *]";}s:5:"match";N;s:8:"rootLine";a:2:{i:0;a:27:{s:3:"pid";s:1:"0";s:3:"uid";s:1:"5";s:9:"t3ver_oid";s:1:"0";s:10:"t3ver_wsid";s:1:"0";s:11:"t3ver_state";s:1:"0";s:14:"t3ver_swapmode";s:1:"0";s:5:"title";s:11:"Stefan Test";s:5:"alias";s:0:"";s:9:"nav_title";s:0:"";s:5:"media";s:0:"";s:6:"layout";s:1:"0";s:6:"hidden";s:1:"0";s:9:"starttime";s:1:"0";s:7:"endtime";s:1:"0";s:8:"fe_group";s:0:"";s:16:"extendToSubpages";s:1:"0";s:7:"doktype";s:1:"1";s:8:"TSconfig";s:986:"mod.web_list.alternateBgColors = 1
mod.wizards.newContentElement.renderMode = tabs
";s:11:"storage_pid";s:1:"6";s:11:"is_siteroot";s:1:"0";s:9:"mount_pid";s:1:"0";s:12:"mount_pid_ol";s:1:"0";s:13:"fe_login_mode";s:1:"0";s:17:"tx_templavoila_ds";s:1:"2";s:17:"tx_templavoila_to";s:1:"2";s:22:"tx_templavoila_next_ds";s:1:"2";s:22:"tx_templavoila_next_to";s:1:"2";}i:1;a:27:{s:3:"pid";s:1:"5";s:3:"uid";s:2:"34";s:9:"t3ver_oid";s:1:"0";s:10:"t3ver_wsid";s:1:"0";s:11:"t3ver_state";s:1:"0";s:14:"t3ver_swapmode";s:1:"0";s:5:"title";s:7:"stdWrap";s:5:"alias";s:0:"";s:9:"nav_title";s:0:"";s:5:"media";s:0:"";s:6:"layout";s:1:"0";s:6:"hidden";s:1:"0";s:9:"starttime";s:1:"0";s:7:"endtime";s:1:"0";s:8:"fe_group";s:0:"";s:16:"extendToSubpages";s:1:"0";s:7:"doktype";s:1:"1";s:8:"TSconfig";s:0:"";s:11:"storage_pid";s:1:"0";s:11:"is_siteroot";s:1:"0";s:9:"mount_pid";s:1:"0";s:12:"mount_pid_ol";s:1:"0";s:13:"fe_login_mode";s:1:"0";s:17:"tx_templavoila_ds";s:0:"";s:17:"tx_templavoila_to";s:1:"0";s:22:"tx_templavoila_next_ds";s:0:"";s:22:"tx_templavoila_next_to";s:1:"0";}}s:6:"rowSum";a:5:{i:0;a:3:{i:0;s:1:"4";i:1;s:26:"Main template: Stefan Test";i:2;s:10:"1263990411";}i:1;a:3:{i:0;s:24:"cssstyledcontent/static/";i:1;s:30:"EXT:css_styled_content/static/";i:2;N;}i:2;a:3:{i:0;s:7:"extbase";i:1;s:7:"extbase";i:2;N;}i:3;a:3:{i:0;s:13:"indexedsearch";i:1;s:14:"indexed_search";i:2;N;}i:4;a:3:{i:0;s:11:"templavoila";i:1;s:11:"templavoila";i:2;N;}}}s:2:"id";i:34;s:4:"type";i:0;s:7:"gr_list";s:4:"0,-1";s:2:"MP";s:0:"";s:5:"cHash";a:0:{}s:15:"domainStartPage";N;}
 )

 [no_cacheBeforePageGen] => 
)
```
