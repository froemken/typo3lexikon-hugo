+++
title = "perfectlightbox"
date = 2024-01-09T20:52:04+01:00
aliases = ["perfectlightbox.html"]
+++

Hab heute mal die `perfectlightbox` installiert. Ich benötige diese Funktionalitäten immer in meinen FCEs. Also hier der Code, wie man die `perfectlightbox` in einem FCE ans Laufen bringt:

```xml
<field_standimage type="array">
    <tx_templavoila type="array">
        <TypoScript><![CDATA[
            10 = IMAGE
            10.file {
                import = uploads/tx_templavoila/
                import.current = 1
                import.listNum = 0
                maxW = 120c
                minW = 120c
                maxH = 90c
                minH = 90c
            }
            10.stdWrap.typolink {
                parameter.cObject = IMG_RESOURCE
                parameter.cObject.file {
                    import = uploads/tx_templavoila/
                    import.current = 1
                    import.listNum = 0
                }
                parameter.cObject.file.maxW = 600
                parameter.cObject.file.maxH = 600
                ATagParams = rel="lightbox[mySet]"
            }
            10.if.isTrue.field = field_standimage
            20 = TEXT
            20.value =
             20.if.isFalse.field = field_standimage
            ]]>
        </TypoScript>
    </tx_templavoila>
</field_standimage>
```
