---
date: 2023-03-29 12:00:00 +0
title: Setting up a Remote Station -- Part 5 Setup continued
categories: [Remote Shack] 
tags: [flex-6400, raspberry-pi, remote, rut-951, teltonika, openvpn] 
---

Rather than continually editing part 4, I\'ve decided to move to
separate posts.

A lot has happened since I last posted and a lot of progress has been
made.\
I\'m nearly at the point where I am ready to start testing the system in
the home shack.

## From my log:

- I\'ve realised that the [Shelly 2.5 Wifi
  Switch](https://www.amazon.co.uk/dp/B07Q9M2Y1S?psc=1&ref=ppx_yo2ov_dt_b_product_details "Shelly 2.5")
  won\'t work after all. It needs to be powered by at least 30V DC and
  the two relays have one side connected to the 0V line. I need them to
  be independent. I\'ll probably implement a couple of switched outputs
  using the GPIO lines on the Pi.

- I configured the [OpenVPN](https://openvpn.net "OpenVPN.net") client
  on the router to connect to my home\'s OpenVPN server.

- I put a SIM card in the router and set up the failover rules so that
  the failover order is:

  - My Home LAN (will be changed to the Host LAN at the final location)
  - Any WAN connection that might be plugged in
  - 4G modem

- This works, though I had to change the process by which the router
  detects the need to failover, and then \"fail-back\".\
  The router detects the need to failover by pinging an address
  periodically. I set a different target for each interface.

- The VPN connects OK, but I can\'t route traffic.

  - I needed to \"push\" a route to the home LAN to the OpenVPN client,
    and add static routing statements to the home LAN\'s router.
  - I also needed to add additional firewall rules on the OpenVPN server

- I now had a major brain fade and managed to screw up the configuration
  of the Pi, so I needed to start again from scratch. Thank heavens for the fact that [Home
  Assistant](https://www.home-assistant.io "home-assistant.io") takes
  regular back-ups.

- I set up an Home Assistant automation to detect when the UPs went on
  to battery and use this to kick off the automation that shuts the
  radio (if it\'s on)
- With this in place, and knowing I\'d be away for a few days, I decided
  to see how long the UPS would power the system and whether it would
  recover atomically when the power returns. I disconnected the mains
  and went away
- When I came back, everything had functioned correctly and the UPS had
  enough energy to power the system for 5h30m. More than enough.

## Still to do

- Sort out the relay switching for the radio power
- Finalise the Node-Red dashboard
- Add temperature/humidity sensors for inside and outside the cabinet
- Install a Real Time Clock on the Pi
