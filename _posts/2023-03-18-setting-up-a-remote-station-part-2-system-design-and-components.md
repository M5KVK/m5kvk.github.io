---
date: 2023-03-18 12:00:00 +0
title: Setting up a Remote Station -- Part 2 System Design and Components
categories: [Remote Shack] 
tags: [remote, flex-6400, raspberry-pi, rut-951, teltonika]
---

Part 1 of this series covered the motivation behind this project and the
requirements I set.
This part will outline the system design and the components I've chosen.

# System Design

## Design Philosophy

The objective is to create a self-contained and secure environment that
delivers the required functionality, looks after itself as much as
possible, reports its status back to base and is fully remotely
controlled. The desired location for the remote station is somewhere
that already has Internet access and has a Wireless LAN that we can
connect to.
This avoids the need to rely on 4G access, but this will also be
provided as a failover option.

I've chosen to deliver this using readily available components and base
the control system on [Home
Assistant](https://www.home-assistant.io "Home Assistant"). 
Home
Assistant is open-source software that can be used to monitor and
automate just about all aspects of your home. It has a vast catalogue of
supported devices and services and I've been using it for some years to
add some smarts to my home.

I'm running Home Assistant on a [Raspberry
Pi](https://www.raspberrypi.com "raspberry.com") 4 housed in the Argon
One metal enclosure from [Argon
40](https://argon40.myshopify.com "Argon 40") with its companion M2 SSD
to improve performance and reliability.
I can\'t speak highly enough for this enclosure.\
Not only is beautiful to look at, it has significant passive cooling
plus a variable-speed fan.
Having the SSD significantly improves the usability of the Pi -
especially with the M2 SSD.

# The Remote Station

- Router with 4G modem and ability to join a host WiFi (if one is available)
- UPS
- Remote controlled mains switches for the Windows PC,
  the Radio Power Supply, and the box heater
- Raspberry Pi computer running Home Assistant
- Flex 6400 HF radio
- Remote On/Off control system for Flex
- Ditto for the Auto ATU and Antenna Disconnect
- Environmental monitors and fans

# The Home Station

- Mac Mini running SmartSDR for Mac
- Microphone
- Key

# Component selection

## Router

I've chosen to use the [Teltonika
RUT951](https://teltonika-networks.com/products/routers/rut951 "RUT951 router")
industrial router. I already have one of these installed in my motorhome
and it's excellent. It isn't cheap, but it is perfect for this job. It delivers:

- 4G dual MIMO WAN access with support for two SIM cards
- Ability to connect to an external Wireless LAN as a WAN
- Automatic failover between the various WAN connections
- Internal Wired and Wireless LANs with 3 physical ports
- VLAN capability (not used in this project)
- Digital I/O, Modbus support and MQTT
- VPN server and client
- Remote management (not used)

## UPS

I'm not looking to power the radio for any great time in the absence of
mains power, but I do want to be able to shutdown in an orderly fashion.
I've opted for the [APC BackUPS
650](https://www.amazon.co.uk/APC-BACK-UPS-BE650G2-UK-Uninterruptible-Protected/dp/B0828FD5KX "BackUPS ES 650").
This provides 650VA capacity, six protected outlets, plus two
surge-protected only outlets.

The following devices are connected:

- 5V 3A PSU for the Raspberry Pi Station Control Computer (SCC)
- 9V 1A PSU for RUT951 Router
- 12V 5A Ancillary PSU for the fans, ATU and antenna
  disconnect
- [Tapo
  P110](https://www.tapo.com/uk/product/smart-plug/tapo-p110/ "Tapo P110")
  remote controlled sockets for the Radio PSU, Windows
  computer and Heater
- Raspberry Pi Station Control Computer running Home Assistant

I have a lot of experience with the Pi and with Home Assistant, so this
combination was a natural choice. I'll cover the Home Assistant
configuration in another post.

## Radio PSU

To give me the potential to maybe operate off-grid, I\'ve chosen the
[MyDel
PS-1335](https://www.hamradio.co.uk/mydel-ps-1335-switching-mode-dc-regulated-power-supply "MyDEL PS-1335 Switched Mode PSU")
12V 30AMP PSU with battery backup capability to give a 30A UPS.
The MyDEL is an unknown quantity to me. It has a good spec and is
supposed to have very low noise.
We'll have to see.
Just in case it proves to be unsuitable for any reason, I have another
30A PSU I can use instead.

## [Flex-6400](https://www.flexradio.com/products/flex-6400-signature-series-sdr-transceiver/ "Flex-6400") Radio

Originally, I intended using my [Elecraft
K3S](https://elecraft.com "Elecraft") in the remote station and using
something like
[RemoteHams](http://www.remotehams.com/index.php "RemoteHams") to
control it.
After playing with RemoteHams, I felt that there was something missing.
Also, there is the issue of power control on the K3S.
You can remotely command the radio to power off, but you can't power it
on remotely.
This needs to be done by toggling a control pin - not a problem, but an added complexity.

For a while now, I've been looking at the FlexRadio range, and realised
that the 6400 is ideal for this application: after all, it's designed
to be operated remotely.

I went for the straight 6400 rather than the 6400M.
The one I have does not have an ATU. 
That's not a problem as I have the SG-230 Auto ATU.
If the proof of concept works, I might add the ATU module.

I'll cover the software and the detail of controlling the power to the
6400 in a separate post.
