---
title: "Gießelink, Emu42 CHANGES.TXT"
type: source
authors: ["Christoph Gießelink"]
year: 2026
raw: "raw/emulator-docs/emu42/CHANGES.TXT"
status: skimmed
tags: [emu42, lewis, registers]
---

# Gießelink, Emu42 CHANGES.TXT

The Emu42 changelog, written per source file (v1.33 back to the first
releases). It narrates code changes, so it was searched only for hardware
names and behaviour, never for structure. Facts found (cite by the release
the entry belongs to; the entries name the register by offset):

- LPD (0x308) has bits GRAM, VLBI and LBI; on the second Lewis of a
  Clamshell machine VLBI and LBI always read 0.
- LPE (0x309) has an EGRAM bit.
- DSPCTL (0x303) holds DON; a DON change also switches the annunciators
  off on the Clamshell machines.
- LEDOUT (0x30D) has an STL bit.
- Only the Lewis has the RATE control (CPU speed); the timer2 register has a
  read access scheme Emu42 analyses.
- Two Lewis chips (master and slave) on the Clamshell machines.

Used on [[hardware/lewis]].
