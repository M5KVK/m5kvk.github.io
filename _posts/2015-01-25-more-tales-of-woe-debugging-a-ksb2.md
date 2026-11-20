---
date: 2015-01-25 12:00:00 +0
title: More tales of woe debugging a KSB2
categories: [Construction]
tags: [elecraft, e2, kdsp2, ksb2]
---

For a couple of months now, I\'ve been trying to get to the bottom of
the problem with my KSB2 board: the SSB adapter in my K2. Not
continuously of course: I\'ve had all sorts of other things to do. 

I\'ve
built the KIO2, KDSP2, and KAT2 modules as well. However, these were
really just ways of resting my brain from the toil of trying to get the
KSB2 working properly.

The problem manifests itself as an almost, but not quite, total absence
of RF when I use SSB. The K2 works fine on CW.

Looking at the circuit, when the KSB2 is installed, it intercepts the
Rx IF path with a controlled bypass around the built in variable
bandwidth IF filter. On Tx, the audio from the Mic (which is unused on
CW) is processed and turned into a DSB (Double Side Band) signal at the
Intermediate frequency before passing through the new filter backwards
(which filters out the unwanted sideband) and then passing back into the
main circuit.

The user can choose whether the received signal passes through the
variable bandwidth filter on the RF board, or through the fixed filter
on the KSB2 board. On transmit, only the fixed filter on the KSB2 board
is used.

Thus, the fixed filter is used on Rx and Tx. I reasoned, wrongly I now
suspect, that because I could choose the fixed filter on SSB receive,
and hear signals, I could exclude the filter as being the source of the
problem.

That left the audio processing section of the KSB2 board, and the ALC
(Automatic Level Control) circuit. The latter is different from that
used on CW and serves to adjust the power of the RF signal to match the
level chosen by the user. Because we are talking about a single side
band signal, all of the output is in the frequency-translated audio
signal.

The output power is directly proportional to how load you talk
into the microphone. To get better \"punch\" to get through
interference, fading and other signals, hams often raise the Mic gain to
the point where they risk \"over-driving\" the Power Amplifier and
sending out a distorted signal. This is \"a bad thing\" but is often by
hams.

As the Mic gain is increased, the PA tries to maintain itself in a
linear configuration (to minimise distortion) by \"feeding back\" a
proportion of the output signal to an earlier stage and use that signal
to lower the gain of the PA. This is the ALC signal.

Most manufacturers have a front panel meter that indicates the magnitude
of the ALC signal and leave it to the user to adjust the Mic gain to a
point where the ALC is at their desired level. Elecraft has chosen to
adopt a smarter approach. The user uses a menu to choose a desired level
of over-driving and the rig then operates automatically to maintain that
level.

The K2 uses different ALC circuits on CW and SSB, so I suspected this
section of the KSB2 because if it wasn\'t operating properly, it could
suppress the drive to the PA too much and result in insufficient RF
power: exactly the symptom I was observing.

I spent ages trying to understand the ALC circuit and determining if it
was working properly. To get anywhere, I had to assure myself that all
the preceding circuits were working properly: Mic processing, DSB
generation in the mixer and sideband removal in the filter. To do this,
I needed a standard to work against.

From the Elecraft list I determined that 100mV of AF at 1kHz should
produce 100% of rated power at whatever level I chose. i.e. if I wanted
5W I should get 5W. I set up a signal generator to inject a 100Hz
sinewave at 100mV pk to pk into the mic socket and then used my
oscilloscope to trace the signal through. It soon became apparent that
the problem lay after the first mixer. Although I was seeing 1V pp at
the output of the first mixer, I was seeing virtually nothing in the
filter.

I don\'t have the test gear to debug the filter, so I have reached out
to the email list for assistance.
