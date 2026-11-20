---
date: 2023-03-19 16:00:00 +0
title: Setting up a Remote Station -- Part 4 Build Log 
categories: [Remote Shack]
tags: [flex-6400, raspberry-pi, remote, rut-951, teltonika] 
---

*Updated: 20/3*

This post will document the process of putting together all the elements
of the remote station \"*on the bench*\" in the shack. I\'ll update this
as it goes, and hopefully put up some pictures as well.

## Day One

The router, pi and UPS have arrived, so I\'ve made a start.

- Configure the router
  - Connected my laptop to one of the LAN ports and logged in as *admin*
  - Changed the admin password
  - Set up the LAN Wi-Fi and address range. It needed to be changed
    because the default 192.168.1.0/24 would clash with one of my
    internal networks.
  - Enabled https management access from the WAN
  - Connected the WAN to my home Wi-Fi and checked I could log in from
    the WAN, enabling me to disconnect from the router\'s LAN
  - Check that the router can reach the internet and install the NTP
    server package to be the local time server for those occasions when
    the WAN is either disconnected or using 4G as I want to minimise
    unecessary network traffic.
- Build the Pi
  - Downloaded the aarch64 Debian 11 image and copied it to the SSD
  - Connected keyboard, mouse and monitor to the Pi and booted it for
    the first time.
  - Connected it to the house WiFi, and used \'nmcli\' to set a static
    address.
- I then configured a firewall rule on the router to allow \'ssh\'
  traffic to the Pi.
- Configured the Pi
  - Installed [Home Assistant](https://home-assistant.io "home-assistant.io")

## Day Two

- Installed the Home Assistant addons and adjusted `configuration.yaml`
  to use Mosquito Broker, MariaDb and InfluxDb.
- Installed the Node-Red addon
- Installed [system_sensors](https://github.com/Sennevds/system_sensors)
  as a service

## Day Three

- The Tapo plugs have arrived, so I installed the [Tapo Controller
  integration](https://github.com/petretiandrea/home-assistant-tapo-p100).
  For this I needed to install [HACS](https://hacs.xyz).
- I setup the Tapo plugs using the Tapo iOS app, connected them to the
  router\'s WiFi, made a note of their IP addresses and added the two
  plugs to the Home Assistant Tapo Controller integration.

## Day Four

I added the [APC UPS
integration](https://www.home-assistant.io/integrations/apcupsd/) to get
better feedback and form the basis for the automation that shuts the
radio down if the mains power fails. After reading some of the Flex
documentation I realised that I needed to sequence the power to the
radio. Basically, I need three \"switches\"

1.  To control the mains to the power supply.
2.  To control the 12V supply from the power supply to the Flex
3.  To remotely control the Flex power itself.

So, I've ordered a [Shelly 2.5PM WiFi Relay Switch](https://www.amazon.co.uk/dp/B07Q9M2Y1S?psc=1&ref=ppx_yo2ov_dt_b_product_details) with two relays
that can be controlled by Home Assistant. One will control another 12V
30A relay in the 12V feed from the power supply. The other will be
connected to the Flex\'s remote control port. The sequence will be:

- Initial state, all switches OFF
- On user command:
  - Switch on mains power and wait 2s
  - Switch on 12V and wait 10s for the Flex's controller processor to boot
  - Switch on Flex
- On user command or UPS going to battery
  - Switch off Flex and wait 30s for it to shutdown safely
  - Switch off 12v and wait 2s
  - Switch off mains power.
