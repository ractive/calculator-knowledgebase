---
title: "HP49G bank latch: which address bits select which flash window?"
type: question
status: answered
tags: [flash, bank-switching, memory]
models: [49g]
---

# HP49G bank latch: which address bits select which flash window?

- Sousa: low window (banks 0-3 at #00000) selected by `base + #20*n` (address
  bits 5-6), high window (banks 0-15 at #40000) by `base + 2*n` (bits 1-4)
  (src: [[sources/memory49-sousa]]).
- Giesselink: A1-A2 select the low-window bank, A3-A6 the high-window bank;
  example value #64 = 1100 10 0 gives high bank 12, low bank 2 (src:
  [[sources/saturn-tutorial]] p. 163-164).

Giesselink wrote Emu48's 49G support and gives a worked example, so his
assignment was the working assumption until the ROM trace below.

## Answer (2026-10-05): Sousa's assignment

Settled by experiment on the saturnus emulator (clean-room, booting the
calculators' own ROMs; emulator behaviour, not a hardware measurement):

- With the latch storing nibble address bits A1-A6 of the latching access,
  **A1-A4 select the bank at #40000-#7FFFF and A5-A6 the bank
  at #00000-#3FFFF**, i.e. Sousa's `base + 2*n` (high view) and
  `base + #20*n` (low view) (src: [[sources/memory49-sousa]]).
- With Giesselink's assignment (A1-A2 low view, A3-A6 high view), ROM 2.10
  and ROM 1.19-6, both with the original 49G boot sector ("Boot Version
  1.A"), stop in their boot loader with "No System": the bank scan finds no
  OS. ROM 2.15 (the hpcalc.org emulator image) switches the low view to a
  wrong bank at its first latch write (#004CD, a `DAT0=C B`
  at #3F000 + 2) and runs into data within a million cycles.
- With Sousa's assignment all three boot to "Try To Recover Memory?", and
  2.15's screens then match saturnng pixel for pixel (boot, arithmetic,
  MODE form, every alpha letter, storing to and recalling from port 2).
- The boot code latches with byte writes into the CE1 window, so writes
  latch on the 49G, as Giesselink says (src: [[sources/saturn-tutorial]]
  p. 163-165).

Giesselink's worked example (#64 = high bank 12, low bank 2) therefore does
not describe the bit order the ROMs use, or uses another numbering; the
tutorial's text should be read with that in mind. Details on
[[hardware/hp49g]].
