---
title: Setting up a Remote Station -- Part 9 Nearly Complete 
date: 2023-06-12 12:48:00.000000000 +01:00
categories: [Remote Shack] 
tags: [construction, flex-6400, remote]
---

## Nearly complete {#nearly-complete .wp-block-heading}

It\'s been some time since I last posted on this topic because I have
been away on an extended trip. However, I\'m back now and very
nearly (hopefully) at the point where I can move the completed station
to its new location. Since the last update, I\'ve been able to:

- Resolve the networking issue I grappled with in the previous post by
  adding an intermediate Ethernet switch.

- Rebuilt the Station Control Computer to run [Home
  Assistant](https://www.home-assistant.io) on its 
  preferred operating system - 
  [Home Assistant OS](https://github.com/home-assistant/operating-system).
   HAOS is a stripped down Linux built using
  [BuildRoot](https://buildroot.org) which is optimized for embedded devices.

As a consequence, I\'ve also migrated a couple of the additional systemd
services I used before, to use Home Assistant Addons, viz: The code to
monitor the UPS, the code to monitor the Pi, and the code to control the
fan in the Argon40 case I am using. 

For the UPS, I am now using [NUT](https://networkupstools.org) rather than APCUPSD. 
This forced me to change the code in the Windows PC as well.

I've also added integrations to display the current RF conditions and
provide warnings of any local thunderstorms.

My main tasks still outstanding are:

- To source an auto-disconnect for the antennas.

- To build a 12V injector to feed power to the SG-230 ATU that will be
  located at the base of the antenna.

- To enable the MQTT service on the router, so it can report status to
  Home Assistant

- To work on the Home Assistant dashboard that will be used to control
  everything.

- Develop automations to disconnect the antennas and power off the ATU if
 lightning is detected close by, or when the radio is off.

Once I have fixed the final location I can also:

- Decide on what is needed to house everything, which will also let,

- Lay everything out on a plywood base board,

- Finalise cable lengths and source screened cables for everything.

So, actually there\'s quite a lot still to do.

In the next post, I\'ll summarize where we are and include some diagrams
that should hopefully make everything clearer.
