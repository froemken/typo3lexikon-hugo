+++
title = "FormEngine"
naviTitle = "Form"
date = 2024-01-09T20:16:19+01:00
alwaysopen = false
aliases = ["form.html"]
+++

{{% badge style="green" icon="angle-double-up" %}}TYPO3 3.6{{% /badge %}}

Die TYPO3 Core Engine Form kurz `TCEform` erstellt auf Basis der zugrundeliegenden TCA (Table Configuration Array) die Formulare zum Bearbeiten von Datensätzen im Listen-Modul des TYPO3 Backends. Dabei werden sämtliche HTML Formularelemente wie Checkboxen, Selectboxen, Eingabe- und Textfelder und vieles mehr unterstützt. Hinzu kommen diverse Validatoren, um die Eingaben vor bzw. während der Speicherung zu überprüfen oder im Zuge der Speicherung nochmals anzupassen.

Die Programmierung der `TCEform` befindet sich in der Klasse `t3lib_tceform`.

{{% badge style="green" icon="angle-double-up" %}}TYPO3 6.0{{% /badge %}}

Mit der großen Umstrukturierung von TYPO3 6.0 wurde die Klasse `t3lib_tceform` in `FormEngine` umbenannt. Die Klasse dafür wurde in diesem Zuge nach `typo3/sysext/basckend/Classes/Form/FormEngine.php` verschoben.

{{% badge style="green" icon="angle-double-up" %}}TYPO3 7.5{{% /badge %}}

Mit TYPO3 7.5 wurde die `FormEngine` mit einem gewaltigen Patch in mehrere Dateien aufgesplittet. Christian Kuhn hat in über 6 Monaten Arbeit aus der über 4000 Zeilen großen `FormEngine` eine Struktur geschaffen, die durchschaubar, verständlich und sehr viel einfacher zu erweitern war. Nachteil war jedoch, dass alle damaligen Hooks zwecks Erweiterbarkeit instant verschwunden waren. Kein Übergangszeitraum. Kein veralteter Code. Alles weg. Alles neu. Ein Breaking Change allererster Güte, der in der TYPO3 Community nicht bei allen gut angekommen ist. Hinzugekommen sind auf jeden Fall ein Haufen UnitTests, um die Stabilität und Funktionsweise der `FormEngine` auch weiterhin zu gewähren. Die HTML-Ausgabe des neuen Systems wurde immer wieder mittels `diff` mit der ursprünglichen HTML-Ausgabe verglichen, um die Darstellung im Backend nicht zu verändern.

Wer Interesse hat, kann sich den Patch hier anschauen: [The FormEngine is dead, long live the FormEngine!]( https://review.typo3.org/c/Packages/TYPO3.CMS/+/41933)

Eine Klasse `FormEngine` existiert nun nicht mehr, dennoch hat dieser Name bis heute auch in der TYPO3 Dokumentation weiterhin Bestand. Unter der Haube werkelt jetzt ein `FormDataCompiler`, der jedes Element aus der TCA gruppiert und verschachtelt in Containern entgegennimmt und diese als Nodes zu einem Backend Formular verarbeitet. Für jedes Element, das im Backend darzustellen ist, gibt es eine eigene PHP Klasse, die sich um die Darstellung kümmert. Alle zur Verfügung stehenden Elemente werden in dem Array `nodeRegistry` geführt und können durch Extension-Entwickler nach Belieben erweitert werden. Wenn es für ein TCA Element mit einem bestimmten `type` mehrere Elemente in der `nodeRegistry` geführt werden, kann mittels eines `NodeResolvers` entschieden werden, welches Element auf Basis einer Priorität oder spezifischen Definition für die Darstellung verwendet werden soll.
