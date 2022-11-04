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

## Preview limitations

-   Each Azure AD tenant is limited to 500 dynamic groups using the memberOf attribute. memberOf groups do count towards the total dynamic group member quota of 5,000.
-   Each dynamic group can have up to 50 member groups.
-   When adding members of security groups to memberOf dynamic groups, only direct members of the security group become members of the dynamic group.
-   You can't use one memberOf dynamic group to define the membership of another memberOf dynamic groups. For example, Dynamic Group A, with members of group B and C in it, can't be a member of Dynamic Group D).
-   MemberOf can't be used with other rules. For example, a rule that states dynamic group A should contain members of group B and also should contain only users located in Redmond will fail.
-   Dynamic group rule builder and validate feature can't be used for memberOf at this time.
-   MemberOf can't be used with other operators. For example, you can't create a rule that states “Members Of group A can't be in Dynamic group B.”
-   The objects specified in the rule can't be administrative units.