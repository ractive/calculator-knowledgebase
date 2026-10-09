---
title: "DA19 (#129 bit 3): which value selects upper ROM?"
type: question
status: answered
tags: [48gx, memory, display]
---

# DA19 (#129 bit 3): which value selects upper ROM?

- Mastracci: "DA19 Control (set is card port 2, cleared is upper ROM)" (src:
  [[sources/mastracci-saturn-guide]] 4.2).
- Voyage: bit 3 of #129 at 0 gives access to the full 512 KB ROM; at 1 the
  first 256 KB is duplicated at #80000, the S/SX-compatible mode (src:
  [[sources/voyage-48gx]] p. 202).
- Tutorial (Giesselink): "CBIT=1 7 % DA19=1, enable ROM" to switch to upper
  ROM, "CBIT=0 7 % DA19=0, disable ROM" to switch to port 2 (src:
  [[sources/saturn-tutorial]] p. 159).

Mastracci and Voyage agree with each other (0 = upper ROM); the tutorial's
code says the opposite. One possibility: the tutorial example manipulates the
OS ghost byte, whose meaning might be inverted, but it writes the same byte
to #128. Must be settled from [[emulators/emu48]] or a ROM trace.

## Answer

DA19 = 1 gives A19 to the ROM (upper ROM visible); DA19 = 0 disables upper
ROM, mirrors the lower 256 KB at #80000, and lets NCE3 select port 2 when
BEN = 1 (src: [[emulators/emu48]] CHANGES SP9, SP16). This matches the
tutorial's code (same author) and Emu48 runs the real ROMs with it, so
Mastracci 4.2 and Voyage p. 202 have the polarity reversed. Voyage's
description of the DA19 = 1 state ("first 256 KB duplicated at #80000") is
in fact the DA19 = 0 state.
