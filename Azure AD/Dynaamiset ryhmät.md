# Johdanto
- Vaatii Azure AD Premium p1 lissenssin tai Intune for education jokaiselta käyttäjältä jotka ovat dynaamisen säännön kohteena.
	- Lisenssit on oltava olemassa mutta niitä ei tarvitse olla laitettuna käyttäjille
- Laitteilta ei tarvita ylimääräisiä lisenssejä

[Mikkiksen docs](https://learn.microsoft.com/en-us/azure/active-directory/enterprise-users/groups-dynamic-membership)
## propertyt

### Boolean propertyt
| Dynaaminen property | AD-property | jotain |
| ------------------- | ------------ | ------ |
| accountEnabled      |              |        |
| dirSyncEnabled      |              |        |

### String propertyt

| Dynaaminen property          | AD-property | Kommentti |
| ---------------------------- | ----------- | --------- |
| city                         |             |           |
| country                      |             |           |
| companyName                  |             |           |
| department                   |             |           |
| displayName                  |             |           |
| EmployeeId                   |             |           |
| facsimileTelephoneNumber     |             |           |
| givenName                    |             |           |
| jobTitle                     |             |           |
| mail                         |             |           |
| mailNickName                 |             |           |
| memberOf                     |             |           |
| mobile                       |             |           |
| objectId                     |             |           |
| onPremisesDistinguishedName  |             |           |
| onPremisesSecurityIdentifier |             |           |
| passwordPolicies             |             |           |
| physicalDeliveryOfficeName   |             |           |
| postalCode                   |             |           |
| preferredLanguage            |             |           |
| sipProxyAddress              |             |           |
| state                        |             |           |
| streetAddress                |             |           |
| surname                      |             |           |
| telephoneNumber              |             |           |
| usageLocation                |             |           |
| userPrincipalName            |             |           |
| userType                     |             |           |
 

