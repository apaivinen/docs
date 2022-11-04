# Yleistä pohdintaa
- Miten CRM otetaan yhteyttä? App ID & Secret?
- Onko managed identity mahdollista? Jos kolme managed identityä on liikaan niin App ID & Secret? 
- Jos App ID & Secret otetaan käyttöön niin tulisi pohtia vaihdetaanko managed identity tähän jolloin kaikki olisi yhden appin takana
- Tarkista kaikki Graph API-oikeudet, että ovatko tarpeellisia.
	- Esim. DeleteUser-flowissa on user.read.all ja user.readwrite.all. Riittäisikö user.readwrite.all ainoastaan.

# Esivaatimukset
- Azure Subscription
- Resource Group
	- RG-UserSyncCRMExtranet
- 3x Logic App consumption mallilla, regioona Sweden central mikäli mahdollista
	- UserSyncCRMExtranet-AddNewUser
	- UserSyncCRMExtranet-UpdateUser
	- UserSyncCRMExtranet-DeleteUser
- Managed Identity aktivoitu jokaiseen logic app
- Graph api luvitukset tehty managed identityille
- Extranet URL minne käyttäjä ohjataan kutsun jälkeen

# Managed Identity Logic Appissa
1. Go to your logic app
2. Go to Settings --> Identity
3. Turn on System assigned managed identity
	1. Note the object ID: `d93b7bd3-fcc3-4911-b87c-17fd8096d804`

# UserSync-AddNewUser

Managed identity object id: `d93b7bd3-fcc3-4911-b87c-17fd8096d804`
Graph permissions: 
- `User.Invite.All`
- `GroupMember.ReadWrite.All`
- `User.ReadWrite.All`

### Powershellillä oikeudet
```powershell
$TenantID="f47c2802-876e-47d2-9164-7e620082f9b0"
$GraphAppId = "00000003-0000-0000-c000-000000000000"
#$DisplayNameOfMSI="UserSync-AddNewUser"
$AppObjectID = "d93b7bd3-fcc3-4911-b87c-17fd8096d804" # Appin objectID joka löytyy enterprise applicationsista. Tämä on myös Service Principalin ID
$PermissionName = "User.Invite.All"  

# Install-Module AzureAD # Install the module

Connect-AzureAD -TenantId $TenantID

$MSI = (Get-AzureADServicePrincipal -Filter "objectid eq '$AppObjectID'")

$GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"

$AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $MSI.ObjectId -PrincipalId $MSI.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

## Logiikka
1. Trigger kun uusi käyttäjä lisätään CRM
2. Ota käyttäjän nimi, sähköposti ja ryhmätieto
3. Create invitation  <--- POHDI VOIKO OLISIKO JÄRKEÄ TEHDÄ GRAPH API SUORAAN [Create invitation docs](https://learn.microsoft.com/en-us/graph/api/invitation-post?view=graph-rest-1.0&tabs=http)
	- Graph permission `User.Invite.All`
	- Aseta sähköposti
	- Lähetä ilmoitusviesti
	- Anna mahdollinen viesti
	- ANNA REDIRECT URL!!!1 Tämä voi olla esimerkiksi työtilojen etusivu
4. Päivitä käyttäjää ja anna tälle displayName kuntoon 
	- Tarkista ettei CRM tuleva nimi ole tyhjä
	- [Update user](https://learn.microsoft.com/en-us/graph/api/user-update?view=graph-rest-1.0&tabs=http)
	- Graph permissions `User.ReadWrite.All`
5. Päivitä käyttäjää ja lisää tämä ryhmään
	- Tarkista ryhmän olemassaolo
	- [Get group docs](https://learn.microsoft.com/en-us/graph/api/group-get?view=graph-rest-1.0&tabs=http)
	- Graph permissions: `GroupMember.Read.All`

# UserSync-UpdateUser

Managed identity object id: `f5138907-a7d0-4cf4-965b-19ca3ba7c59e`
Graph permissions: 
- `GroupMember.ReadWrite.All`
- `User.Read.All`

### Powershellillä oikeudet
```powershell
$TenantID="f47c2802-876e-47d2-9164-7e620082f9b0"
$GraphAppId = "00000003-0000-0000-c000-000000000000"
#$DisplayNameOfMSI="UserSync-AddNewUser"
$AppObjectID = "f5138907-a7d0-4cf4-965b-19ca3ba7c59e" # Appin objectID joka löytyy enterprise applicationsista. Tämä on myös Service Principalin ID
$PermissionName = "GroupMember.ReadWrite.All"  

