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
* Diese Seite als Grundlage fuer die Doku nehmen: [Docsy User Guide](https://docsy.dev/docs/) (Repo: [Docsy theme repo](https://github.com/google/docsy/tree/master/userguide))
* Installation und Verwendung Docsy/Hugo dokumentieren
* Publishen mit Github Actions
* About Sektion schreiben ("Everything IoT. Homeassistant, MQTT, sensors, status, InfluxDB, Grafana, etc.")
* Blogpost(s) schreiben (was soll das alles, wozu gibt es diese Seite, Ist-Zustand, Plan, Progress Reports)
* Doku vervollstaendigen (Grafana Dashboard erstellen, InfluxDB Aufbau und zugriff, wie baut man ein neues Getränk ein, wie baut man einen neuen (MQTT) Sensor ein)
* Dashboards dokumentieren
* Diagramm malen
  * Zeug in der K4:
    * Sensoren
    * wifi2mqtt
    * mqtt2spacestatus
    * Dinge auf mqtt Server, die hier noch nicht aufgelistet sind
    * Apache mit status.json
    * k4cgrouter mit Port Forwarding
    * Space API?
  * Zeug auf rumpl:
    * Grafana
    * InfluxDB
    * sensor-fetcher
    * Telegraf? Apache Metrics?
    * Zusätzliche Metriken (Wetter?)
    * Dinge auf rumpl, die hier noch nicht aufgelistet sind
    * Wie kommen die Daten ueberhaupt in die InfluxDB auf der rumpl? Scheinbar mit diesem Skript: `/usr/local/sensor-fetcher/fetch.py`. Das fragt die [status.json](http://k4cgrouter.duckdns.org:59468/status.json) ab und schreibt die Werte in die InfluxDB `sensors`
    * Graphs haben teilweise `InfluxDB-wip` als Data source (InfluxDB Datenbank `wip`). Auf `mqtt.intern.k4cg.org` laeuft ein telegraf, der hat einen Output, bei dem die InfluxDB Datenbank `wip` konfiguriert ist. Der Input ist per MQTT (`sensors/#`).

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
