## Case study
Esimerkkinä tietyltä twitter-tililtä haetaan uudet twiitit ja postataan ulkoiseen järjestelmään (Discord)

### Vaikeustaso
medium

## Tech specs
Vaaditaan Twitter-tili ja Discord-kanavalle WebHook aktivoitu

1. When a new tweet is posted -connector
2. Tweetti ja retweet sisältö lähetetään HTTP-kutsuna Discordiin
Docs: https://discord.com/developers/docs/interactions/message-components

HTTP-kutsu lähetetään POST-muodossa webhookin URL. Alla cCheatsheet discord-viestin body-sisällöstä:
```json
{
  "avatar_url": "Tähän avatarin URL. TÄMÄ ON VALINNAINEN ja voidaan jättää tyhjäksi.",
  "content": "TÄHÄN VIESTIN SISÄLTÖ",
  "username": "Tähän käyttäjä jonka nimi näkyy discord-viestin lähettäjänä. TÄMÄ ON VALINNAINEN. Oletuksena käytetään webhookin nimeä"
}
```


### Hifistelyä
Haetaan twitter rajapinnan kautta tiedot sen sijaan, että käytetään valmista connectoria mikä vaatii käyttäjätunnuksen.
Haetaan webhook URL keyvaultista