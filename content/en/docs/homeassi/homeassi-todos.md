---
title: "Homeassi Todos"
linkTitle: "Homeassi Todos"
date: 2023-01-10T21:57:00+01:00
weight: 30
description: >
  Homeassi Todos.
---

## Doku

* Evtl. Issues fuer die einzelnen Punkte erstellen
* [x] Diese Seite als Grundlage fuer die Doku nehmen: [Docsy User Guide](https://docsy.dev/docs/) ([Repo](https://github.com/google/docsy/tree/master/userguide))
* Installation und Verwendung Docsy/Hugo dokumentieren
* Publishen mit Github Actions (https://k4cg.github.io/homeassi/)
* Ueberfluessiges Zeug rausnehmen
* About Sektion schreiben ("Everything IoT. Homeassistant, MQTT, sensors, status, InfluxDB, Grafana, etc.")
* Blogpost(s) schreiben (was soll das alles, wozu gibt es diese Seite, Ist-Zustand, Plan, Progress Reports)
* Doku vervollstaendigen (Grafana Dashboard erstellen, InfluxDB Aufbau und Zugriff, wie baut man ein neues Getränk ein, wie baut man einen neuen (MQTT) Sensor ein)
* Dashboards dokumentieren
* Diagramm malen
  * Zeug in der K4:
    * [ ] Sensoren:
      * CO2 (CO2/Temp)
      * Matomat
      * Door (+Temp/Humidity/Pressure)
      * Tennis (Sound/Light/Temp)
      * Hosts
      * Octopi
    * [ ] [wifi2mqtt](https://k4cg.org/index.php/Projekt:wifi2mqtt)
      * /usr/local/wifi2mqtt auf `mqtt.intern.k4cg.org`
      * Wifi Geraete vom [router](https://k4cg.org/index.php/Host:router.intern.k4cg.org) => `sensors/wifi/online`
    * [ ] [mqtt2spacestatus](https://k4cg.org/index.php/Host:mqtt.intern.k4cg.org#MQTT2Spacestatus)
      * /usr/local/spacestatus/mqtt2spacestatus.py auf `mqtt.intern.k4cg.org`
      * Temp, Matomat, Sound, Light, Humidity, Hosts, Door, Octopi (`sensors/#`) => `/var/www/spacestatus/status.json`
    * [ ] [Apache mit status.json](https://k4cg.org/index.php/Host:mqtt.intern.k4cg.org#Apache)
      * stellt `status.json` unter <http://192.168.5.20/status.json> zur Verfuegung
      * von ausserhalb erreichbar unter <http://k4cgrouter.duckdns.org:59468/status.json> (Portweiterleitung im [router](https://k4cg.org/index.php/Host:router.intern.k4cg.org))
      * Am Ende erreichbar unter <https://status.k4cg.org/status.json> (zeigt auf [router](https://k4cg.org/index.php/Host:router.intern.k4cg.org), `httpd` dort macht `ProxyPass "/status.json" "http://k4cgrouter.duckdns.org:59468/status.json"`)
    * [ ] [Space API](https://spaceapi.k4cg.org/)
      * laeuft auf [rumpl](https://k4cg.org/index.php/Host:rumpl.k4cg.org)
      * `update.py` in `/var/www/spaceapi.k4cg.org/` erstellt aus dem [status](https://status.k4cg.org/status.json) die Datei `spaceapi.json`
      * `DirectoryIndex spaceapi.json` in der `httpd` Konfig
    * [ ] [k4cgrouter](http://k4cgrouter.duckdns.org) mit [Port Forwardings](https://router.intern.k4cg.org/cgi-bin/luci/admin/network/firewall/forwards)
      * <http://k4cgrouter.duckdns.org:59468/status.json> => <http://192.168.5.20/status.json>
      * alle anderen nicht weiter relevant
    * [ ] [Projekt SpaceStatus](https://k4cg.org/index.php/Projekt:SpaceStatus)
      * Zugriff auf `status.json`
        * IRC Bot (`Rezeptionistin`)
        * `sensor-fetcher` (s.u.)
        * Wiki Status
        * Telegram Bot (`k4cgbot`)
    * [x] Dinge auf mqtt Server, die hier noch nicht aufgelistet sind: sollte alles gewesen sein, siehe [Eintrag im Wiki](https://k4cg.org/index.php/Host:mqtt.intern.k4cg.org)
    * [x] [Projekt Stats](https://k4cg.org/index.php/Stats): Inhalt im Wiki veraltet, genauso wie [Projekt Sensors](https://k4cg.org/index.php/Projekt:Sensors)
  * Zeug auf [rumpl](https://k4cg.org/index.php/Host:rumpl.k4cg.org)
    * `sensor-fetcher`
      * `/usr/local/sensor-fetcher/fetch.py` auf [rumpl](https://k4cg.org/index.php/Host:rumpl.k4cg.org)
      * schreibt Daten aus [status.json](http://k4cgrouter.duckdns.org:59468/status.json) in die InfluxDB `sensors`
      * Tennis: Temp, Noise, Light
      * Externe Temperatur Wounderground, Open Weather Map, Dark Sky
      * Matomat: bier, mate, matecola, apfelschorle
      * Hosts
      * Door (Status, Humidity)
    * [ ] [Grafana](https://graphs.k4cg.org/)
      * laueft auf [rumpl](https://k4cg.org/index.php/Host:rumpl.k4cg.org)
        * [K4CG Homeassi InfluxDB](https://graphs.k4cg.org/d/0TS6_uMVk/k4cg-homeassi-influxdb?orgId=1): `home_assistant` (dort werden die Daten vom [neuen Homeassi](http://homeassistant.local:8123) befuellt)
        * [Besucher](https://graphs.k4cg.org/d/000000002/besucher?orgId=1): `sensors` (wird von [Donnerstats](https://k4cg.org/index.php/Stats#Besucherzahlen_2) befuellt)
        * [Getraenke](https://graphs.k4cg.org/d/puOH61mWz/getranke?orgId=1): `sensors` (wird von [sensor-fetcher](https://k4cg.org/index.php/Projekt:SpaceStatus#Schnittstelle_graphs.k4cg.org) befuellt)
        * [IRC users](https://graphs.k4cg.org/d/wKojG1kiz/irc-k4cg?orgId=1): `sensors` (Quelle: ???)
        * [Proxmox](https://graphs.k4cg.org/d/kxQQuHRZk/proxmox): `proxmox` (direkt auf den Proxmox Hosts proxmox.intern.k4cg.org und beehive.intern.k4cg.org konfiguriert)
        * [rumpl](https://graphs.k4cg.org/d/Xudgohmik/rumpl-k4cg-org?orgId=1&refresh=1m): `sensors` (wird vom `telegraf` auf [rumpl](https://k4cg.org/index.php/Host:rumpl.k4cg.org) befuellt)
        * [Sensoren](https://graphs.k4cg.org/d/000000001/sensoren?orgId=1): `wip` (wir vom `telegraf` auf `mqtt.intern.k4cg.org` befuellt; `["sensors/#"]` => InfluxDB `wip`)
        * [Sensoren Hass.io](https://graphs.k4cg.org/d/YKNaVklWz/sensoren-hass-io?orgId=1): `homeassi` (obsolet)
        * [Wahlcomputer](https://graphs.k4cg.org/d/6xEg3A1Vk/wahlcomputer?orgId=1): `wahlcomputer` (DB fuer den [c3wahlcomputer](https://c3wahl.computer/) in der CG)
        * [Oeffnungszeiten](https://graphs.k4cg.org/d/000000003/offnungszeiten?orgId=1): `sensors` (Quelle: ???)
    * [ ] InfluxDB
      * laueft auf [rumpl](https://k4cg.org/index.php/Host:rumpl.k4cg.org)
      * Datenbanken:
        * `sensors`: alle moeglichen Daten, default
        * `apache`: ???
        * `wip`: Quelle: `telegraf` auf `mqtt.intern.k4cg.org` (`["sensors/#"]`); Datenbasis fuer General/Sensoren
        * beehive: obsolet???
        * homeassi: DB vom alten Home Assi, obsolet
        * home_assistant: DB vom neuen Home Assi
        * proxmox: Proxmox Metriken von den Hosts `beehive` und `proxmox`
        * wahlcomputer: c3wahlcomputer
      * **TODO:** Zuordnung Grafana Dashboards -> InfluxDB Datenbanken und wie die Daten in die InfluxDB kommen
    * Telegraf? Apache Metrics?
    * Zusätzliche Metriken (Wetter?)
    * https://status.k4cg.org/status.json => Apache auf [rumpl](https://k4cg.org/index.php/Host:rumpl.k4cg.org) als reverse Proxy fuer http://k4cgrouter.duckdns.org:59468/status.json (s.o.)
    * Dinge auf rumpl, die hier noch nicht aufgelistet sind
    * Wie kommen die Daten ueberhaupt in die InfluxDB auf der rumpl? Scheinbar mit diesem Skript: `/usr/local/sensor-fetcher/fetch.py`. Das fragt die [status.json](http://k4cgrouter.duckdns.org:59468/status.json) ab und schreibt die Werte in die InfluxDB `sensors`
    * Graphs haben teilweise `InfluxDB-wip` als Data source (InfluxDB Datenbank `wip`). Auf `mqtt.intern.k4cg.org` laeuft ein telegraf, der hat einen Output, bei dem die InfluxDB Datenbank `wip` konfiguriert ist. Der Input ist per MQTT (`sensors/#`).
    * `influx --username admin --password ''`, `show users`, `show grants for $user`

## Homeassi

* Automatisches Backup einrichten (https://addons.mathesonsteplock.ca/docs/addons/remote-backup/basic-config/)
* Daten prüfen (Matomat Sensoren, Payload checken)
* Graphs auf neue Daten umbauen
* InfluxDB Config Homeassi checken (include, exclude, evtl. Domains für Sensoren festlegen und das uebrige Zeug excluden, damit nichts unnoetiges gespeichert wird)
* InfluxDB aufräumen (altes Zeug Homeassi)
* API:
  * Queries machen und Daten checken
  * Python Skript (nur mit Logik) schreiben
  * API mit fastapi bauen
* Customizations einbauen?
* Evtl. Device bei den Sensoren eintragen
* Repo aufräumen (https://github.com/k4cg/k4cg-automation und dieses Repo)
* Evtl. neue VM provisionieren
* Cleanup Zeug in Playbook einbauen (k4cg-automation)
