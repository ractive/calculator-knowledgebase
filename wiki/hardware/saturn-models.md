---
title: "Saturn-based models: the whole family"
type: hardware
models:
  - 48sx
  - 48gx
  - 49g
  - 38g
  - 39g
  - 40g
  - 42s
  - 17b
  - 17bii
  - 27s
  - 28s
  - 19bii
status: draft
sources:
  - "[[sources/wikipedia-saturn-models]]"
  - "[[sources/mastracci-saturn-guide]]"
  - "[[sources/emu42-manual]]"
  - "[[sources/emu42-pioneer-dump]]"
  - "[[sources/emu42-problems]]"
  - "[[sources/emu42-changes]]"
  - "[[sources/emu28-manual]]"
  - "[[sources/emu71-manual]]"
  - "[[sources/emu71-garnier]]"
  - "[[sources/hp71b-compendium]]"
  - "[[sources/hpmuseum-saturn-pages]]"
  - "[[sources/hpmuseum-forum]]"
  - "[[sources/pioneers-rom-dumping]]"
  - "[[sources/hp28s-procnotes]]"
  - "[[sources/garnier-hp42s-new-facts]]"
  - "[[sources/hp17b-om]]"
  - "[[sources/hp17bii-om]]"
  - "[[sources/hp27s-om]]"
tags: [saturn, model, pioneer, clamshell, lewis, 71b, rom]
---

# Saturn-based models: the whole family

Every HP product built on Saturn hardware, by chip, with what is
published about each and how its ROM can be had. The models this wiki
covers in depth have their own pages ([[hardware/hp48sx]],
[[hardware/hp48gx]], [[hardware/hp38g]], [[hardware/hp39g-40g]],
[[hardware/hp49g]], [[hardware/hp42s]]); this page is about the rest.
Chip numbers and companion chips also on [[hardware/saturn-cpu]].

## Not Saturn hardware

- The 49g+, 48gII, 50g, 39g+, 39gs and 40gs run an emulated Yorke on a
  Samsung ARM920T (S3C2410 family) (src: [[sources/wikipedia-saturn-models]]
  HP Saturn).
- Later models that kept an old name: the 17bII+ (Sunplus), the 10BII
  (Hitachi H8) and the 10bII+ (ARM) (src: [[sources/wikipedia-saturn-models]]
  HP-17B, HP-10B).
- Wikipedia also lists an "HP-44A" on the 1LF2; no other source found
  names it (unverified).

## Chips

| Chip | Part | Clock | On chip | Models | Source |
| --- | --- | --- | --- | --- | --- |
| Saturn | 1LF2 | - | CPU only | 71B (early) | [[sources/mastracci-saturn-guide]] 1.5; [[sources/wikipedia-saturn-models]] |
| Saturn | 1LK7 | 640 kHz | CPU only, more instructions than the 1LF2 | 71B (later), 18C, 28C | [[sources/mastracci-saturn-guide]] 1.5; [[sources/emu71-manual]] 1; [[sources/emu28-manual]] 1 |
| Bert | 1LU7 | 640 kHz | CPU, 10 KB ROM, 256 bytes RAM, LCD driver | 10B, 20S, 21S | [[sources/emu42-manual]] 1; [[sources/emu42-pioneer-dump]] LEWISCRC; [[sources/wikipedia-saturn-models]] |
| Sacajawea | 1LR3 | 640 kHz | CPU, 16 KB ROM, 512 bytes RAM, LCD driver | 14B, 22S, 32S, 32SII | same |
| Lewis | 1LR2 | about 1 MHz | CPU, LCD driver, timers, CRC, IR, memory interface; ROM and RAM outside | 17B, 17BII, 27S, 42S (one chip); 19B, 19BII, 28S (two) | [[hardware/lewis]] |
| Clarke, Yorke | 1LT8 core | 2 / 4 MHz | | 48S/SX; 48G/GX, 38G, 39G, 40G, 49G | [[hardware/saturn-cpu]] |

Companion chips of the first generation: on the 71B the 1LF3 display
driver, the 1LG7 16 KB ROM and the 1LG8 RAM (src: [[sources/emu71-manual]]
1); on the 18C and 28C the 1LP2 "Centipede" and the 1LP4 ROM (src:
[[sources/emu28-manual]] 1). What the Centipede does (display, RAM, or
both) is not stated (unverified).

Register names of the Bert and Sacajawea (offsets in their I/O blocks):
the Sacajawea has DTEST (0x302), CTRL (0x303), LPE (0x309) and RAMTST
(0x30B), close to the Lewis's names at the same offsets; the Bert has a
"System Test Nibble" at 0x02 and a "Mode Register" at 0x03 (src:
[[sources/emu42-problems]]). Their displays are character or segment
LCDs driven from segment tables, unlike the Lewis's dot columns (src:
[[sources/emu42-changes]], which names segment tables for both). The
Bert's ROM self-check is a checksum, the Sacajawea's and Lewis's a CRC
(src: [[sources/emu42-pioneer-dump]] LEWISCRC).

