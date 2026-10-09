---
title: "Does CE1 (bank switcher) or CE2 (port 1) win when they overlap?"
type: question
status: open
tags: [saturn, memory]
---

# Does CE1 (bank switcher) or CE2 (port 1) win when they overlap?

- Voyage: priority follows the bus order I/O RAM, RAM, bank switcher (CE1),
  port 1 (CE2), port 2 (NCE3), ROM (src: [[sources/voyage-48gx]] p. 86-87,
  205).
- Giesselink: priority is "different from the daisy chain order":
  HDW, NCE2, CE2, CE1, NCE3, NCE1 (src: [[sources/saturn-tutorial]] p. 151).
- Mastracci: HDW, RAM, CE2, CE1, NCE3, ROM (src:
  [[sources/mastracci-saturn-guide]] 2.4).

Two against one, and Giesselink explicitly contrasts priority with chain
order. Matters only when CE1 and CE2 windows overlap, which the stock ROM
avoids. Verify in [[emulators/emu48]].

Emu48 corrected the CE1/CE2 access and unconfigure priority once (SX: port 1
vs port 2; GX: port 1 vs bank select), but the changelog does not say which
wins (src: [[emulators/emu48]] CHANGES SP5). Still open.
