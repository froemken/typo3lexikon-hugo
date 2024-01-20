+++
title = "HTMLParser"
date = 2024-01-09T20:18:39+01:00
slug = "htmlparser"
aliases = ["htmlparsr.html"]
+++

Mithilfe des HTMLparsers könnt ihr kleinere, einfachere Modifikationen an den Inhalten des RTEs beim Speichern bzw. beim Laden in den RTE vornehmen. Für größere Nacharbeiten an den Inhalten verwendet bitte Transformations.

## Eigenschaften

### allowTags

Für diese Eigenschaft sind nur Komma separierte Werte erlaubt, auch für YAML.

Für HTMLparser_db und HTMLparser_rte werden die hier angegebenen Tags mit den Tags aus proc.allowTags zusammengeführt. Das gilt nicht für die anderen HTMLparser wie z.B. exitHTMLparser_db. Ein denyTags gibt es hier nicht. Wenn ihr für HTMLparser_db einen Tag verbieten wollt, dann verwendet dafür bitte proc.denyTags.

Gebt hier die Tags an, die beim Speichern/Laden erlaubt sind. Tags die nicht aufgeführt wurden gelten als verboten und werden bei Speicherung/Laden entfernt.

### stripEmptyTags

Mit dieser Eigenschaft könnt ihr angeben, ob leere Tags, also sowas wie <strong></strong> beim Speichern entfernt werden sollen. Die Tags werden auch dann entfernt, wenn sich ein oder mehr Leerzeichen innerhalb des Tags befinden. Beispiel:

```yaml
processing:
  mode: default
  exitHTMLparser_db:
    allowTags: "p, h2, h3, h4, strong, em"
    stripEmptyTags: 1
```

Wenn ihr bestimmte leere Tags nicht entfernen wollt, dann könnt ihr die entsprechend ausschließen. Angabe von keepTags nur mittels Komma separierter Liste.

```yaml
processing:
  mode: default
  exitHTMLparser_db:
    allowTags: "p, h2, h3, h4, strong, em"
    stripEmptyTags:
    keepTags: "em"
```

Wenn ihr nur bestimmte Tags entfernen wollt, dann gebt die Tag-Liste Komma separiert an. Diese Option kann nicht gleichzeitig mit keepTags verwendet werden:

```yaml
processing:
  mode: default
  exitHTMLparser_db:
    allowTags: "p, h2, h3, h4, strong, em"
    stripEmptyTags:
    tags: "strong, em"
```

Mit der zusätzlichen Angabe von treatNonBreakingSpaceAsEmpty werden sogar solche Tags entfernt, die als Inhalt nur das &nbsp; enthalten:

```yaml
processing:
  mode: default
  exitHTMLparser_db:
    allowTags: "p, h2, h3, h4, strong, em"
    stripEmptyTags:
    tags: "strong, em"
    treatNonBreakingSpaceAsEmpty: 1
```
