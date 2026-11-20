---
date: 2023-03-19 12:00:00 +0
title: Setting up a Remote Station -- Part 3 Software
categories: [Remote Shack]
tags: [flex-6400, remote, home-assistant, docker, influxdb, mariadb, node-red]
---

Parts 1 and 2 covered the motivation and system design for the remote
station.
This post will cover the configuration of the Raspberry Pi System
Control Computer.

# High level view

The following operating system services will be installed:

- [Home Assistant](https://www.home-assistant.io "Home Assistant") and its
  [Addons](https://www.home-assistant.io/addons "Home Assistant Addons")
  - [Home Assistant Google Drive
    Backup](https://github.com/sabeechen/hassio-google-drive-backup "Google Drive Backup")
    to store the regular HA backups in the cloud
  - [Influxdb](https://github.com/hassio-addons/addon-influxdb "Influxdb")
    to capture the historical record of system events and sensor
    readings
  - [MariaDB](https://github.com/home-assistant/addons/tree/master/mariadb "MariaDB")
    to store the log files
  - [Mosquitto
    Broker](https://github.com/home-assistant/addons/tree/master/mosquitto "Mosquitto Broker")
    to act as the message broker for HA
  - [Node-Red](https://github.com/hassio-addons/addon-node-red "Node-Red")
    to deliver the control dashboard
  - [Visual Studio Code
    Server](https://github.com/hassio-addons/addon-vscode "Visual Studio Code")
    to edit the various configuration files
  - [ESPHome Device Builder](https://esphome.io) to build the software that runs on the [ESP32](https://esphome.io/components/esp32/) that monitors the environment and controls the fans and heater
- [system_sensors](https://github.com/Sennevds/system_sensors "System_Sensors")
  to read various sensors like CPU temperature and usage and feed them
  to HA using MQTT

# Home Assistant

Home Assistant is implemented as a series of "containers". The
recommendation is to either install it on [Home Assistant
OS](https://www.home-assistant.io/installation/raspberrypi "Home Assistant OS")
or as a single Docker container.

Using Home Assistant OS provides a fully supported environment and you
can use Home Assistant itself to install the various addons; whereas if
you install Home Assistant on its own, you need to manually install and
manage the other addon containers.

In addition to the addons, I also need to install several [Home
Assistant
Integrations](https://www.home-assistant.io/integrations/ "Integrations")
the main one being the [Tapo
Controller](https://github.com/petretiandrea/home-assistant-tapo-p100 "Tapo Controller")
so I can control the mains switches.

# Node-Red Dashboard

I\'ll cover this later as I haven\'t really given it any great thought
yet.\
However, in principle there will be a single page with two groups of
objects: the first displaying various sensors and the second with the
various controls that are needed - such as switching on the mains power
to the radio.

# Update
Actually, I backed away from using Node-Red. Whilst I was developing the dashboard, Home Assistant released an updated UI that provided everything I need.

I've nothing against Node-Red and do use it for other things: just not for this.
