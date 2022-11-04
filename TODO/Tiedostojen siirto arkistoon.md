## Case study
Sharepoint Onlinessa tiedostot halutaan siirtää arkistoon.
Tiedostojen kuuluu siirtyä, kun käyttä aktivoi tiedoston siirron.

### Vaikeustaso
Medium, koska trigger conditions & HTTP Request

## Tech stuff

### trigger condition
```
@equals(triggerBody()?['Arkistoon_x0020_siirto'],true)
```

### HTTP requests to sharepoint
https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/update-file-metadata-with-rest-api-using-validateupdatelistitem/ba-p/1365682

```
{
  "formValues": [
    {
      "FieldName": "Viesti",
      "FieldValue": "Tiedosto on siirretty arkistoon kansioon @{variables('KirjastonNimi')}"
    },
    {
      "FieldName": "Modified",
      "FieldValue": "@{variables('ConvertedModified')}"
    }
  ],
  "bNewDocumentUpdate": true
}

```