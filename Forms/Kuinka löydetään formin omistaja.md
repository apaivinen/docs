Formin omistaja pitää löytää mutta tiedetään vain formin URL
Url `https://forms.office.com/Pages/ResponsePage.aspx?id=Aih89G6H0keRZH5iAIL5sODulGoTwOtPnxGYsVGgngNUQU5DVDlUVUFEWVREMDRHMkQ5UFNXMTJRUS4u`  
Esimerkissä urlissa on ID-parametri `Aih89G6H0keRZH5iAIL5sODulGoTwOtPnxGYsVGgngNUQU5DVDlUVUFEWVREMDRHMkQ5UFNXMTJRUS4u`  

1. Aseta ID-parametri tähän URL:
`https://forms.office.com/pages/designpagev2.aspx?origin=shell&subpage=design&id=`

URL pitäisi näyttää tältä:
`https://forms.office.com/pages/designpagev2.aspx?origin=shell&subpage=design&id=Aih89G6H0keRZH5iAIL5sODulGoTwOtPnxGYsVGgngNUQU5DVDlUVUFEWVREMDRHMkQ5UFNXMTJRUS4u` 

2. Mene äsken luotuun osoitteeseen selaimella ja avaa Developer tools (F12)
3. Mene Network-välilehdelle
4. Syötä suodatushakuun "formapi" niin saat esiin vastauksen joka on käytännössä "UnauthorizedAccess" (tämän tiedon näkee Responses-välilehdeltä)

```json
{"error":{"code":"707","message":"UnauthorizedAccess to CDB. Inner Message: {\"error\":{\"code\":\"OperationForbidden\",\"message\":\"Unauthorized Access\",\"innerError\":{\"code\":\"UnauthorizedAccess\"}}}","@ms.form.error.type":"ExpectedFailure"}}
```
6. Mene Headers-välilehdelle ja saat Request-URL:n tietoon
   
```
https://forms.office.com/formapi/api/f47c2802-876e-47d2-9164-7e620082f9b0/users/6a94eee0-c013-4feb-9f11-98b151a09e03/light/forms('Aih89G6H0keRZH5iAIL5sODulGoTwOtPnxGYsVGgngNUQU5DVDlUVUFEWVREMDRHMkQ5UFNXMTJRUS4u')?$select=
```
7. Urlissa `/users/` jälkeen on käyttäjän ID `6a94eee0-c013-4feb-9f11-98b151a09e03`
8. Hae käyttäjän ID:tä esim. Azure Ad users tai Microsoft Admin Users
9. Nyt pitäisi olla selvillä käyttäjä, joka omistaa formin.
10. Pyydä käyttäjää jakamaan formin eteenpäin

![[Pasted image 20221104125546.png]]


Source: 