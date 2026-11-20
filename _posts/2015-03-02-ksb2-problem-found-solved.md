---
date: 2015-03-02 12:00:00 +0
title: KSB2 problem found, and solved
categories: [Construction]
tags: [elecraft, k2, ksb2]
---

I finally solved the problem with my KSB2 board. It turned out to be a
poorly soldered joint on one of the transformers that terminate the
filter. I found it by writing down everything I knew about the problem
and using that to systematically exclude different elements in the
system.

The final clue was that receive audio was poorer through the KSB2 filter
than through the variable filter on the RF board. Poorer, but not
absent. That pointed me back to the filter.

I was ready to rip the filter out and rebuild it from scratch, but
decided to have one more look at all the joints using an X3 magnifier. I
could just see that one of the leads from T1 primary had an annulus of
solder and that I could see the copper end of the wire. Sussed: I
obviously hadn't fully stripped the insulation when I made the
transformer. A continuity tester confirmed the problem: high, but not
infinite, resistance.

It was a 5 second job to re-solder the joint: leaving the iron on long
enough for the remaining insulation to bubble off. Just for
completeness, I re-did all the other solder joints as well.

The result is that the board now works fine. I'm not completely happy
with the performance, or of the filter alignment, but that's for the
next post.
