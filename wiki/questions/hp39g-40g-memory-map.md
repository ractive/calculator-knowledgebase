---
title: "HP 39G/40G: memory map, ROM banking and ROM file layout"
type: question
status: open
tags: [39g, 40g, memory, rom]
---

# HP 39G/40G: memory map, ROM banking and ROM file layout

Known: 49G-based, 1 MB ROM and 256 KB RAM (src:
[[sources/giesselink-emu48-25-years]]); display ghost registers at #8068D
(src: [[sources/saturn-tutorial]] p. 166); Emu48 mirrors ROMs smaller than
2 MB (src: [[emulators/emu48]] CHANGES SP19).

Open:

- Is the RAM on NCE2 at #80000-#FFFFF like the 49G's IRAM, and are CE2 and
  NCE3 (the 49G's port 1 ERAM) unconnected?
- Is the 1 MB ROM 8 banks of 128 KB behind the 49G's CE1 latch, with banks
  8-15 mirroring 0-7? Does any code path probe flash (NCE3 plus #11C bit
  3) and expect an answer?
- Is hpcalc's `rom.39g` (2,097,152 bytes) 1 MB unpacked or 2 MB packed with
  the image mirrored? Check after download: all bytes below #10 means
  unpacked. Does it carry the I/O window from an upload (bytes
  at #00100-#0013F)?
- Is the beta ROM in `emu48-39.zip` laid out differently from the release
  ROM ([[sources/hpcalc-rom-listings]])?

How to settle: inspect the file, then boot it on the 49G model with 256 KB
RAM and no ERAM, logging CONFIG and the bank latch reads. Context:
[[hardware/hp39g-40g]], [[hardware/hp49g]].

## Answers from a boot on saturnus (2026-10-05)

Booted on the clean-room saturnus emulator with the 49G wiring cut down;
no oracle, so this shows what the ROM accepts, not the hardware. Details on
[[hardware/hp39g-40g]] "Facts settled while building saturnus".

- **RAM.** The ROM configures NCE2 as 256 KB at #80000 and never
  configures CE2 or NCE3.
- **ROM banks.** The ROM switches banks through the 49G's CE1 latch,
  configuring CE1 at #7E000 only around each switch. It runs with high
  bank 9 and scans banks 0-7, which works with banks 8-15 mirroring 0-7. No
  flash probe was seen: #11C bit 3 is written 0 once and NCE3 never
  configured.
- **The file.** hpcalc's `rom.39g` is 1 MB unpacked, one nibble per byte,
  and carries an upload's I/O window at #00100-#0013F.
- Still open: the beta ROM in `emu48-39.zip`.
