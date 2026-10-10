---
title: "Emu48 CHANGES.TXT (service packs 1-68)"
type: source
authors: ["Christoph Gießelink"]
year: 2025
raw: >-
  https://hp.giesselink.com/Emu48/CHANGES.TXT (not in raw/; the change log
  shipped with the GPL Emu48 package)
status: digested
tags: [emu48, emulator, changelog]
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
---

# Emu48 CHANGES.TXT

The change log of Emu48 1.0 service packs 1 to 68, 5,400 lines. It is a
changelog, not code, and was the only file read in the Emu48 source tree.

**It carries no dates**, only service-pack numbers, so citations use the form
`(src: [[emulators/emu48]] CHANGES SP43)` instead of the date form suggested
in CLAUDE.md.

Read: every entry for the hardware-relevant source modules (memory and I/O,
timers, serial, display, keyboard, opcodes, engine, flash, beeper, files);
UI, debugger, bitmap and build entries were filtered out. The findings,
restated as hardware facts without function names, are on
[[emulators/emu48]].

## Reliability

High for behaviour: each entry records a fix made so that real ROMs and
programs run correctly, by the author of the tutorial's chapters 66-68
([[sources/saturn-tutorial]]). Not independent of that tutorial.