# Install-Module AzureAD # Install the module

# Connect-AzureAD -TenantId $TenantID

$MSI = (Get-AzureADServicePrincipal -Filter "objectid eq '$AppObjectID'")

$GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"

$AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $MSI.ObjectId -PrincipalId $MSI.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

## Logiikka
1. Trigger kun käyttäjän ryhmätietoa muokataan CRM
2. Ota ryhmätieto ja käyttäjän sähköposti/UPN
3. Tarkistukset ryhmätieto tai käyttäjän tiedot tyhjänä
4. Hae käyttäjä
	- [Get a user docs](https://learn.microsoft.com/en-us/graph/api/user-get?view=graph-rest-1.0&tabs=http)
	- Graph permissions: `User.Read.All`
5. Hae käyttäjän ryhmät
	-  [List memberOf docs](https://learn.microsoft.com/en-us/graph/api/group-list-memberof?view=graph-rest-1.0&tabs=http)
	- Graph permissions: `GroupMember.Read.All`
6. Jos MemberOf-tiedoissa ei ole muutettua ryhmää niin lisätään käyttäjä kyseiseen ryhmään
	
### Pohdintaa
Voiko käyttäjä kuulua useampaan kuin yhteen ryhmään?
Mitä tapahtuu jos käyttäjälle tulee ryhmämuutos?

# UserSync-DeleteUser
Managed identity object id: ``
Graph permissions: 
- `User.ReadWrite.All`
- `User.Read.All`

### Powershellillä oikeudet

## Logiikka

1. Trigger kun käyttäjä poistetaan CRM
2. Otetaan käyttäjän sähköposti/UPN
3. Haetaan käyttäjä A-AD ja tarkistetaan olemassaolo
	- [Get a user docs](https://learn.microsoft.com/en-us/graph/api/user-get?view=graph-rest-1.0&tabs=http)
	- Graph permissions: `User.Read.All`
4. Poistetaan käyttäjä
	- [Delete a user docs](https://learn.microsoft.com/en-us/graph/api/user-delete?view=graph-rest-1.0&tabs=http)
	- Graph permissions `User.ReadWrite.All`

# Syitä miksi pitäisi käyttää Graph API
## Create invitation
Ei ole syitä miksi pitäisi käyttää Graph api. Tässä t oimii Managed Identity mille voidaan asettaa oikeudet powershell-kautta. Vaadittava oikeus on `User.Invite.All`

Jos halutaan kaivaa syitä et miksi create invitationia ei käytetä johtuu ihan siitä, että se on random kaverin tekemä connector jossa on selviä virheitä.
Esimerkiksi Name-parametri on oikeesti CC-sähköpostiosoite.

## Add user to group
Azure AD Connector vaatii käyttäjätunnuksen ja salasanan connectorin tunnistautumiseksi.
https://learn.microsoft.com/en-us/connectors/azuread/#add-user-to-group

Tulisi käyttää Graph API ja Managed Identityä. Vaihtoehtoisesti tulisi käyttää App registrationia jonka kautta App ID & Secret

Vaadittava Graph oikeus: `GroupMember.ReadWrite.All`
Docs: https://learn.microsoft.com/en-us/graph/api/group-post-members?view=graph-rest-1.0&tabs=http

## Delete User
Ei ole kyseistä connectoria saatavilla. Pakko tehdä Graph API

# Demoilua

UserSync-DEMO group object ID: 35a1841d-0c59-4605-a2f6-53cdcd67faee

UserSync-UpdateUser object ID: f5138907-a7d0-4cf4-965b-19ca3ba7c59e

# Katso myöhemmin

https://learningbydoing.cloud/blog/stop-using-client-secrets-start-using-managed-identities/