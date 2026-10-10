---
title: "Gießelink, Pioneer and Clamshell ROM dump notes, LEWISCRC"
type: source
authors: ["Christoph Gießelink"]
year: 2009
raw: "raw/emulator-docs/emu42/PIONEER.TXT"
status: digested
tags: [emu42, lewis, rom, ir]
models: [42s]
---

# Gießelink, Pioneer and Clamshell ROM dump notes, LEWISCRC

`PIONEER.TXT` (2009) from the `CPROMUPL` package describes dumping a 17B,
17BII, 27S or 42S ROM over the infrared printer port into an HP 48 running
BINPRT (a binary-safe INPRT). `CLAMSHEL.TXT` does the same for the 28C/S;
`LEWISCRC.TXT` (2022, raw `raw/emulator-docs/emu42/LEWISCRC.TXT`) is the
readme of the ROM checksum tool. The package's tools and the LEWISCRC source
were not kept or read.

Facts (cite by file and section):

- PIONEER 2: a built-in memory scanner, entered with EXIT/ON + the fourth
  menu key, then ←; `^`/`v` move by #1000 nibbles (17BII/42S only), `*` `/`
  by #100, `+` `-` by 1, `.` executes the current address, 0-9 A-F enter a
  digit. EXIT/ON + the third menu key leaves.
- PIONEER 3: the 42S has ROM revisions A, B and C; `.` in the scanner shows
  the revision in the top left corner.
- PIONEER 5: the dump program, entered at #52000 (so RAM covers #52000),
  reads from address 0 with `A=DAT1 B` and sends each byte through the
  ROM's IR byte routine `OUTBYT` (#0318D on rev. A, #031D6 on rev. B and C).
  512 blocks of 128 bytes: a 64 KB ROM.
- PIONEER 6: after the dump the calculator warm-starts by jumping to #00000.
- LEWISCRC: the Mid-Range and High-End Pioneer and Clamshell ROMs on the
  1LR3 Sacajawea and 1LR2 Lewis have a built-in CRC (the Low-End ones on the
  1LU7 Bert a checksum); 64 KB images: 17B, 17BII, 27S, 42S; packed or
  unpacked images are accepted.

Facts used on [[hardware/hp42s]] and [[hardware/lewis]].
