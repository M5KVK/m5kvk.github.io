---
title: Using SmartLink to access my remote shack
date: 2024-10-26 11:14:14.000000000 +01:00
categories: [Remote Shack] 
tags: [flex-6400, remote, virtual-private-server, vps, zerotier, vpn]
---

A consequence of the way my remote shack is connected to the Internet
stops me using [FlexRadio](https://www.flexradio.com)'s SmartLink to
access it.
I knew this would be the case, and I put in place a solution to give me
access from home; but it was only a partial solution because I couldn't
use the [Windows version of SmartSDR](https://www.flexradio.com/ssdr/).

This article details how I implemented a more general solution using a
[Virtual Private
Server](https://en.wikipedia.org/wiki/Virtual_private_server) and
[Zerotier](https://www.zerotier.com).

# Background

All Flex radios are LAN connected and support multiple ways to access
them from a network device running a variant of the
[SmartSDR](https://www.flexradio.com/ssdr/) software.
You can "Discover" the radio from SmartSDR running on a Windows PC - but
_only_ if it's on the same IP subnetwork.
If you are using the excellent [SmartSDR for
Mac](https://roskosch.de/smartsdr-for-mac/) - which I do most of the
time - you can also access the radio using a specific IP address - which
overcomes the restriction in SmartSDR for Windows.

For both clients, you can also access the radio using FlexRadio's
proprietary SmartLink protocol.

Unfortunately, there are situations where none of these solutions work;
and I am in one of those situations.

# Problem

SmartLink is a deceptively simple protocol that enables a radio to
register its presence in a central directory so that suitably
authenticated SmartSDR clients can lookup the IP address of the radio;
and then connect to it.
Provided the user can open a couple of ports in the firewall, this
approach works for most installations because the radio is sitting on a
home LAN behind a simple router/firewall that implements [Network
Address
Translation](https://en.wikipedia.org/wiki/Network_address_translation).

Unfortunately - [as detailed elsewhere on this
Blog]({% post_url 2023-04-06-setting-up-a-remote-station-part-6-remote-access %}) -
my remote shack is located behind two firewall routers - of which I have
control over only one.
On top of that, the Internet Service Provider to which my kind farmer's
router is connected implements
[CGNAT](https://en.wikipedia.org/wiki/Carrier-grade_NAT); which also
screws up SmartLink.

# The Partial Solution

As an interim solution to access my [Flex
6400](https://www.flexradio.com/products/flex-6400-signature-series-sdr-transceiver/)
from home, I setup an Layer 3 [Overlay
Network](https://en.wikipedia.org/wiki/Overlay_network) using
[Zerotier](https://www.zerotier.com) between my home
[Unifi](https://ui.com/uk/en/switching) USG router and the [Teltonika
RUT951](https://teltonika-networks.com/products/routers/rut951) at the
remote site. 

This created a VPN circuit between the two
sites - bypassing the farm's router - and allowing me to "see" the
Flex.

However, this only works for SmartSDR for Mac because only this variant
allows one to specify the IP address of the radio. SmartSDR for Windows doesn't work because it can only use either
Discovery or SmartLink to the radio.

Discovery requires the PC and the Radio to be on the same Level 3
network - which they aren't; and SmartLink "sees" the IP address of my
router, not the IP address of the farm's router (plus of course, there
is no route).

# The Full Solution

To enable SmartSDR for Windows to work, I need to get the radio to
advertise a publicly accessible IP address.

My solution is to implement a [Virtual Private
Server](https://en.wikipedia.org/wiki/Virtual_private_server) in the
cloud and set it as the default route for my remote shack.

This is how I did it.

# The Virtual Private Server

After some research, I settled on [IONOS](https://www.ionos.co.uk) as a
hosting provider.
My needs are very basic and they are very cheap.
Also, their technical support is responsive and they're located in the
UK: which helps to reduce [network
latency](https://en.wikipedia.org/wiki/Network_delay).

## Ansible and Debops

I use [Ansible](https://docs.ansible.com) and
[Debops](https://docs.debops.org/en/stable-3.2/) to manage the
configuration of the many servers and network devices under my control.
I won't go into details as there are plentiful sources of excellent
tutorials, but in brief: Ansible allows me to define the configuration
of the VPS in a series of text files on my computer and then "build" the
VPS with a single command.\
Debops builds on Ansible to deliver a comprehensive suite of
configurations for [Debian](https://www.debian.org) based servers.

The files are themselves managed in a [git](https://git-scm.com)
repository, and if I want/need to change the configuration, I simply
update the text files and run the command again.

Crucially, it means I don't have to worry about backing up the VPS.
If it gets compromised or corrupted, I destroy it and recreate it from
scratch with a single command.

It's brilliant!

## The VPS Configuration

The VPS is a smallest IONOS provides; with 1GB RAM and 10GB of disk.\
The Ansible and Debops configurations:

- Harden the VPS against attacks
- Install Zerotier and configure it
- Install nginx and configure a Reverse Proxy to give me access to the
  Raspberry Pi that controls the remote shack.
- Configure the WAN firewall to forward traffic on the Smartlink ports
  to the remote site.

# At the remote site

The RUT-951 supports Zerotier out of the box, so all I needed to do was
connect it to the Zerotier network and configure it to use the VPS as
its default route.

And that was it. When I run SmartSDR, I see the IP address of the VPS
and I can connect to it.

Simples.

