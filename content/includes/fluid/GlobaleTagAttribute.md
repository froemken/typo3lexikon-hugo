### Globale Tag Attribute

| Eigenschaft          | Datentyp | Erklärung                                                                                                                                                                                                                |
|----------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| additionalAttributes | array    | Zusätzliche Tag Attribute. Diese werden dem Element direkt hinzugefügt. Beispiel: `{foo: 'bar'}` oder auch `{onchange: 'alert("Hallo")'}`                                                                                |
| data                 | array    | Hinzufügen von `data` Attributen. Diese Attribute werden dem Element mit vorangestelltem `data-` hinzugefügt. Beispiel: `{brand: 'Volkswagen'}` resultiert im Element zu: `<input type="text" data-brand="Volkswagen">`. |
| aria                 | array    | Hinzufügen von `aria` Attributen. Diese Attribute werden dem Element mit vorangestelltem `aria-` hinzugefügt. Beispiel: `{hidden: 'true'}` resultiert im Element zu: `<input type="text" aria-hidden="true">`.           |
