---
title: "Timer expiry: when does it fire and what is latched?"
type: question
status: answered
tags: [saturn, timers, interrupts]
---

# Timer expiry: when does it fire and what is latched?

Mastracci only says each timer "will decrement each cycle, until it reaches
zero, at which time it will perform a function" chosen by the control bits
(src: [[sources/mastracci-saturn-guide]] 4.10).

Need for [[hardware/timers]]:

- Does TIMER2 fire on reaching 0 or on underflow past 0 (sign bit)?
- Does the timer keep counting after expiry (wrap) or stop?
- Which control bit records the expiry (the "service request" bit 3?)
- Does TIMER1 run while the CPU is in SHUTDN, and does TIMER2?
- Are writes to TIMER2 nibble-atomic? The ROM writes 8 nibbles while it
  counts.

## Progress

- Voyage: control bit 3 is "timer needs service", set when the interrupt has
  occurred and used by the handler; bits 1 and 2 enable interrupt and wake on
  reaching zero (src: [[sources/voyage-48gx]] p. 203).
- TIMER2 counts from #FFFFFFFF down to #00000000; "le passage de l'horloge
  par la valeur #00000000 provoque une interruption" (p. 204), which suggests
  the event is the transition through zero (wrap), not arrival at zero.
- TIMER2 run bit is #12F bit 0; the ROM halts if it is clear (p. 203).

Still open: precise moment (arrival at 0 vs wrap), whether TIMER1 has a run
bit, behaviour during SHUTDN, write atomicity. Next: [[emulators/emu48]]
CHANGES.

## Answer (TIMER2)

The TIMER2 event is tied to a change of the counter's most significant bit:
it fires when TIMER2 counts down through zero into #FFFFFFFF, and while the
interrupt is pending a read returns #FFFFFFFF (src: [[emulators/emu48]]
CHANGES SP8, SP43). TIMER1 decrements after each full 1/16 s period,
reloading the same value does not restart the period, and TIMER1 runs only
while TIMER2 runs (SP4, SP12, SP39). Remaining detail (exact TIMER1 expiry
point, nibble-write atomicity of TIMER2) is best checked by running a ROM.
