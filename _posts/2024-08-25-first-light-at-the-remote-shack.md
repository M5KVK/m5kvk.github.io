---
title: First Light at the Remote Shack 
date: 2024-08-25 14:15:06.000000000 +01:00
categories: [Remote Shack] 
tags: [remote]
---

It's been so long since I last updated this project, you could be
forgiven for thinking that I'd abandoned it. Apologies for that.
However, the good news is that the remote shack is up and running.
The performance needs to be improved, but it works.

I soak tested the whole set of kit in the back garden for nearly a year
so that I could be sure that everything worked, and could be recovered
if something untoward happened. It also gave me the opportunity to test
the environmental controls across summer and winter. *As it turned out,
I didn't learn enough*.

## Environmental Changes

The main change was to insulate the inner cabinet with some aluminium
backed polystyrene sheet (the sort of the stuff sold to go down the back
of radiators) and add a controlled 60W heater in the base of the inner
cabinet. Based on the performance last summer, I didn't need any forced
ventilation - though I left provision for it in the system design.

Now that it's in it's final position, I now know I do need forced
ventilation as well. I'll fit something next time I visit.

## Moving to the Farm

I moved the shack to the farm in June, but immediately realised that my
original site survey had been invalidated by the erection of a new
steel-framed barn. After a lot of wandering around with my phone testing
WiFi signal strength, I realised there was nowhere I could place the kit
and connect directly to the farm's WiFi. That required a re-think.

Luckily, I was able to negotiate a new position close to an external
power socket and with easy cable runs to the main farm building and
where I wanted to put the antenna. However, I did need to re-design the
connection to the farm's Wi-Fi network as there was no line-of-site link
to the nearest Access Point.

I bought a [TP-Link CPE210 PoE Wireless Access
Point](https://www.tp-link.com/uk/business-networking/outdoor-radio/cpe210/).
This device can be placed in client mode to become an Ethernet connected
wireless interface for the RUT951 router. 

I placed the CPE210 physically
close to the farm's main Access Point and ran a 40m long Ethernet cable
back to the WAN port of the RUT951 in the cabinet. It works perfectly
and I'm getting about 20ms ping times to google.com. Throughput is not
really important, but I'm getting about 10 MByte/s down and 500 kByte/s
up.

## Antenna

I've changed my mind about hanging a big doublet in the trees for now
and have instead erected a multi-band vertical.
Initially I looked at DX-Commander, but then realised that I had a [18m
Spiderbeam
pole](https://www.spiderbeam.com/product_info.php?info=p71_Spiderbeam%2018m%20fiberglass%20pole.html)
in the shed.\
With a wire running up it and the SG-230 ATU at the bottom ( plus
radials), it has the potential to provide a 80m-10m antenna.

## First Light

The newly located system went live in the middle of July.
I'll do a video run through of the station as it appears from home when I get time.

## Still to do are:

- The antenna is not performing as well as I hoped - the wire length
  needs to be adjusted - and base noise levels are higher than I hoped -
  though considerably better than from home.

- With the compromised method of getting Internet access (i.e. via
  somebody else's wireless network), I am suffering from double-NAT: IP
  addresses are being translated twice whereas NAT only occurs once in
  usual cases. This prevents me exploiting the UPNP feature on the
  farm's router to open a temporary hole in the firewall for SmartLink.
  As a result, I can't access the Flex using SmartLink. I can get in
  using a VPN connection using ZeroTier, but the performance is
  insufficient for voice traffic.

To get around this, I am implementing a cloud-based Virtual Private
Server that will be an access server for the remote shack. All traffic
to and from the shack will transit the VPS where I can control fire
walling and other aspects. More on that another time.