## Models

| Model | Year | Chip | ROM | RAM | Display | I/O | Source |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 71B | 1984 | 1LF2, later 1LK7, plus 1LF3, 1LG7, 1LG8 | 64 KB | 17.5 KB, ports | one line of 22 characters, 132 x 8 dots | four module ports, card reader, HP-IL module | [[sources/hpmuseum-saturn-pages]]; [[sources/emu71-manual]]; [[sources/hp71b-compendium]] |
| 18C | 1986 | 1LK7, 1LP2, 1LP4 | (128 KB, as 28C, unverified) | - | (as 28C, unverified) | IR printer | [[sources/emu28-manual]] |
| 28C | 1986 | 1LK7, 1LP2, 1LP4 | 128 KB | 2 KB | 137 x 32 dots, four lines | IR printer | [[sources/emu28-manual]]; [[sources/emu42-pioneer-dump]] CLAMSHEL; [[sources/wikipedia-saturn-models]] |
| 28S | 1988 | two Lewis | 128 KB | 32 KB | 137 x 32 dots | IR printer | [[hardware/hp28s]]; [[sources/emu42-pioneer-dump]] LEWISCRC; [[sources/hp28s-procnotes]]; [[sources/wikipedia-saturn-models]] |
| 19B, 19BII | 1988, 1990 | two Lewis | 128 KB (19BII) | 8 KB at #C4000 (19BII) | 137 x 32 dots (19BII) | IR printer; the 19BII a clock with six appointment alarms | [[hardware/hp19bii]]; [[sources/emu42-pioneer-dump]] LEWISCRC; [[sources/wikipedia-saturn-models]] |
| 17B, 17BII | 1988, 1990 | Lewis | 64 KB (English), 96 KB (international) | 8 KB | 131 x 16 dots, as 42S | IR printer, clock with ten appointment alarms | [[hardware/hp17bii]]; [[sources/emu42-pioneer-dump]]; [[sources/wikipedia-saturn-models]] |
| 27S | 1988 | Lewis | 64 KB | 8 KB | 131 x 16 dots | IR printer, clock with ten appointment alarms | [[hardware/hp27s]]; same |
| 42S | 1988 | Lewis | 64 KB | 8 KB | 131 x 16 dots | IR printer | [[hardware/hp42s]] |
| 14B, 22S, 32S, 32SII | 1988-1991 | Sacajawea | 16 KB on chip | 512 bytes on chip | twelve 5 x 7 cells (32S, 32SII) | none | [[sources/emu42-pioneer-dump]] LEWISCRC; [[sources/wikipedia-saturn-models]] |
| 10B, 20S, 21S | 1988-1989 | Bert | 10 KB on chip | 256 bytes on chip | seven-segment, 12 digits (10B) | none | same |

Notes:

- The 28S's two Lewis chips are master and slave; on the slave some
  power bits always read 0 (src: [[sources/emu42-changes]]). The 28S puts
  RAM at #C0000 and the display at #FF840 and #FFC00 (src:
  [[sources/hp28s-procnotes]]), unlike the 42S, so the Lewis windows are
  configurable ([[questions/lewis-memory-map]]).
- The 17BII's memory editor shows the 42S layout: ROM at #00000, display
  at #40000, 8 KB RAM at #50000 (src: [[sources/pioneers-rom-dumping]]).
  The international 17B/17BII's extra 32 KB fits the second-ROM hook
  at #20000 seen on the 42S (src: [[sources/garnier-hp42s-new-facts]] 3.3;
  the fit is inferred); Emu42 v1.24 prepares the larger Pioneers with such
  a 32 KB extra ROM (src: [[sources/emu42-changes]] v1.24). The image
  form is open: [[questions/hp17b-international-rom-image]].
- The 71B's memory is a daisy chain of soft-configured chips that answer
  an ID (type, size, last in chain) and get their base address from the
  OS, with the system ROM hard configured (src: [[sources/emu71-manual]]
  9.1): the scheme the 48's controllers inherited
  ([[hardware/memory-controller]]).
- HP-IL on the 71B goes through the 82401A module, which has its own I/O
  processor; Emu71 simulates it rather than emulating its chip (src:
  [[sources/emu71-manual]] 1, 13).

## Public documentation

- 71B: the best documented Saturn machine. HP sold Internal Design
  Specifications: the design (Vol. I), entry points (Vol. II), the full
  operating-system source (Vol. III), a Hardware Design Specification and
  an HP-IL IDS (src: [[sources/hp71b-compendium]];
  [[sources/hpmuseum-saturn-pages]]). Not yet read for this wiki.
