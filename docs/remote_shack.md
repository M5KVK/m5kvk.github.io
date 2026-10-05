---
layout: page
title: My Remote Shack
permalink: /remote_shack
home_url: https://m5kvk.org
---

!["Picture of remote shack cabinet"](/assets/IMG_2841.png "External view of the remote shack")

## Overview

Due to the excessive, and increasing, levels of man-made interference being experienced at my home QTH, I decided to create a remote shack in a more rural location that would, hopefully, experience better conditions. I have documented the process of building the remote shack on my [blog](https://garethhowell.com/wp/tag/remote). This is the current configuration.

## Radio Side

- ITX Mini PC running Microsoft Windows 11 for local station control and digital modes (see later)
- [Flex Radio](https://www.flexradio.com/) 6400 100W HF Transceiver connected to
- 12V power injector to feed the Antenna Disconnect and the ATU
- 50m length of RG213 to reach the
- 12V power extractor providing power to
- [Paradan](https://paradanradio.com/products/antenna-disconnect) Antenna Disconnect and
- [SGC SG-230 ATU](https://www.radioworld.co.uk/sg-230_sgc_smartuner)
- 41m End Fed Random Wire
- 5 off 5-7m above-ground wires to act as a counterpoise

## Control System

- [Raspberry Pi 4](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/specifications/) with SSD in a fan-cooled Argon 40 metal case and running
  - [Home Assistant](https://www.home-assistant.io/) (see later)
- [ESPHome](https://esphome.io/) mains plugs to control the 240V for:
  - The Radio PSU
  - The Windows PC
  - The 60W Heater for winter use
- ESPHome flashed [SONOFF](https://sonoff.tech/products/) 4ch switch for:
  - Switching the Flex on and off
  - Toggling the PTT on the Flex (needed occasionally)
  - Controlling the 12V power to the Antenna Disconnect and ATU
- ESPHome flashed ESP32 with:
  - Dallas 1-wire temperature sensors for
    - outside air temperature
    - outer cabinet air temperature
    - inner cabinet upper shelf air temperature
    - inner cabinet lower shelf air temperature
  - DHT32 Temp/Humidity sensor for the inner cabinet middle shelf
  - 2 PID controllers for the inner and outer cabinet fan speed controls.

## Home Assistant

The brain controlling the remote shack is [Home Assistant](https://www.home-assistant.io/). I also use Home Assistant to augment my home and I know it very well.

Operating autonomously, Home Assistant will

- Monitor and control the environment in the inner cabinet using the heater and cooling fans
- Monitor the state of the local 240V supply and Internet access – and send me a Pushover alert if the UPS kicks in or primary Internet connection goes down – using the 4G modem on the Teltonika RUT-951 router if necessary
- Keep an eye on local windspeed and lightning and take action to shut the radio side down if necessary – as well as sending me an alert via Pushover.
- Poll external services to main a local record of appropriate propagation data, and present them via the UI.
- Using the Lovelace dashboard I designed, I can:

  - Sequence the power up and power down of the Flex, Antenna Disconnect and ATU
  - Ditto for the Windows PC
  - Manually toggle the PTT on the Flex – needed occasionally for SmartLink.
  - Manually override the state of the system where necessary
  - Monitor propagation and environmental data-
