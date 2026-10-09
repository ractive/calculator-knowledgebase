---
title: "Gießelink, Emu42 Manual"
type: source
authors: ["Christoph Gießelink"]
year: 2025
raw: "raw/emulator-docs/emu42/Emu42.txt"
status: digested
tags: [emu42, lewis, 42s, pioneer]
---

# Gießelink, Emu42 Manual

The manual of Emu42, the emulator of the Pioneer series (10B, 14B, 17B,
17BII, 20S, 21S, 22S, 27S, 32S, 32SII, 42S) and the Clamshell 19BII and 28S.
Documentation shipped with an emulator: a fact source under the clean-room
rule; the GPL source is not. Read in full on 2026-10-05.

Useful parts (cite by section):

- 1 General: the calculators "are based on the 1LR2 Lewis, the 1LR3
  Sacajawea or on the 1LU7 Bert chip". The 42S is a Lewis machine (the KML
  `Hardware "Lewis"`, [[sources/kml20]] Global).
- 3 ROM Images: HP's ROMs are copyrighted and not distributed. Images are
  packed (even address in the low nibble) or unpacked (one nibble per byte);
  `LEWISCRC` checks their built-in checksums ([[sources/emu42-pioneer-dump]]).
- 8.6.1 Authentic Calculator Speed: "on the Lewis chip depending on the RATE
  control register content".
- 8.6.3 Sound: the beeper output ports are emulated; the firmware measures
  its own CPU strobe frequency at a cold or warm start and stores it in the
  `=CSPEED` variable, which sets beep frequency and duration. Emu42's CPU
  frequency is a registry value times 16384 Hz.
- 8.6.4 Infrared Printer: output to an HP 82240A/B printer simulation.
- 9.1/9.2: the 42S's user code is HP-41 compatible with a zero byte after
  each number.

Facts used on [[hardware/lewis]] and [[hardware/hp42s]].
