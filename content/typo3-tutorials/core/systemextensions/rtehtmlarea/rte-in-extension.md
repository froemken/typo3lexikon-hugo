+++
title = "RTE in Extension"
date = 2024-01-09T20:31:52+01:00
aliases = ["rte-in-einer-extension", "rte-in-einer-extension.html"]
+++

Dieser Bericht ist noch nicht fertig, aber grundsätzlich wird der RTE in der localconf.php konfiguriert. Hier eine Konfiguration, wie ich sie für ein kleineres Projekt eingesetzt habe:

```php
t3lib_extMgm::addPageTSConfig('
#***************************************************************************************
# CONFIGURATION of RTE in table "tx_meine_extension", field "description"
#***************************************************************************************
RTE.classes >
RTE.classesAnchor >

RTE.classes {
  subheader1 {
    name = Subheader 1
    value = font-weight: bold; font-size: 14px; color: #5F5F5F;
  }
  subheader2 {
    name = Subheader 2
    value = font-weight: bold; font-size: 12px; color: #5F5F5F;
  }
}

RTE.classesAnchor {
  internalLink {
    class = internal-link
    type = page
    titleText = LLL:EXT:rtehtmlarea/res/accessibilityicons/locallang.xml:internal_link_titleText
  }
  externalLink {
    class = external-link
    type = url
    titleText = LLL:EXT:rtehtmlarea/res/accessibilityicons/locallang.xml:external_link_titleText
  }
  internalLinkNewWindow {
    class = internal-link-new-window
    type = page
    titleText = LLL:EXT:rtehtmlarea/res/accessibilityicons/locallang.xml:internal_link_new_window_titleText
  }
  externalLinkNewWindow {
    class = external-link-new-window
    type = url
    titleText = LLL:EXT:rtehtmlarea/res/accessibilityicons/locallang.xml:external_link_new_window_titleText
  }
  download {
    class = download
    type = file
    titleText = LLL:EXT:rtehtmlarea/res/accessibilityicons/locallang.xml:download_titleText
  }
  email {
    class = email
    type = mail
    titleText = LLL:EXT:rtehtmlarea/res/accessibilityicons/locallang.xml:mail_titleText
  }
}

RTE.colors {
  black {
    name = Black
    value = #000000
  }
  gray {
    name = Gray
    value = #5F5F5F
  }
}

RTE.config.tx_meine_extension.description {
  contentCSS = typo3conf/ext/meine_extension/res/rte.css
  useCSS = 0
  showButtons = blockstylelabel, blockstyle, formatblock, bold, italic, link, chMode, textstylelabel, textstyle, textcolor, unorderedlist
  buttons {
    formatblock {
      removeItems = address, div, pre, blockquote, h1, h3, h4, h5, h6
    }
  }
  enableWordClean = 1
  keepButtonGroupTogether = 1
  skin = EXT:t3skin/rtehtmlarea/htmlarea.css
  removeTrailingBR = 1
  removeComments = 1
  showTagFreeClasses = 0
  disableRightClick = 1
  ignoreMainStyleOverride = 1
  colors = black,gray
  disableColorPicker = 1
  classesParagraph = subheader1,subheader2,subheader3
  classesCharacter = labelSmall,labelBig
  classesAnchor = internal-link,external-link,internal-link-new-window,external-link-new-window,download,email
  classesAnchor.default {
    page = internal-link
    url = external-link-new-window
    file = download
    mail = email
  }
  proc {
    overruleMode = ts_css
    allowTags = a,link,strong,p,br,h2,h3,span,div,ul,li
    allowTagsOutside = img
    allowedClasses < RTE.config.tx_meine_extension.description.classesParagraph
    allowedClasses := addToList(labelSmall,labelBig)
    allowedClasses := addToList(rte-ulist)
    dontConvBRtoParagraph = 1
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags < RTE.config.tx_meine_extension.description.proc.allowTags
      keepNonMatchedTags = 1
      htmlSpecialChars = 0
      tags {
        h2.fixAttrib.class.list = subheader1,subheader2,subheader3
        h2.fixAttrib.class.default = subheader1
        ul.fixAttrib.class.default = rte-ulist
      }
    }
    exitHTMLparser_db = 1
    exitHTMLparser_db {
      allowTags < RTE.config.tx_meine_extension.description.proc.allowTags
      tags {
        b.remap = strong
        i.remap = em
      }
      htmlSpecialChars = 0
    }
    exitHTMLparser_rte = 1
    exitHTMLparser_rte {
      allowTags < RTE.config.tx_meine_extension.description.proc.allowTags
      tags {
        strong.remap = b
        em.remap = i
      }
      htmlSpecialChars = 0
    }
  }
}
');
```
