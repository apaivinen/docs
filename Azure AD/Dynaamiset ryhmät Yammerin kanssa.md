Esimerkkinä Yammer-ryhmään halutaan organisaation työntekijät jotka ovat omassa Security groupissa ja Guest-käyttäjät jotka ovat Microsot 365 groupissa.
Yammer itsessään luo Microsoft 365 groupin. 
Microsoft 365 grouppeja ei voi asettaa sisäkkäin eli Yammer-ryhmään ei voi lisätä guestien ja työntekijöiden ryhmiä.

Syksyllä 2022 on tullut dynaamiset sisäkkäiset ryhmät mahdolliseksi:
[Group membership for Azure AD dynamic groups with memberOf - Azure AD - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/enterprise-users/groups-dynamic-rule-member-of)

Dynaaminen ryhmä ryhmässä mahdollistaa sen, että Microsoft 365 groupiin haetaan muiden ryhmien jäsenet. Tällä hetkellä (Q4 2022) rajoitteena on, että dynaaminen kysely tulee luoda käsin ja kyselyä ei voi validoida etukäteen.
>HUOM!
>Dynaaminen ryhmä ei ole ryhmä ryhmässä. Dynaaminen ryhmä hakee muiden ryhmien jäsenet.

Yksinkertaisuudessaan kysely on tässä:
```
user.memberof -any (group.objectId -in ['Ryhmän-ObjectID-Tähän'])
```

Kyselyyn lisätään haettavien ryhmien object ID:t taulukkona eli tämä mahdollistaa yhdellä queryllä usean ryhmän jäsenten lisäyksen
```
user.memberof -any (group.objectId -in ['Ryhmän1-ObjectID-Tähän','Ryhmän2-ObjectID-Tähän'])
```

- Yammerin (ja teamsin) näkökulmasta Dynaamisella ryhmällä menetetään kyseisen yammer-communityn (ja teams-teamin) manuaalinen jäsenten hallinta.
	- Yammer (ja teams) käyttöliittymän kautta ei pysty lisäämään/poistamaan jäseniä ryhmiin jossa on aktivoitu dynaaminen kysely.
- Ryhmä jossa dynaaminen kysely aktivoidaan tyhjentää alkuperäiset jäsenet ja hakee uudet jäsenet kyselyn mukaisesti
- Vaatii Yammer Native Moden [Overview of Native Mode for Microsoft 365 - Yammer | Microsoft Learn](https://learn.microsoft.com/en-us/yammer/configure-your-yammer-network/overview-native-mode)
	- Kaikki tammikuu 2020 jälkeen luodut yammer-verkostot ovat oletuksena native moodissa
