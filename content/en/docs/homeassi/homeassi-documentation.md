---
title: "Homeassi Documentation"
linkTitle: "Homeassi Documentation"
date: 2023-01-10T21:57:00+01:00
weight: 10
description: >
  Homeassi documentation.
---

## Grundlegendes

* [Doku zur Installation](https://www.home-assistant.io/installation/linux) (momentan als VM auf einem der [Proxmox Hosts](https://proxmox.intern.k4cg.org:8006/) installiert)
* Web UI unter homeassistant.local:8123 (**TODO** statisches Lease im [Router](https://k4cg.org/index.php/Host:router.intern.k4cg.org) erstellen))
* Username/Passwort vergeben
* Grundlegene Einstellungen vornehmen

* Integrationen konfigurieren (*Einstellungen - Geraete & Dienste - Integrationen*):
  * **Philips Hue**: mit Knopfdruck an de Hue Bridge verbinden
  * **OctoPi**: nach Start der Einrichtung im Homeassistant an der [OctoPi Web UI](https://octopi.intern.k4cg.org/) anmelden und Anfrage bestaetigen
  * **Print Server**: geht ohne grossartige Konfiguration
  * **MQTT**: unter *Settings - Devices & Services* auf *Add Integration* klicken
    * Nach MQTT suchen und installieren
    * In der Tile der MQTT Integration (unter *Settings - Devices & Services*) auf *Configure* klicken und entsprechende Daten eintragen (Zugangsdaten muessen vorger im [MQTT Server](https://k4cg.org/index.php/Host:mqtt.intern.k4cg.org) eingerichtet werden)
    * Dort gibts auch die Moeglichkeit, mit *Listen to a topic* Nachrichten fuer ein bestimmtes Topic zu erhalten (nuetzlich fuer Debugging, z.B. `sensors/#` eintragen)

* Das Default Dashboard, das nach dem Login angezeigt wird, ist schreibgeschuetzt (kann ueber die drei Punkte rechts oben -> *Edit Dashboard* aber geaendert werden, wenn man unbedingt moechte)
* Auf dem Default Dashboard werden alle neu erkannten/konfigurierten Dinge angezeigt
* Unter *Settings - Dashboards* koennen eigene Dashboards angelegt werden (in unserem Fall z.B. das Dashboard *K4CG*)
* Die koennen fuer den schnellen Zugriff auch in der Sidebar auf der linken Seite hinterlegt werden

Das klingt super! Wie fuege ich jetzt einen MQTT Sensor hinzu?

**TODO** besser dokumentieren, wie man einen neuen Sensor hinzufuegt (MQTT Server, Topic, Payload, config.yaml, value_template, Dashboard, InfluxDB und Grafana)

* Custom Definitionen von Sensoren (z.B. `sensors/door/default/status`) koenen in der config.yaml hinterlegt werden
* Dazu ueber Settings -> Add-ons das `File Editor` Add-on installieren, starten und in der Sidebar auswaehlen
* Oder das VS Code Add-on installieren, damit koennen die Files mit VS Code direkt im Browser bearbeitet werden (bequemer, weil auto-complete und Syntaxcheck)
* Beispiel für einen Sensor:

```yaml
mqtt:
  sensor:
    - name: "Door Status"
      value_template: "{{ value_json.value }}"
      state_topic: "sensors/door/default/status"
      unique_id: "sensor_door_status"
```

* Danach ueber Developer Tools -> YAML -> YAML configuration reloading -> Manually configured MQTT entities die MQTT Dinge neu laden
* Eventuell in eigenes Dashboard einbinden
* Profit!
* Docs:
    * https://www.home-assistant.io/integrations/sensor
    * https://opensource.com/article/21/2/home-assistant-custom-sensors
    * https://www.home-assistant.io/integrations/sensor.mqtt
    * https://www.home-assistant.io/docs/configuration/templating/#processing-incoming-data
* Reload ggf. bei anderen Änderungen der Config notwendig, siehe https://www.home-assistant.io/docs/configuration/

## Sensors

### CO2 Sensor

* Projekt: N/A (noch nicht auf https://k4cg.org dokumentiert)
* Repo: https://github.com/k4cg/sensors-misc
* Herstellerseite: https://www.tfa-dostmann.de/en/product/co2-monitor-airco2ntrol-mini-31-5006/

#### CO2
  
* Payload: `{ "co2": 824, "_datestr": "2022-09-22T19:45:05.000000", "_timestamp": 1663868705 }`
* Topic: `sensors/co2sensor/default/co2`
* Retain: `false`
* Type: `mqtt sensor`
* value_template: `"{{ value_json.co2 }}"`
* Unit: `ppm`
* [x] im Grafana eingebaut?

| Property   | Datentyp | Format                      | Wertebereich        |
|------------|----------|-----------------------------|---------------------|
| co2        | int (?)  | 824                         | 0...3000 ppm        |
| _datestr   | date     | YYYY-MM-DDTHH:MM:SS.000000  |                     |
| _timestamp | int      | 1663868639 (unix timestamp) |                     |

#### Temperature

* Payload: `{ "_datestr": "2022-09-22T19:45:05.000000", "_timestamp": 1663868705, "co2temp": 22.8 }`
* Topic: `sensors/co2sensor/default/temp`
* Retain: `false`
* Type: `mqtt sensor`
* value_template: `"{{ value_json.co2temp }}"`
* Unit: `°C`
* [x] im Grafana eingebaut?

| Property   | Datentyp | Format                      | Wertebereich        |
|------------|----------|-----------------------------|---------------------|
| co2temp    | float    | 22.8 (1 Nachkommastelle?)   | 0...+50 °C          |
| _datestr   | date     | YYYY-MM-DDTHH:MM:SS.000000  |                     |
| _timestamp | int      | 1663868639 (unix timestamp) |                     |

### Matomat

* Projekt: https://k4cg.org/index.php/Projekt:Matomat-Service
* Repo: https://github.com/k4cg/matomat-service
* Payload: `{ "EventSource": "matomat", "EventName": "total-items-consumed", "EventTime": 1664471592, "ItemID": 14, "ItemName": "Spezi", "ItemCost": 101, "TotalItemCount": 62 }` (schickt noch andere Events, aber nur dieser Event wird im Value Template verwendet)
* Topic: `sensors/matomat/status`
* Retain: `false` (sollte eigentliche `true` sein)
* Type: `mqtt sensor`
* value_template: `"{{ value_json.TotalItemCount if value_json.ItemName == 'Club Mate' and value_json.EventName == 'total-items-consumed' else states('sensor.matomat_club_mate') }}"` (siehe auch https://community.home-assistant.io/t/template-sensor-based-on-mqtt-data/325418)
* Unit: N/A
* [x] im Grafana eingebaut?

| Property       | Datentyp | Format                      | Wertebereich        |
|----------------|----------|-----------------------------|---------------------|
| EventSource    | string   | "matomat                    |                     |
| EventName      | string   | "total-items-consumed"      | "item-consumed", "total-item-consumed-for-user-changed", "total-items-consumed" |
| EventTime      | int      | 1664471592 (unix timestamp) |                     |
| ItemID         | int      | 14                          |                     |
| ItemName       | string   | "Spezi"                     |                     |
| ItemCost       | int      | 101                         |                     |
| TotalItemCount | int      | 62                          |                     |

* Debugging

#### Items ausgeben lassen

Geht auch direkt am Matomat, wenn man Admin ist.

```bash
TOKEN=$(curl -k https://matomat.intern.k4cg.org:8443/v0/auth/login -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=<username>&password=<password>" | jq -r '.token')

curl -k https://matomat.intern.k4cg.org:8443/v0/items -H "Authorization: Bearer ${TOKEN}" | jq '.[] | { name: .name, id: .id} '
```

```json
{
  "name": "Club Mate",
  "id": 1
}
{
  "name": "Bier",
  "id": 2
}
{
  "name": "Apfelschorle",
  "id": 3
}
{
  "name": "Mate-Cola",
  "id": 4
}
{
  "name": "Flora",
  "id": 7
}
{
  "name": "Spezi",
  "id": 14
}
```

Diese Werte koennen dann im Template verwendet werden.

#### Stats ausgeben lassen

Geht auch direkt am Matomat, wenn man Admin ist.

```bash
TOKEN=$(curl -k https://matomat.intern.k4cg.org:8443/v0/auth/login -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=<username>&password=<password>" | jq -r '.token')

curl -k https://matomat.intern.k4cg.org:8443/v0/items/stats -H "Authorization: Bearer ${TOKEN}" | jq '.[] | { name: .name, id: .id, consumed: .consumed }'
```

```json
{
  "name": "Club Mate",
  "id": 1,
  "consumed": 494
}
{
  "name": "Bier",
  "id": 2,
  "consumed": 880
}
{
  "name": "Apfelschorle",
  "id": 3,
  "consumed": 329
}
{
  "name": "Mate-Cola",
  "id": 4,
  "consumed": 217
}
{
  "name": "Flora",
  "id": 7,
  "consumed": 51
}
{
  "name": "Spezi",
  "id": 14,
  "consumed": 62
}
```

#### Debugging

MQTT Config:

```bash
mqtt:
    client_id: matomat
    connection_string: tcp://192.168.5.20:1883
    topic: sensors/matomat/status
    username: matomat
    password: <password>
    retain_messages: True
```

Das Passwort findet sich im entweder auf dem [Matomat](https://k4cg.org/index.php/Host:matomat.intern.k4cg.org) selbst oder in der Konfig unseres [MQTT Brokers](https://k4cg.org/index.php/Host:mqtt.intern.k4cg.org).

Zum Debugging kann man sich dann eine Nachricht zusammenbauen und die an das Topic `sensors/matomat/status` schicken, z.B. so etwas:

```json
{
  "EventSource": "matomat",
  "EventName": "total-items-consumed",
  "EventTime": 1655553901,
  "ItemID": 14,
  "ItemName": "Spezi",
  "ItemCost": 101,
  "TotalItemCount": 60
}
```

Das sog. Value Template aktualisiert den Wert des jeweiligen Sensors im Homeassistant nur, wenn der EventName `total-items-consumed` ist (siehe Homeassi `configuration.yaml`). Die `ItemID` bekommt man mit einem [curl Befehl](#items-ausgeben-lassen).

Download von Debug Informationen der MQTT Integration: *Settings - Devices & Services*, Klick auf die drei Punkte in der MQTT Tile -> Download diagnostics

Die Logs unter *Settings - System - Logs* sind auch manchmal hilfreich.

### door-status

* Projekt: https://k4cg.org/index.php/Projekt:door-status
* Repo: https://github.com/k4cg/door-status

#### Temperature

* Payload: `{ "_datestr": "2022-09-22T17:43:59.000000", "value": 21.58, "_timestamp": 1663868639 }`
* Topic: `sensors/door/default/bme280/temperature`
* Retain: `true` (HA sagt nein)
* Type: `mqtt sensor`
* value_template: `"{{ value_json.value }}"`
* Unit: `°C`
* [x] im Grafana eingebaut?

| Property   | Datentyp | Format                      | Wertebereich        |
|------------|----------|-----------------------------|---------------------|
| value      | float    | 123.45 (2 Nachkommastellen) | -40.00...+85.00 °C  |
| _datestr   | date     | YYYY-MM-DDTHH:MM:SS.000000  |                     |
| _timestamp | int      | 1663868639 (unix timestamp) |                     |

#### Humidity

* Payload: `{ "_datestr": "2022-09-22T17:43:59.000000", "value": 38.27, "_timestamp": 1663868639 }`
* Topic: `sensors/door/default/bme280/humidity`
* Retain: `true` (HA sagt nein)
* Type: `mqtt sensor`
* value_template: `"{{ value_json.value }}"`
* Unit: `% RH`
* [x] im Grafana eingebaut?

| Property   | Datentyp | Format                      | Wertebereich        |
|------------|----------|-----------------------------|---------------------|
| value      | float    | 34.56 (2 Nachkommastellen)  | 0.00...100.00 %     |
| _datestr   | date     | YYYY-MM-DDTHH:MM:SS.000000  |                     |
| _timestamp | int      | 1663868639 (unix timestamp) |                     |

#### Pressure

* Payload: `{ "_datestr": "2022-09-22T17:43:59.000000", "value": 982.76, "_timestamp": 1663868639 }`
* Topic: `sensors/door/default/bme280/pressure`
* Retain: `true` (HA sagt nein)
* Type: `mqtt sensor`
* value_template: `"{{ value_json.value }}"`
* Unit: `hPa`
* [x] im Grafana eingebaut?

| Property   | Datentyp | Format                      | Wertebereich         |
|------------|----------|-----------------------------|----------------------|
| value      | float    | 901.56 (2 Nachkommastellen) | 300.00...1100.00 hPa |
| _datestr   | date     | YYYY-MM-DDTHH:MM:SS.000000  |                      |
| _timestamp | int      | 1663868639 (unix timestamp) |                      |

#### Status

* Payload: `{ "_datestr": "2022-09-22T17:43:59.000000", "value": "open", "_timestamp": 1663868639 }`
* Topic: `sensors/door/default/status`
* Retain: `true` (HA sagt nein)
* Type: `mqtt binary_sensor`
* value_template: `"{{ value_json.value }}"`
* Unit: N/A
* [x] im Grafana eingebaut?

| Property   | Datentyp | Format                      | Wertebereich        |
|------------|----------|-----------------------------|---------------------|
| value      | string   | "open"                      | "open", "closed"    |
| _datestr   | date     | YYYY-MM-DDTHH:MM:SS.000000  |                     |
| _timestamp | int      | 1663868639 (unix timestamp) |                     |

### Tennis

* Projekt: https://k4cg.org/index.php/Host:tennis.intern.k4cg.org
* Repo: https://github.com/Tinkerforge/brick-mqtt-proxy

#### Temperature

* Doku: https://www.tinkerforge.com/en/doc/Hardware/Bricklets/Temperature.html#temperature-bricklet
* Payload: `{ "_timestamp": 1663870803.199035, "temperature": 2443 }`
* Topic: `sensors/tischtennis/bricklet/temperature/tfj/temperature`
* Retain: `true` (HA sagt nein)
* Type: `mqtt sensor`
* value_template: `"{{ value_json.temperature | float / 100 | round(2) }}"`
* Unit: `°C`
* [x] im Grafana eingebaut?

| Property    | Datentyp | Format                                | Wertebereich        |
|-------------|----------|---------------------------------------|---------------------|
| temperature | int (?)  | 1234                                  | -40°C...+125°C      |
| _timestamp  | float    | 1663870803.199035 (unix timestamp???) |                     |

#### Sound Intensity

* Doku: https://www.tinkerforge.com/en/doc/Hardware/Bricklets/Sound_Intensity.html
* Payload: `{ "intensity": 1433, "_timestamp": 1663871103.167636 }`
* Topic: `sensors/tischtennis/bricklet/sound_intensity/voE/intensity`
* Retain: `true` (HA sagt nein)
* Type: `mqtt sensor`
* value_template: `"{{ value_json.intensity }}"`
* Unit: `N/A`
* [x] im Grafana eingebaut?

| Property    | Datentyp | Format                                | Wertebereich        |
|-------------|----------|---------------------------------------|---------------------|
| intensity   | int (?)  | 1433                                  |                     |
| _timestamp  | float    | 1663871103.167636 (unix timestamp???) |                     |

#### UV Light

* Payload: `{ "uv_light": 0, "_timestamp": 1663851602.851641 }`
* Topic: `sensors/tischtennis/bricklet/uv_light/xpa/uv_light`
* Retain: `true` (HA sagt nein)
* Type: `mqtt sensor`
* value_template: `"{{ value_json.uv_light }}"`
* Unit: `µW/cm²`
* [x] im Grafana eingebaut?

| Property    | Datentyp | Format                             | Wertebereich        |
|-------------|----------|------------------------------------|---------------------|
| uv_light    | int (?)  | 1                                  | 0...1 (?)           |
| _timestamp  | float    | 1663871103.167636 (unix timestamp) |                     |

### Wifi

* Projekt: https://k4cg.org/index.php/Projekt:wifi2mqtt
* Repo: https://github.com/k4cg/wifi2mqtt
* Payload: `{ "online": 7, "_timestamp": "2022-09-22 19:45:33.167346" }`
* Topic: `sensors/wifi/online`
* Retain: `false`
* Type: `mqtt sensor`
* value_template: `"{{ value_json.online }}"`
* Unit: `N/A`
* [x] im Grafana eingebaut?

| Property   | Datentyp | Format                     | Wertebereich        |
|------------|----------|----------------------------|---------------------|
| online     | int      | 7                          |                     |
| _timestamp | date     | YYYY-MM-DD HH:MM:SS.ffffff |                     |

### OctoPi

* Projekt: https://k4cg.org/index.php/Host:octopi.intern.k4cg.org
* Repo: https://github.com/OctoPrint/OctoPrint-MQTT
* Payload: `{ "progress": 100, "_timestamp": 1625572970, "location": "local", "path": "usb_cable_holder_v10_0.15mm_PLA_MK3_1h7m.gcode" }`
* Topic: `sensors/octopi/progress/printing`
* Retain: `true`
* Type: `mqtt sensor`
* value_template: `"{{ value_json.progress }}"`
* Unit: `%`
* [ ] im Grafana eingebaut? **TODO**

| Property   | Datentyp | Format                      | Wertebereich        |
|------------|----------|-----------------------------|---------------------|
| progress   | int (?)  | 100 (?)                     | 0...100 (?)         |
| location   | string   | "local"                     |                     |
| path       | string   | "foo.gcode"                 |                     |
| _timestamp | int      | 1663868639 (unix timestamp) |                     |

### Sensor Referenzen

* https://www.home-assistant.io/integrations/sensor.mqtt
* https://www.home-assistant.io/integrations/sensor/#device-class

## Templates

* https://www.home-assistant.io/docs/configuration/templating

## Dashboards

* https://www.home-assistant.io/dashboards
* https://www.home-assistant.io/dashboards/views/
* https://www.home-assistant.io/dashboards/cards/

## Automatisierung

* https://www.home-assistant.io/docs/automation
* https://www.home-assistant.io/docs/automation/action/
* https://www.home-assistant.io/docs/scripts/

## Schoene Namen

* Die Namen fuer Entities und Devices lassen sich in der UI und mit Eintraegen in den Config Files anpassen: https://www.home-assistant.io/docs/configuration/customizing-devices/

## Wireguard Verbindung zu rumpl

### Vorbereitungen

* VPN IP (aus dem Bereich `192.168.42.0/24`) im Wiki hinterlegen (https://k4cg.org/index.php/Hosts)

### Konfiguration rumpl

* Verbindung: `ssh <user:in>@rumpl.k4cg.org`
* Wireguard Key generieren (https://www.wireguard.com/quickstart/)
* Wireguard Konfig bearbeiten (https://k4cg.org/index.php/Projekt:VPN#Wie_kann_auch_meine_Ger.C3.A4t_in_das_VPN.3F)

```nix
{
  allowedIPs = [ "192.168.42.5/32" ]; # VPN IP homeassi
  publicKey = "ZdIZn2naS0yDN8OxFXzj7jf0dvNrStUk6bjjytvIkVw="; # wg pubkey < privatekey > publickey
}
```

### Konfiguration im Home Assistant

* Repo hinzufuegen und `wireguard-client` Addon installieren (https://github.com/bigmoby/hassio-repository-addon)
* Konfig im Homeassi:
  * Start on boot und Watchdog aktivieren (Addon wird neu gestartet, falls es crasht)

```yaml
interface:
  private_key: <private_key>
  address: 192.168.42.5/24
  dns:
    - 8.8.8.8
    - 8.8.4.4
  post_up: iptables -t nat -A POSTROUTING -o wg0 -j MASQUERADE
  post_down: iptables -t nat -D POSTROUTING -o wg0 -j MASQUERADE
peers:
  - public_key: fY0GiZHUVGSyuteTwrWTnBpURiGOwtJtP0p4gxBj8UY=
    endpoint: 213.95.154.150:51666
    allowed_ips:
      - 192.168.42.0/24
    persistent_keep_alive: "25"
log_level: debug # anpassen, wenn alles funktioniert
```

### Alte Homeassi Konfig

```bash
root@homeassi:~# cat /etc/wireguard/wg-rumpl.conf 
[Interface]
PrivateKey = ''
Address = 192.168.42.5/24

[Peer]
PublicKey = fY0GiZHUVGSyuteTwrWTnBpURiGOwtJtP0p4gxBj8UY=
AllowedIPs = 192.168.42.0/24
Endpoint = 213.95.154.150:51666
```

## InfluxDB Addon

### Vorbereitungen

#### rumpl

* Admin Zugangsdaten fuer InfluxDB: `pass Sites/graphs.k4cg.org`
* Datenbank und User in der InfluxDB anlegen (https://docs.influxdata.com/influxdb/v1.8/introduction/get-started/#creating-a-database und https://docs.influxdata.com/influxdb/v1.8/administration/authentication_and_authorization/)

```bash
sudo -iu root
influx --username admin --password ''
password: # InfluxDB admin Passwort aus pass 
> CREATE DATABASE <homeassi_db_name> # home_assistant; alte DB: homeassi
> CREATE USER <homeassi_user> WITH PASSWORD '<user-password>' # homeassi
> GRANT ALL ON <homeassi_db_name> TO <homeassi_user>
> SHOW GRANTS FOR <homeassi_user>
> GRANT READ ON home_assistant TO grafana # falls die Daten auch in Grafana verfuegbar sein sollen 
> SHOW GRANTS FOR grafana
```

#### Homeassi Konfig

Folgendes in der `configuration.yaml` eintragen:

```yaml
influxdb:
  host: 192.168.42.1
  port: 8086
  database: home_assistant
  username: homeassi
  password: <password>
  # ssl: true
  # verify_ssl: true
  max_retries: 3
  # default_measurement: state
  # exclude:
  #   entities:
  #      - entity.id1
  #      - entity.id2
  #   domains:
  #      - automation
  # include:
  #   entities:
  #      - entity.id3
  #      - entity.id4
  tags:
    # instance: prod
    source: hass
```

Die Konfig muss wahrscheinlich noch ein bisschen angepasst werden. Hier gibts eine Uebersicht, wie man rausfinden kann, was so alles in der Datenbank landet:
* https://docs.influxdata.com/influxdb/v1.8/query_language/explore-data/
* https://docs.influxdata.com/influxdb/v1.8/query_language/explore-schema/

## SSH Addon

* Addon installieren
* In den Addon Einstellungen Public Key(s) hinterlegen, Port konfigurieren
* Verbindung mit `ssh -i ~/path/to/private_key -p <port> root@<home_assi_ip>`
* HA CLI: https://www.home-assistant.io/common-tasks/os#home-assistant-via-the-command-line
