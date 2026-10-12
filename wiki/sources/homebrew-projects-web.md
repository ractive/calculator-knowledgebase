---
title: "Project pages: newRPL, HPGCC, Areuh, KASM, HP48XGCC, the 49/50 article"
type: source
authors: ["various"]
year: 2026
raw: "https://newrpl.wiki.hpgcc3.org/doku.php?id=manual%3Achapter1%3Ainstallation"
status: digested
tags: [rom, homebrew, toolchain]
models: [48sx, 48gx, 49g]
---

# Project pages: newRPL, HPGCC, Areuh, KASM, HP48XGCC, the 49/50 article

Project home pages and READMEs, not in `raw/`, read on 2026-10-12 (READMEs
and descriptions only; no source code was opened). Each fact cites its
page.

| Page | URL |
| --- | --- |
| newRPL manual, installation | <https://newrpl.wiki.hpgcc3.org/doku.php?id=manual%3Achapter1%3Ainstallation> |
| newRPL project page | <https://hpgcc3.org/projects/newrpl> |
| Areuh README | <https://github.com/hp71b/areuh> |
| KASM | <https://sourceforge.net/projects/k-asm/> |
| HP48XGCC | <https://sourceforge.net/projects/hp48xgcc/> |
| HP 49/50 series (Wikipedia) | <https://en.wikipedia.org/wiki/HP_49/50_series> |
| sunhpgames README | <https://github.com/brizzly/sunhpgames> |

## newRPL

- newRPL installs on the HP 50g, 39g+, 39gs, 40gs, 48gII and Prime G1, all
  ARM machines; the installation chapter names no Saturn model (newRPL
  manual). It goes on through the calculator's own boot loader (SD card
  with `update.scp`, or USB with the connectivity kit; boot mode by
  holding + and - while pressing the reset hole), and going back means
  flashing HP's ROM file the same way (newRPL manual).
- The project page describes newRPL as a re-implementation of the 48/49/50
  programming environment running natively on the hardware, and lists the
  50g with its ARM920T (project page). Licence: BSD (as the project
  states; the licence page itself was not read).

## HPGCC and the ARM models

- The 49g+ and later run the Saturn system on an emulation layer on an
  ARM920T; the original 49G has a 4 MHz Yorke (Saturn core) and was HP's
  first calculator with flash and upgradable firmware (Wikipedia).
- HPGCC compiles C for the ARM-based models on a PC (Wikipedia); it does
  not target the Saturn.
- Up to at least ROM 2.10, the 49g+/50g emulator builds could be installed
  on the original 49G, which then reports itself as an HP 48 in the
  version menu; back-ports HP48-C 2.09 (2006-06-03) and 2.10 (2007) are
  named (Wikipedia, no primary source checked). This fits the saturnus
  observation that the 2.15 "emulator" image boots as a 49G
  ([[hardware/hp49g]] "Bring-up findings").

## Saturn assemblers and compilers

- **Areuh** (Pierre David, Janick Taillandier; 1990, modernised 2022):
  assembler, linker and utilities for the Saturn, written in C, builds on
  Linux, BSD and macOS, CeCILL-B licence (BSD-like). Made for the HP-71;
  the README says some opcodes of the newer machines (28, 48) are missing
  from its table and could be added (Areuh README).
- **KASM** (SourceForge user ekalin): Saturn assembler in C++ with MASD syntax,
  GPL-2, last release 2013, beta; a 2017 user review reports build errors
  with modern compilers (SourceForge page).
- **HP48XGCC** (Alex Ramos): GCC-based C cross-compiler for the 48, started
  1994, GPL-2, last update 2013, beta (SourceForge page).

## Games

- sunhpgames: Saturn assembly games for the 48 (and some for the 49/50)
  written 1995-1998, among them ZeldaHP, SuperMarioHP, Tron48 and F-ZeroHP,
  advertised with four grey levels and fast scrolling; the README gives no
  technique, licence or frame rate (sunhpgames README).

## Reliability

Projects' own pages are good for scope, targets and licence. The
Wikipedia statements on ROM back-ports were not checked against a primary
source.

Facts used on [[questions/custom-rom-for-saturn-calculators]].
