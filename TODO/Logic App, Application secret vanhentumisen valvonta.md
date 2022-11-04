## Case study
Azuressa ei ole ilmoitusta Application Secrettien vanhentumisesta.

### Vaikeustaso
medium/hard, koska Graph API & Azure AD

## Tech specs
Vaaditaan application ID joka on luvitettu Graph API

Luetaan Graph apista Application-endpointista 
```
https://graph.microsoft.com/v1.0/applications
```

Docs: https://docs.microsoft.com/en-us/graph/api/application-list?view=graph-rest-1.0&tabs=http

Stepit
1. Listataan kaikki Aplikaatiot
2. Luetaan jokaisessa aplikaation secretistä endDateTime-arvo
3. Verrataan arvoa nykyhetkeen (tai muuhun haluuttuun ajankohtaan esim. 14 päivän sisällä vanhentuvat)
4. Luodaan sähköposti-ilmoitus vanhentuvasta secretistä
	- Vaihtoehtoisesti ilmoitetaan Teams kanavalle/viestinä vanhentumisesta


### Hifistelyä

Logic appin käyttämä App ID & Secret haetaan keyvaultista

