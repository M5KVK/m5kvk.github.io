---
date: 2014-07-07 12:00:00 +01:00
title: Making progress again
categories: [Construction]
tags: [elecraft, k2]
---

OK, we got past the problem with the VCO.

After tracing through the signals, I checked the levels on DIN and CLK
against the indicative levels in the manual. They were the wrong way up!

At this point I got on to the Elecraft mailing list and started asking
questions. Luckily, Don Wilhelm W3FPR was on hand to offer some advice.
Essentially, the main reason for the VCO not locking was likely to be
the values of L3 and T5 being such that the voltage swing on the PLL
couldn\'t get the VCO to change frequency enough to lock. The advice was
to either adjust L3 or change the turns on T5.

I didn\'t want to disturb T5 unless it was really necessary, so I did a
bit of thinking. The PLL is calibrated when the K2 is switched on for
the first time. The MCU swings the VCXO through its full range and
measures the relationship between PLL data and VCO frequency. It uses
the results to program the EEPROM U3. What if, I reasoned, if the EEPROM
calibration had not completed properly? I decided to give the rig a full
reset (hold keys 4-5-6 in when switching on).

Bingo! Immediately, the VCO locked on.

Next step was to adjust L3 to set the voltage range so that the VCO
works on all bands.

## More problems - my fault this time

I had to tweak T5 a bit, but I got the VCO to lock OK on some bands, but
not on others.

Turned out that I had put a 47nF capacitor in C74 instead of 47pF. Not
much difference!

Anyway, I changed it and all bands work OK.

Lastly, for this evening anyway, I got as far as checking the BFO
frequencies.
