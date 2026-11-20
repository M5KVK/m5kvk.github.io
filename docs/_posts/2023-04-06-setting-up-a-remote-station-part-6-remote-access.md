---
title: Setting up a Remote Station -- Part 6 Remote Access 
date: 2023-04-06 15:01:29.000000000 +01:00
categories: [Remote Shack]
tags: [flex-6400, openvpn,raspberry-pi, remote, rut-951, teltonika]
---

In this post I am going to discuss the need for a local Windows PC and
considerations for remote access.

# Local Windows PC {#local-windows-pc .wp-block-heading}

I allowed for a local Windows PC in the original spec, but I was hoping
to not need it. However, after watching [Mike, VA3MW\'s YouTube video on
his remote
station](https://www.youtube.com/live/_QxapW0bMLc?feature=share), I changed my mind. Mike\'s
arguments in favour were:

- running Ham-related software locally reduces Internet traffic and latency; and,

- having everything local opens up the potential for others to access and
  use the station with minimal trouble

So, I stumped up for a [Mini IT8 from
Geekom](https://www.geekompc.com/geekom-mini-it8/). This is a micro-PB with an Intel i5, 16GB RAM and
512GB SSD. More than enough for my needs. It runs Windows 11
(unfortunately), but I'm gradually getting used to its foibles.

So far I\'ve installed:

- [VE7CC DX Cluster
  software](https://en.freedownloadmanager.org/Windows-PC/Ve7cc-FREE.html)

- [Log4OM](https://www.log4om.com) logging software

- [SmartSDR for
  Windows](https://www.flexradio.com/ssdr/ssdr-for-windows/) to drive the Flex locally

- [WSJT-X](https://wsjt.sourceforge.io/wsjtx.html)

The PC is logged into my Microsoft account so the OneDrive replicates to
my home QTH.

I'll access the remote PC over [Microsoft\'s Remote Desktop
client](https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) - remembering to play audio
on the remote server and not back on the home station.

It's worth noting that I'm only proposing to use the remote
installation of SmartSDR for digital modes. For voice modes and CW I'll
use SmartSDR on my home Mac and use FlexLink to access the Remote Station.

# Remote Access

Given that I am designing a Remote Station, access to the Remote Station
is a major aspect of the design. The primary requirement is for remote
access from my home QTH, but I also want access when I am on the road.

Normally, this would be relatively easy to achieve because the RUT951
would be directly connected to the Internet and I could either open
ports or set up a VPN gateway.

In my case though, it is hoped that the Remote Station will be connected
to the host's LAN (as it is at home on the bench), in which case there
is an additional router/firewall in the path to the Internet. I cannot
assume that I have any potential to control this host-LAN router, so I
cannot forward ports or use something like UPnP.

So, what I am doing is to have the RUT951 establish a VPN back to my
home network. That way:

- I don't need to worry about the public IP address of the remote
  station; and,

- once the VPN is established, I have full access to the remote station's LAN

My Home LAN already has a [OpenVPN](https://openvpn.net) 
gateway to allow me remote access to the LAN when I am on the road.
Hopefully, with this arrangement I can VPN into the Home LAN and also
get access to the Remote LAN. We'll have to see what effect this has on audio latency etc.

## Implementation

### At the Server end

- The home QTH OpenVPN server is reachable from the Internet.
- The Home QTH LAN is 172.29.12.0/24
- The VPN Server\'s Home QTH LAN IP is 172.29.12.11
- The VPN uses 10.8.0.0/24
- The VPN server uses the following server.conf (relevant content only)

<!-- -->

    dev tun 
    topology subnet 
    server 10.8.0.0 255.255.255.0 
    push "dhcp-option DNS 172.29.12.1" 
    route 10.29.5.0 255.255.255.0 
    push "route 172.29.12.0 255.255.255.0"
    push "route 0.0.0.0 0.0.0.0" 
    client-to-client

- The Home QTH\'s router has a static route to 10.29.5.0/24 using the
  gateway 172.29.12.11

### At the Remote Station

- The LAN has the address space 10.29.5.0/24
- The RUT951 is 10.29.5.1
- Once connected to the VPN, the RUT is also 10.8.0.5

With this config, the remote LAN is reachable from the Home LAN and vice-versa.
