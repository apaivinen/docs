## Case study
Azure AD:sta halutaan poistaa Guest-käyttäjiä 

TÄMÄ ON LOGIC APP

### Vaikeustaso
Medium/Hard, koska Graph API

## Tech specs

1. Haetaan kaikki käyttäjät
	- https://docs.microsoft.com/en-us/graph/api/resources/user?view=graph-rest-1.0#properties
2. Loopataan käyttäjiä läpi tarkistaen
	-  Last SignInActivity
	  ```url
	  https://graph.microsoft.com/beta/users/USER-ID-TÄHÄN?select=SignInActivity,DisplayName
	  ```
3. Jos viimeisin kirjautuminen on tapahtunut yli kuukausi/6kk/vuosi sitten niin käyttäjä poistetaan
	```url
	https://docs.microsoft.com/en-us/graph/api/user-delete?view=graph-rest-1.0&tabs=http
	```