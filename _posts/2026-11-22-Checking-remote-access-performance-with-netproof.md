---
title: Checking remote access performance with netproof
date: 2026-11-22 12:17:00
layout: post
categories: [Remote Shack]
tags: [remote, performance, netproof, zerotier, starlink]
---

Ever since I moved my Flex to the remote shack, audio performance has been a problem.

As a reminder, the remote shack runs its own 10.29.5.0/24 LAN with a [Teltonika RUT951](https://wiki.teltonika-networks.com/view/RUT951) router connecting it to the farm's LAN.
The Router runs [wireguard](https://wireguard.com) to create a VPN tunnel to [44net](https://connect.44net.cloud).

Until recently, the farm was connected to the Internet over a [Wireless Metropolitan Area Network](https://www.geeksforgeeks.org/computer-networks/overview-of-wireless-metropolitan-area-network-wman/) as it is too far from the nearest exchange for ADSL.
That changed when they migrated to [Starlink](https://starlink.com) and I had hoped for an improvement.

Sadly not. I still experience a lot of drop-outs.

I've used [iperf](https://iperf.fr) to look into the problem, but [FlexRadio's](https://www.flexradio.com) [Mike Walker - VA3MW](https://www.qrz.com/db/VA3MW) recently created - 
in partnership with [Claude](https://claude.ai/login) - a tool called [netproof](https://va3mw.github.io/netproof/) which is designed to test links to Flex radios. I downloaded it and gave it a try.

For reasons so far unresolved, it won't work over 44net - probably the firewall. 
I did try using the [Zerotier](https://zerotier.com) connection that I use for management.
The results using Zerotier are [here](/assets/img/NetProof.webarchive). _(hint: not good)_
