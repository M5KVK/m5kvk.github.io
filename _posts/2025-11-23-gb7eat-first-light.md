---
title: GB7EAT - first light 
date: 2025-11-23 13:23:50.000000000 +00:00
categories: [Packet Radio] 
tags: [direwolf, gb7eat, packet-radio, xrouter, xrpi]
---

The plan is to start small and build up from there, so this is the starting position:

# Hardware

- [Raspberry Pi 4](https://www.raspberrypi.com) (I could have used a 3B
  but I had a Pi 4 sitting doing nothing).
- [Digirig Mobile](https://digirig.net/digirig-mobile-rev-1-9/) audio
  adapter.
- [Tait 8115](https://wiki.oarc.uk/radios:tait_tm8100) 25W Mobile Radio.
- Antenna - initially just my shack 2m/70cm co-linear in the back
  garden.
- PSU, cables, etc.

This is pretty much as simple as you can get; a computer to run the
software; an audio adapter to convert digital signals to audio; and a radio+antenna.

# Software

- [Xrouter Pi Edtion - XRPi](https://wiki.oarc.uk/packet:xrouter)
- [Direwolf](https://github.com/wb2osz/direwolf)

Again, as simple as I can get to begin with. I'm using the Direwolf sound card modem to start off with.

# Configuration

I started by installing the stock `bookworm-lite` edition of the
Raspberry Pi OS - which is a slightly adapted version of Debian.
I don't need the graphical desktop as I'm more used to interacting with Linux via the console.

As with the [Remote Shack](https://garethhowell.com/wp/radios/my-remote-shack/), 
I created a dedicated [Zerotier](https://www.zerotier.com) 
network to which the node and my home Mac are connected.

The Zerotier "SNOPAC" network uses the address space `192.168.192.0/24`
and m5kvk-5 has the address `192.168.192.3`.
It is this network that I use for all remote access rather than the local VLAN address. 
That way, if the node is moved to a different
location - and LAN - it will still have the same IP address.

The second step was to `bootstrap` the node using
[debops]({% post_url 2025-10-30-building-the-test-node-with-ansible-and-debops %})
so that it had a known configuration. I then re-ran debops to install
XRPi and Direwolf and configure them to run as `systemd` services under
[tmux](https://github.com/tmux/tmux/wiki).

The reason for this is that neither XRPi nor Direwolf are daemons, so
they need to be run from a console. By running them in tmux windows, I
can run them simultaneously and access them remotely.

The code for doing this was fairly simple:\
A systemd service unit that runs a script

    [Unit]
    StartLimitIntervalSec=5
    Description=XRpi service
    After=network.target
    StartLimitIntervalSec=0

    [Service]
    Type=forking
    Restart=unless-stopped
    RestartSec=5
    User=pi
    WorkingDirectory=/home/pi/xrpi
    ExecStart=/home/pi/xrpi/start_xrpi.sh

    [Install]
    WantedBy=multi-user.target

And the script `/home/pi/xrpi/start_xrpi.sh`

    #!/usr/bin/bash
    #
    /usr/bin/tmux new -d -s xrpi
    /usr/bin/tmux send-keys -t xrpi:0 '/home/pi/xrpi/xrpi64v504m-bullseye' ENTER

The pair of files for Direwolf are similar.

# direwolf.conf

This file configures direwolf. Mine is pretty simple:

    ADEVICE plughw:3,0
    #
    CHANNEL 0
    MYCALL GB7EAT-5
    MODEM 1200
    PTT /dev/ttyUSB0 RTS
    #
    AGWPORT 8000
    KISSPORT 8001
    #
    # Transmit FX25 instead of AX25
    #
    FX25TX 1
    #
    # How likely are we to Tx?
    #
    PERSIST 63
    # 
    # How often do we check?
    #
    SLOTTIME 12
    #
    # How many tries do we try to send a frame before giving up?
    #
    RETRY 5
    #
    # How long do we wait for a reply?
    #
    FRACK 3
    #
    # How many frames will we send before we wait for an acknowledgement
    #
    MAXFRAME 4
    #
    # How long should packets be?
    #
    PACLEN 128
    #
    # How long do we allow for PTT to take effect
    #
    DWAIT 0
    #
    # How long (10ms) to let the Tx stabilise
    #
    TXDELAY 30
    #
    # How do we wait at the end before dropping Tx
    #
    TXTAIL 15

The line `ADEVICE plughw:3,0` tells direwolf the address of the digirig
mobile, and comes from running the command:\
`aplay -l`\
To get the card (3) and device (0) of the dgirig.

# XROUTER.CFG

This is the main config file for XRPi.\
This is the version after I applied for and was allocated GB7EAT:

    # XROUTER.CFG - Main Configuration file for XRPi / XRLin
    #
    # This file is read only when the program boots.
    #
    #===================================================================
    #
    #       Primary Identity for AX25 & NetRom.
    #
    NODECALL=GB7EAT-5
    NODEALIAS=EATON1
    #
    #       QTH
    #
    QTH=St Neots, Cambs, UK, IO92uf
    LOCATOR=IO92uf
    #
    #       Console operations.
    #
    CONSOLECALL=M5KVK
    #
    #       Chat server.
    #
    CHATCALL=GB7EAT-8
    CHATALIAS=EATCHT
    CHATQUAL=100
    #
    #        PMS
    #
    PMSTYPE=4
    PMSCALL=GB7EAT-2
    PMSALIAS=EATBBS
    PMSQUAL=120
    PMSHADDR=.#22.GBR.EU
    #
    #       "Connection text", sent to anyone connecting to the alias
    #
    CTEXT
    M5KVK Test Node chat server
    Type '?' for list of commands.
    ***
    #
    # This text is the response to the 'I' command
    #
    INFOTEXT
    XRPi Packet Router, St Neots, Cambs, UK, IO92uf
    Sysop: Gareth M5KVK (M5KVK@GB7EAT.#22.GBR.EU)
    To connect to the BBS, use the command: BBS
    Comments/reports.queries to: M5KVK
    Website: https://m5kvk.org/packet/gb7eat
    ***
    #
    #
    #       This ID message is sent every IDINTERVAL.
    #
    IDTEXT
    !5213.72N/00016.79W XRPi EATON (GB7EAT-5), 44.33.3.152, Chat=GB7EAT-8/EATCHT
    ***
    #
    # Port used to access Telnet
    #
    TELNETPORT=8023
    # =====================================================================
    # Interface definitions - These MUST precede any port definitions
    # =====================================================================
    #
    INTERFACE=1
      TYPE=AGW
      MTU=256
    ENDINTERFACE
    #
    # =====================================================================
    # Port definitions. Each one begins with PORT and ends with ENDPORT
    # =====================================================================
    #
    PORT=1
      ID=(FSK) 144.950MHz 1200Bd TESTING
      INTERFACENUM=2
      CHANNEL=A
      MHEARD=50
    ENDPORT

Note that I am using the direwolf modem in AGW emulation. This appears
to be the most stable method.

# Conclusion

So, I have GB7EAT operational on 144.950MHz.

On the To-DO list are connections to the rest of the network,
replacement of the direwolf modem with a NinoTNC, connection to AMPRnet
and addition of a high speed 70cms link.

Also on the list for longer term exploration are a move of the node to a
better physical location, and addition of higher speed links.

