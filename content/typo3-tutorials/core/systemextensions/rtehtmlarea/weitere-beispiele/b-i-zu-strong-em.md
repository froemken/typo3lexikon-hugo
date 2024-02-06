+++
title = "b und i zu strong und em"
date = 2024-01-09T20:33:50+01:00
aliases = ["b-und-i-zu-strong-und-em.html"]
+++

Bei dieser Konvertierung haben wir ein Problem mit den Transformationen. Im Internet kursieren dazu viele verschiedene Anleitungen wie die Folgende:

```typo3_typoscript
entryHTMLparser_db {
  tags.b.remap = strong
}
```

Hier wurde zum ersten vergessen den entryHTMLparser_db zu aktivieren, zum Zweiten wurde auch noch der falsche HTMLparser angegeben und zum Dritten lassen sich die fetten Wörter/Abschnitte nach der Speicherung nur schwer wieder entfernen.

All das hat damit zu tun, weil in diesen Anleitungen die Verarbeitung der Transformationen nicht berücksichtigt und auch nicht die Verarbeitung durch den RTE näher gebracht wurde. Denn der RTE kann zwar strong-Bereiche anzeigen, aber der Button zum Fett machen kann nur b-Tags verarbeiten. Genau so auch der Button für kursiv. Dieser kann nur i-Tags verarbeiten...nicht aber em-Tags.

In der Transformation vom RTE zum Datenbank werden alle strong-Tags automatisch in b-Tags umgewandelt. Das könnt Ihr mit dem Code-Button mal selbst ausprobieren. Heißt: Wenn ich wie im oberen Beispiel den entryHTMLparser_db verwende. Dann habe ich zwar mein strong aber durch die Transformation landet wieder ein b-Tag in der Datenbank. Also müssen wir auf den exitHTMLparser_db umsteigen. Hier können die Ausgaben der Transformation nochmals nachbearbeitet werden.

Wenn ich meinen Datensatz allerdings erneut öffne, dann landet ein strong-Tag im RTE. Wie ich oben bereits erklärt habe, kann der RTE strong zwar anzeigen nicht aber mit den Buttons verarbeiten. Also müssen wir mit einem zweiten HTMLparser den strong-Tag aus der Datenbank wieder in einen b-Tag konvertieren.

Hier also ein vollständiges Codebeispiel, wie ich es gelöst habe:

```typo3_typoscript
RTE.classes >
RTE.classesAnchor >
RTE.default >

RTE.default {
  contentCSS = fileadmin/templates/style/rte.css
  showButtons = bold, italic, chMode, formatblock, blockstyle, blockstylelabel, textstyle, textstylelabel
  proc {
    overrideMode = css_transform
    allowedClasses = bodytext, blauerHintergrund, ekelig, code
    denyTags = u,a,img,br,center,font,hr,sub,sup,li,ul,ol,strike
    entryHTMLparser_db = 1
    entryHTMLparser_db {
      allowTags = p,b,i,strong,em,blockquote,pre,h1,h2,h3,h4,h5,h6,span,div,address
    }
    exitHTMLparser_db = 1
    exitHTMLparser_db {
     allowTags = p,b,i,strong,em,blockquote,pre,h1,h2,h3,h4,h5,h6,span,div,address
     tags {
       b.remap = strong
       i.remap = em
     }
    }
    exitHTMLparser_rte = 1
    exitHTMLparser_rte {
     allowTags = p,b,i,strong,em,blockquote,pre,h1,h2,h3,h4,h5,h6,span,div,address
     tags {
       strong.remap = b
       em.remap = i
     }
    }
  }
}
```
