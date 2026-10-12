---
title: "Gießelink, Emu28 Manual and ROM dump notes"
type: source
authors: ["Christoph Gießelink"]
year: 2025
raw: "https://hp.giesselink.com/Emu28/Emu28.htm"
status: skimmed
tags: [emu28, clamshell, rom, 28c, 18c]
---

# Gießelink, Emu28 Manual and ROM dump notes

The manual of Emu28 (v1.40), the emulator of the HP 18C and HP 28C, with
the emulator's web page (<https://hp.giesselink.com/emu28.htm>) and its
`ROMDMP.TXT` (<https://hp.giesselink.com/Emu28/ROMDMP.TXT>). Web pages,
not in `raw/`; read on 2026-10-12 for the chips and the ROM situation.
Documentation shipped with an emulator: a fact source under the
clean-room rule; the GPL source is not.

- 1 General: the 18C and 28C are built from the 1LK7 Saturn CPU, the
  1LP2 "Centipede" and 1LP4 ROM chips. The author has not tested the 18C,
  having no ROM image of it.
- 3 ROM Images: HP's ROMs are copyrighted and the author has no licence to
  distribute them; images are packed or unpacked as on Emu42; a checksum
  tool (CHAMPCHK) validates them.
- 8.6.3 Sound: as on Emu42, the firmware measures its CPU strobe frequency
  into `=CSPEED` at a cold or warm start.
- 8.6.4: printing goes to an HP 82240A/B printer simulation (the 28C's IR
  printer port).
- Web page: an entry point list exists for the 28C revision 1CC ROM.
- `ROMDMP.TXT`: dumping a 28C ROM needs no opening of the case: a
  peek program typed in as machine code (entered through internal
  `SYSEVAL` entries, so a typo loses memory) sends ROM content as IR
  printer data to an HP 48 running INPRT, about 2 KB per session because
  INPRT rejects NUL bytes and the 48's memory is small. The faster
  binary-safe route is in [[sources/emu42-pioneer-dump]] (`CLAMSHEL.TXT`).

Used on [[hardware/saturn-models]].
