---
title: "HP 49G FAQ 1.10 (Rechlin)"
type: source
authors: ["Eric Rechlin"]
year: 2000
raw: "https://www.hpcalc.org/hp49/docs/faq/"
status: skimmed
tags: [flash, rom]
models: [49g]
---

# HP 49G FAQ 1.10 (Rechlin)

The comp.sys.hp48 community FAQ for the 49G, version 1.10 of 23 December
2000, maintained by Eric Rechlin on hpcalc.org. Web page, not in `raw/`;
read on 2026-10-12 at <https://www.hpcalc.org/hp49/docs/faq/>. Only the
sections on flash, the boot ROM, ROM versions, diagnostics and hidden
keys (2.6, 4.10, 4.14, 6.1; read 2026-10-25) were read.
Cite by FAQ section.

- 1.6: flash can be read like ROM and also written; of the 2 MB, 1 MB holds
  the upgradable ROM and 1 MB is for the user. HP quotes about a million
  erase cycles.
- 1.7: a 64 KB part that cannot be erased (the FAQ calls it non-flash ROM;
  HP's name is "boot sector") holds software to download a new system from
  a PC if the flash is wiped, so a 49G with erased flash does not have to go
  back to HP.
- 1.12: the flash is 16 banks of 128 KB: 7 for the system, 8 for the user,
  1 for the boot sector. The first 256 bytes of each bank are reserved for
  identification data, 9 x 256 = 2304 bytes in all for the user banks.
- 4.11: `VERSION` shows the system ROM version, `VER` the CAS version; there
  is no command for the boot sector's version, and "all known production
  49's have version 1.9".
- 4.10: in RPN mode the plot windows (Y=, WIN, GRAPH, ...) need the left
  shift held down while the function key is pressed, because a tapped
  shift before a function key stores into variables or converts units;
  in algebraic mode a tapped shift works.
- 4.14: a list of undocumented held-shift combinations (← NXT last menu,
  ← TOOL real/complex, ← VAR HOME, → ENTER exact/approximate, → EQW back
  quotes, → ▶ Kermit and XModem server, → 9 time tools, → ▼ menu labels)
  and of unshifted shortcuts (ENTER DUP, ⬅ DROP, ▶ SWAP, ▲ HIST). Used on
  [[hardware/hp49g-keyboard]].
- 5.8: ON-D starts the diagnostics; the number keys run tests including
  keyboard, ROM, RAM and flash formatting, and Y^x reboots.
- 6.1: holding ⬅ during a warm start skips the libraries' configuration.

## Contradictions

- Boot version: the FAQ says production units have boot version 1.9; the
  boot sector in hpcalc.org's ROM 2.10 and 1.19-6 images reads "Boot
  Version 1.A" ([[hardware/hp49g]] "Flash chip and ROM images"). Both can
  be true (a later boot sector in the images than in the FAQ's units);
  unverified.
- Erasable or not: the FAQ calls the boot part "non-flash ROM"; Giesselink
  says it is the first half of flash bank 0, write-protected in hardware
  (src: [[sources/saturn-tutorial]] p. 161). The tutorial is the more
  precise source; for software the effect is the same.

Facts used on [[questions/custom-rom-for-saturn-calculators]].
