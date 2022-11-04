Home assistantin PING-integaatio on out-of-the-box saatavilla mutta manuaalisesti konfiguroitavissa.

### Step 1. Luo seurattava entity

Configuration.yaml
```yaml
#Ping
binary_sensor:
  - platform: ping
    host: 1.1.1.1
    name: "Ping cloudflare"
    count: 1 #number of packets sent
    scan_interval: 30 #Seconds
```

Tästä entiteetistä tulee Connectivity-tyyppinen ja tämä sisältää seuraavat arvot:
- Connected (pellin alla: On)
- Disconnected (Pellin alla: Off)
- Unavailable
- Unknown

### Step 2.  Luo automaatio
1. Trigger = State, hae luoma sensorisi.
	- Aseta From: Connected ja To: Disconnected
		- For vaikka 1min jolloin yhteys pitää olla poikki minuutin, että automaatio laukee. Tämä on vaihtoehtoinen.
```yaml
platform: state
entity_id:
  - binary_sensor.ping_cloudflare
from: "on"
to: "off"
for:
  hours: 0
  minutes: 1
  seconds: 0
```
2. Conditions
	- Luo tarpeeseen sopivat conditions (valinnainen)
3. Actions
	- Valitse Device Action, valitse laitteesi (plug)
		- Valitse action (Turn off plug)
	- Valitse uusi action Wait for time to pass (Delay)
		- Valinnainen.
	- Valitse Device action, valitse laitteesi (plug)
		- Valitse action (Turn on plug)