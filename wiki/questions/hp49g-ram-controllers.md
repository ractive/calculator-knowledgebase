---
title: "HP49G: which controller maps which RAM block?"
type: question
status: answered
tags: [49g, memory]
---

# HP49G: which controller maps which RAM block?

Sousa's RAM table (block 0 CE0 #40000-#FFFFF, block 1 CE3 #80000-#FFFFF,
block 2 CE0 #00000-#3FFFF, block 3 CE2 #00000-#FFFFF) uses controller names
that do not match the HDW/RAM/CE1/CE2/NCE3 naming of the 48 (src:
[[sources/memory49-sousa]]). Smith only gives the normal OS mapping (src:
[[sources/hp49-memmap-smith]]). An emulator needs the real controller-to-chip
wiring. Check [[emulators/emu48]].

## Answer

NCE2 = 256 KB RAM for HOME/port 0 at #80000-#FFFFF; CE2 and NCE3 = the two
128 KB halves of port 1, unconfigured by default; NCE1 = flash; CE1 = bank
switcher (src: [[sources/saturn-tutorial]] p. 163-164). Sousa's table is not
reconcilable with this and is set aside.
