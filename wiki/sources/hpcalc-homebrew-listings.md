---
title: "hpcalc.org listings: homebrew systems, games and Saturn tools"
type: source
authors: ["Eric Rechlin"]
year: 2026
raw: "https://www.hpcalc.org/hp48/pc/programming/"
status: digested
tags: [rom, homebrew, toolchain]
models: [48sx, 48gx, 49g]
---

# hpcalc.org listings: homebrew systems, games and Saturn tools

Web pages of the HP Calculator Archive, not in `raw/`, read on
2026-10-12. Nothing but the ShellOS manual ([[sources/shellos-manual]])
was downloaded. Each fact cites the page it comes from.

| Page | URL |
| --- | --- |
| PC programming tools for the 48 | <https://www.hpcalc.org/hp48/pc/programming/> |
| 48 arcade games | <https://www.hpcalc.org/hp48/games/arcade/> |
| Doom Preview II | <https://www.hpcalc.org/details/486> |
| Doom Alpha for the GX | <https://www.hpcalc.org/details/487> |
| ShellOS section and details | <https://www.hpcalc.org/hp48/apps/shell/>, <https://www.hpcalc.org/details/79> |
| Meta Kernel review | <https://www.hpcalc.org/hp48/apps/mk/mk.php> |
| Meta Kernel 2.30 | <https://www.hpcalc.org/details/213> |
| HP48XGCC 1.0.1 for Unix | <https://www.hpcalc.org/details/3794> |
| 49/50 ROM updates | <https://www.hpcalc.org/hp49/pc/rom/> |
| 49 beta ROM 1.19-6 (mirror) | <https://hpcalc.gaak.org/?id=3240> |

## Replacement systems

- **ShellOS 3.0 beta 2.4** (Stephane Cocquereaumont, Philippe Dedieu,
  1998-09-13): a Unix-like multitasking OS written entirely in assembly,
  incompatible with the RPL OS, needing a 32 KB or 128 KB RAM card in slot
  1; no source included, no licence stated (details 79). Applications
  written for it (Tetris, Diamonds, Boxxle, a debugger, a memory viewer)
  are listed in the same section.
- **Meta Kernel** (Maubert Development Group: Jean-Yves Avenard, Cyrille de
  Brébisson, Gérald Squelart and others): a 128 KB ROM card for slot 1 of a
  48GX that replaces most of the user interface (stack, editor, equation
  and matrix writers, filer, graphics editor) while staying compatible
  with the HP 48 (review, Eric Rechlin, July 1999). It was sold, about
  US$90 on card; the review reports that Avenard said it would one day be
  free. Version 2.30 (2000-06-18) is on the archive as a kernel image plus
  libraries, no source, no licence stated (details 213). It replaces the
  shell, not the RPL kernel or the boot code.

## Games that drive the hardware

- **Doom Preview II** (Clément Pillias, "HPfool"; file date 1998-10-25): a
  greyscale Doom-style game with two monsters, four weapons and one large
  level; compiled, no source (details 486).
- **Doom Alpha for the GX** (Cyrille de Brébisson, "HpMad", and Paul
  Lancaster; added 1997-08-21): partly greyscale, two players over the
  link (one player can only walk the level), plus a smaller monochrome
  version; no source (details 487).
- Other first-person maze games listed: Lab3D (Rodrigo Campanini Rubio)
  and Rescue 3D 1.1 (CEFT Software) (arcade page).
- None of these pages gives a frame rate or says how the program takes
  the machine over.

## Toolchains (48 PC programming page)

- **HP Tools 3.0.8** (Jean-Yves Avenard, HP): System RPL and assembly kit
  under the GNU GPL, Win32, Linux, FreeBSD, source available. **GNU HP
  Tools 2.1.9** (HP, Mario Mikocevic): the same idea, DOS, Linux, Win32.
  **HP 48 Software Development Tools** (HP): RPL compiler, Saturn assembler
  and library builder (see [[sources/sasm-reference]],
  [[sources/hp48-sdk-makerom]]).
- **Areuh** (Pierre David, Janick Taillandier): development system for any
  Saturn calculator.
- Other assemblers: ASAP 1.01 (Andreas Gustafsson, Perl), STAR 1.04.4 (Jan
  Brittenson), the HP Saturn Assembler 2.3 (David L. Brown, Steve
  Haehnichen), CLASS, AMIGHP, ASMFIRE, HPAssembler Gx.
- C: **HP48XGCC** (Alex Ramos), a cross-compiler based on GNU C; the 1.0.1
  Unix package (archive date 1998-01-22) carries GCC 2.7.2 binaries,
  libraries (libgcc, libc, libm, libcore, libgraph) with their assembly
  and C sources, GPL and LGPL texts, and a loader (details 3794). hp48cc
  (Sandro Sigala) and G48 (Michele Andreoli) translate C-like code to RPL,
  not to machine code.
- Disassemblers: SADHP (Mika Heiskanen, Jan Brittenson, Paul Onions),
  Voyager 1.07 (Derek S. Nickel, source included), Decode, Dis48.

## 49G ROM updates

- Official 49G ROMs 1.05, 1.10, 1.16, 1.18 and the unsupported beta 1.19-6
  (details 3236-3240). The update instructions are for Windows 95-2000
  with the HP Graphing Calculator PC Connectivity Kit 3.0 (49/50 ROM
  page).
- Procedure: in the kit, pick the `.flash` file; on the 49G hold + and
  ENTER, press and release ON to get the "No System" menu, press 4 for
  terminal mode, start the flash upgrade on the PC (it first sends a
  download program), then press 1 "Download System"; about 15 minutes
  (49/50 ROM page; the page also mentions ON-F4 for diagnostic mode).
- Recovery after a failed upgrade: paper clip in the reset hole, then the
  same + ENTER, ON route to the "No System" menu and terminal mode, and
  repeat (49/50 ROM page).
- Beta 1.19-6: "AT YOUR OWN RISK"; the normal procedure does not apply and
  the user is told to back up the 64 KB flash bank first and read the
  included instructions (details 3240, mirror). This is the ROM that moves
  the second half of bank 0 from user to system ([[hardware/hp49g]] table
  "Flash and RAM use by ROM version").

## Reliability

Archive listings written by the archive's maintainer from the uploaders'
descriptions. Names, dates and file contents are exact; descriptions are
the uploaders' claims.

Facts used on [[questions/custom-rom-for-saturn-calculators]].
