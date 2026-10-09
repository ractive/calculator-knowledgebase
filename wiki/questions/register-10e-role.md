---
title: "What does I/O register #10E really control?"
type: question
status: open
tags: [saturn, interrupts, card-ports]
---

# What does I/O register #10E really control?

Mastracci: #10E bits are software interrupt, set module pulled, run card
detect, enable card detect (src: [[sources/mastracci-saturn-guide]] 4.3).
Brittenson's light-sleep code (quoted by Ervin) names #10E `event_mask`, writes
8 before SHUTDN and restores #C after (src: [[sources/keyboard-ervin]]
Appendix A). Writing 8 sets only "enable card detect" under Mastracci's
naming. Which events wake the CPU from SHUTDN, and how does #10E select them?

Duchesne calls #10E the "configuration nibble": the ROM handler reads it
before writing, and after a card is removed it writes C to #10E repeatedly
until HST.MP reads 0 (src: [[sources/duchesne-interrupts-en]] p. 12). This
fits Mastracci's card-detect bits and suggests that card detection is what
sets MP via *NINTX.

Voyage: #10E configures the card-detection circuit; do not modify it or the
system halts; bit 3 must always be 1 (src: [[sources/voyage-48gx]] p. 196).
That fits Brittenson's light-sleep code: it writes 8 (only bit 3) before
SHUTDN and #C (bits 3 and 2) after, i.e. it turns off "run card detect"
(Mastracci's bit 2) while asleep. Remaining doubt: whether bit 0 ("software
interrupt") can trigger an interrupt, which DisableIntr would need.

Emu48 names the #10E bits SMP, SWINT and ECDT and makes a card change set MP
and pull NINT low; CARDSTAT (#10F) reads 0 while card detection is disabled
(src: [[emulators/emu48]] CHANGES SP16, SP19). SWINT is the software
interrupt Mastracci lists as bit 0, which is how DisableIntr can raise an
interrupt on purpose (inferred).