- 28S: Gariepy's notes ([[sources/hp28s-procnotes]]) and what applies
  from the 42S's Lewis ([[hardware/lewis]]).
- 17B, 17BII, 27S: the Lewis as on the 42S; Garnier and Gießelink's
  notes ([[sources/garnier-hp42s-new-facts]], [[sources/emu42-pioneer-dump]]).
  The owner's manuals ([[sources/hp17b-om]], [[sources/hp17bii-om]],
  [[sources/hp27s-om]]) give the keyboards, annunciators, the key chords
  (CLR with menu keys: reset, memory erase, self-test, on the 42S's matrix
  positions) and the self-test pass messages; collected on
  [[hardware/hp17bii]] and [[hardware/hp27s]]. KML 2.0 gives their key
  matrix (the 42S's positions) and contrast (0-31, reset 22, keyboard
  15-31) ([[sources/kml20]]).
- 18C, 28C: the Emu28 manual only names the chips; no register-level
  public description found.
- Bert, Sacajawea: register names in the Emu42 notes, nothing else found.
  Gießelink refers to HP technical documents (a pin-selected debug mode)
  that are not public (src: [[sources/hpmuseum-forum]] F3).

## ROM availability

Classes: (a) distributed with HP's permission; (b) circulating without
stated permission; (c) only by dumping one's own calculator; (d) unknown.
No download locations are recorded in this wiki.

| Model | Class | Dump method | HP statement |
| --- | --- | --- | --- |
| 71B | b, c | a BASIC `PEEK$` loop sends the ROM to an HP-IL printer device, captured on a PC through an HP-IL interface (PIL-Box) ([[sources/emu71-manual]] 3) | none; Garnier offers images arguing HP published the source listings ([[sources/emu71-garnier]]); a compendium shares them for personal use ([[sources/hp71b-compendium]]) |
| 28C | b, c | machine code typed in, IR printer output to an HP 48 with a binary-safe INPRT ([[sources/emu28-manual]], [[sources/emu42-pioneer-dump]] CLAMSHEL) | none |
| 28S | b, c | as 28C (CLAMSHEL); needs a 48 with about 133 KB free, or in pieces | none |
| 17B, 17BII, 27S, 42S | b, c | memory scanner plus IR, to an HP 48 ([[sources/emu42-pioneer-dump]] PIONEER; [[sources/pioneers-rom-dumping]]) | 42S: in 2004 HP was said to be "looking into" releasing it ([[sources/hpmuseum-forum]] F4); nothing followed |
| 19BII | b | none without decapping: no memory scanner ([[sources/hpmuseum-forum]] F3) | images reached Gießelink from people at HP for an emulator project (F3) |
| 19B | d | none known | none |
| 18C | d | the bus is external, so a logic-analyser capture during the self-test could work (theory, F3) | none; Emu28's author has no image |
| 14B, 22S, 32S, 32SII, 10B, 20S, 21S | b | no IR port: chip decapping or an undocumented debug mode (F3); some images came from HP to Gießelink (F3) | none |

The only permission statement found, "HP graciously began allowing this
to be downloaded in mid-2000" on hpcalc.org, is on the 48 ROM pages; it
names none of these models ([[sources/hpcalc-rom-listings]]).

## ROM image forms

Pioneer images (42S, 27S, 17B, 17BII) come in two byte orders (observed
in saturnus on 42S, 27S, 17B and 17BII images, iteration 42):

- **Address order**: byte i holds the nibbles at #2i (the low nibble)
  and at #2i+1. The CPU starts at #00000 with code.
- **Dump order**: the image cut into 256 equal units (256 bytes of a
  64 KB image, 384 bytes of a 96 KB one), stored last unit first. The
  file then starts with zeros and, near its start, the text
  "COPYRIGHT HP 198x".

Two 42S images of revision C differ exactly by this reordering. Each ROM,
the 64 KB main ROM and the 32 KB language ROM of an international image,
ends with "COPYRIGHT HP 198x" and its self-test pass text in its last 32
bytes, so the text's place tells the forms apart: in the last unit in
address order, in the first unit in dump order. A 96 KB international
image in address order is the 64 KB main ROM (#00000-#1FFFF) followed by
the 32 KB language ROM (#20000-#2FFFF); the main ROM checks a mark at
#20000 and, when it is there, asks for a language at the first start
(see [[hardware/hp17bii]]).

The Lewis CRC ([[hardware/lewis]] "CRC") tells the forms apart without
the text: each 64 KB Lewis ROM gives #FFFF over #00000-#1FFFF only in
address order. The 128 KB 19BII and 28S images are in dump order
too (256 units of 512 bytes, last first); the 28S image has no
"COPYRIGHT" text, so for it the CRC is the only test of the two
([[questions/hp42s-rom-crc]]).
