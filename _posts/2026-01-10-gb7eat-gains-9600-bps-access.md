---
layout: post 
title: GB7EAT gains 9600 BPS access 
date: 2026-01-10 
categories: [Packet Radio] 
tags: [direwolf, gb7eat, ninotnc, packet-radio, xrouter]
---

Since my last post, there has been a period of settling in for GB7EAT, and now some updates.

When I started, I was using the
[Direwolf](https://github.com/wb2osz/direwolf) sound card modem as the
interface to the radio. However, I was getting some issues relating to
packet corruption, so I replaced this with a
[NinoTNC](https://tarpn.net/t/nino-tnc/nino-tnc.html) that I built up
from a kit and put in a 3D printed case.

It's been running nicely with this, but the NinoTNC does not support
[FX.25](https://en.wikipedia.org/wiki/FX.25_Forward_Error_Correction):
the upgrade to [AX.25](https://en.wikipedia.org/wiki/AX.25) that adds
[Forward Error Correction -
(FEC)](https://en.wikipedia.org/wiki/Error_correction_code) to improve
throughput. Also, it only supports one mode at a time.

I wanted to add high speed access on the same frequency, so I switched
back to using Direwolf and implemented a hack I found on [The Modern
Ham](https://themodernham.com/share-soundcard-between-direwolf-and-vara-on-linux/)'s
website that allows you to run multiple modems on the same sound card.
In the hack shown on the website, the purpose is to run Direwolf and
[VARA](https://rosmodem.wordpress.com) on the same sound card, but the
principle applies equally to Direwolf.

What I did was as follows:

## Allow the sound card to be shared

The output from `cat /proc/asound/cards` showed:

    0 [vc4hdmi0       ]: vc4-hdmi - vc4-hdmi-0
                          vc4-hdmi-0
     1 [vc4hdmi1       ]: vc4-hdmi - vc4-hdmi-1
                          vc4-hdmi-1
     2 [Headphones     ]: bcm2835_headpho - bcm2835 Headphones
                          bcm2835 Headphones
     3 [Device         ]: USB-Audio - USB Audio Device
                          C-Media Electronics Inc. USB Audio Device at usb-0000:01:00.0-1.3.2, full speed

Thus my DigiRig sound card is device 3

I created the file `/etc/asound.conf` (because it didn't already exist)

    pcm_slave.digirig {
       pcm {
          type hw
          card Device
       }
       period_time 0
       buffer_size 8192
    }

    pcm.digirig-dmix {
       type dmix
       ipc_key 2023041901
       slave "digirig"
       bindings.0 0
    }

    pcm.digirig-dsnoop {
       type dsnoop
       ipc_key 2023041902
       slave "digirig"
       bindings.0 0
    }

    pcm.digirig-rx {
       type plug
       slave.pcm "digirig-dsnoop"
       hint.description "digirig RX audio plug"
    }

    pcm.digirig-tx {
       type plug
       slave.pcm "digirig-dmix"
       hint.description "digirig TX audio plug"
    }

This creates two 'virtual' sound devices `digirig-rx` and `digirig-tx`

## Modifying direwolf.conf

After rebooting, I could then modify my `direwolf.conf` to read
(partially)

    ADEVICE digirig-rx digirig-tx
    #
    CHANNEL 0
    MYCALL GB7EAT-5
    MODEM 1200
    PTT /dev/ttyUSB0 RTS

i.e. changed the name of the audio device.

## Changing how I used direwolf

The Direwolf sound card modem can be used in two modes: [AGW Packet
Engine (AGPWE)](https://www.sv2agw.com/Home/Packet) emulation and
[KISS](https://en.wikipedia.org/wiki/KISS_(amateur_radio_protocol)).
Previously, I was using AGWPE mode as follows (in `XROUTER.CFG`)

    INTERFACE=2
      TYPE=AGW
      MTU=256
    ENDINTERFACE

Investigation of the problem I was experiencing where other stations
were rejecting my AX.25 frames with an `FRMR` or "Frame Reject"
response, prompted me to change to using KISS mode. The appropriate
stanza now looks like:

    INTERFACE=2
      TYPE=TCP
      PROTOCOL=KISS
      IOADDR=127.0.0.1
      INTNUM=8001
      MTU=256
    ENDINTERFACE

The Port definition hasn't changed:

    PORT=2
      ID=(FSK) 144.950MHz 1200BPS Direwolf
      INTERFACENUM=2
      ;CHANNEL=A
      MHEARD=50
      IDPATH=APXR00
      IDTEXT=!5213.72N/00016.79W XRPi EATON (GB7EAT-5), Chat=GB7EAT-8/EATCHT
    ENDPORT

## Adding a second port on the same frequency

After checking that this all worked, I was able to add a second direwolf
instance fairly easily.

1.  Edit `start_direwolf.sh`

<!-- -->

    #!/usr/bin/bash
    #
    /usr/bin/tmux new -d -s direwolf
    /usr/bin/tmux send-keys -t direwolf:0 '/usr/local/bin/direwolf -c \              /home/pi/direwolf/direwolf1.conf' ENTER
    /usr/bin/tmux new-window -t direwolf
    /usr/bin/tmux send-keys -t direwolf:1 '/usr/local/bin/direwolf -c \   /home/pi/direwolf/direwolf2.conf' ENTER

2.  rename the original `direwolf.conf` file

<!-- -->

    mv direwolf.conf direwolf1.conf

3.  Copy and amend the second `direwolf.2.conf` file

<!-- -->

    cp direwolf1.conf direwolf2.conf
    vi direwolf2.conf

The relevant changes are:

    MODEM 9600
    #
    AGWPORT 8002
    KISSPORT 8003

Everything else stayed the same.

Now, when I reboot, `tmux` opens two windows and launches two distinct
copies of direwolf.

4.  Amend `XROUTER.CFG` to add the additional Interface and Port

<!-- -->

    INTERFACE=2
      TYPE=TCP
      PROTOCOL=KISS
      IOADDR=127.0.0.1
      INTNUM=8001
      MTU=256
    ENDINTERFACE
    INTERFACE=7
      TYPE=TCP
      PROTOCOL=KISS
      IOADDR=127.0.0.1
      INTNUM=8003
      MTU=256
    ENDINTERFACE

    PORT=2
      ID=(FSK) 144.950MHz 1200BPS Direwolf
      INTERFACENUM=2
      ;CHANNEL=A
      MHEARD=50
      IDPATH=APXR00
      IDTEXT=!5213.72N/00016.79W XRPi EATON (GB7EAT-5), Chat=GB7EAT-8/EATCHT
    ENDPORT
    PORT=3
      ID = (G3RUH) 144.950MHz 9600BPS Direwolf
      INTERFACENUM=7
      MHEARD=50
      IDPATH=APXR00
      IDTEXT=!5213.72N/00016.79W XRPi EATON (GB7EAT-5), Chat=GB7EAT-8/EATCHT
    ENDPORT
