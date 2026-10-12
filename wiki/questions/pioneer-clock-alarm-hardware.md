---
title: How do the 17B, 17BII and 27S keep time and wake for an alarm?
type: question
status: open
tags: [lewis, timers, clock, pioneer]
models: [17b, 17bii, 27s]
sources:
  - "[[sources/hp17bii-om]]"
  - "[[sources/hp27s-om]]"
  - "[[sources/garnier-hp42s-new-facts]]"
---

# How do the 17B, 17BII and 27S keep time and wake for an alarm?

The manuals document a clock that runs while the calculator is off,
regulated by a quartz crystal to within three minutes a month, and
appointment alarms that beep and light the ((•)) annunciator "even if
the calculator was off" (src: [[sources/hp17bii-om]] p. 136, 218;
[[sources/hp27s-om]] p. 143, 196). Nothing says how.

What is known of the Lewis ([[hardware/lewis]]): a 32.768 kHz crystal, a
4-bit TIMER1 at #403F7 and a 32-bit TIMER2 at #403F8-#403FF counting down
at 8192 Hz, control nibbles at #4030E/#4030F with INT and WAKE bits laid
out as on the 48 (src: [[sources/garnier-hp42s-new-facts]] 2.4; 42S ROM).
On the 48 the time of day is a RAM counter plus TIMER2, and an alarm is a
TIMER2 expiry with WAKE set ([[hardware/timers]]).

Open:

- Does the 17B/17BII/27S ROM keep the time the 48's way (a RAM tick base
  plus TIMER2 read on demand)? Where is that base in RAM?
- Is an alarm a TIMER2 expiry with the WAKE bit set, so the CPU leaves
  SHUTDN, beeps and shows the message, or does the ROM wake periodically?
- What does TIMER1 do on these models?

How to settle it: run a 17BII or 27S ROM in an emulator, set the clock and
an appointment one minute ahead, turn the calculator off, and watch the
writes to #4030E-#4030F and #403F7-#403FF before SHUTDN and what wakes the
CPU. A ROM is needed; see [[hardware/saturn-models]] ROM availability.
