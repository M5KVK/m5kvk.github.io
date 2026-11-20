---
title: Setting up a Remote Station -- Part 8 networking issues 
date: 2023-04-11 17:02:58.000000000 +01:00
categories: [Remote Shack]
tags: [construction, remote, rut-951]
---

I\'m seeing some strange networking issues.\
On a couple of occasions, I\'ve lost communication to the remote
station.

Currently, the remote station is sitting on the bench, and the RUT-951
router is connected to my home LAN via Wi-Fi.\
This is also the desired final situation.

On one occasion, the Pi and Windows PC lost comms to the Internet, even
though I could access them remotely using `ssh` or
`Microsoft Remote Desktop` respectively.
The only interpretation is that the router lost its default route (which
is to the home LAN\'s gateway).
However, running the `route` command on the router showed the proper
routing table. All very odd.

The most likely cause is that I had the router sitting on top of the 12v
PSU. I\'ve moved it now.

Luckily, one of my reasons for choosing this router now comes to the
fore.
Being a router designed for remote installation, it has services built
in that enable it to recover from some problems:

- One is a service to `ping` a defined IP address (in my case `8.8.8.8`)
  and reboot if a pre-determined number of attempts fail to get a
  response.
- The second is that you can set the router to reboot on a schedule.

I have now enabled both services, with the router rebooting at 3AM.
