---
title: "Lewis (1LR2)"
type: hardware
models: [42s]
status: draft
sources:
  - "[[sources/garnier-hp42s-new-facts]]"
  - "[[sources/hosoda-hp42s]]"
  - "[[sources/emu42-manual]]"
  - "[[sources/emu42-problems]]"
  - "[[sources/emu42-changes]]"
  - "[[sources/emu42-pioneer-dump]]"
  - "[[sources/kml20]]"
  - "[[sources/hp28s-procnotes]]"
  - "[[sources/mastracci-saturn-guide]]"
tags: [lewis, chip, 42s, pioneer]
---

# Lewis (1LR2)

The HP chip of the High-End Pioneer (17B, 17BII, 27S, 42S) and Clamshell
(19BII, 28S) calculators: a Saturn CPU with the display driver, timers,
keyboard scanning, a CRC generator, IR output and the memory interface on
one die (src: [[sources/emu42-manual]] 1; [[sources/garnier-hp42s-new-facts]]
2; Emu42's KML `Hardware "Lewis"`, [[sources/kml20]] Global). Mastracci
lists the CPU core of the 17B/19B/27S/28S family as 1LT8 inside "Lewis"
(src: [[sources/mastracci-saturn-guide]] 1.5); Emu42 and Hosoda's schematic
name the chip 1LR2 (src: [[sources/emu42-manual]] 1, [[sources/hosoda-hp42s]]).
The 28S carries two Lewis chips, master and slave (src:
[[sources/emu42-changes]]). This page is about the chip as the 42S uses it;
the board is on [[hardware/hp42s]].

Facts marked "(42S ROM)" were found by running and tracing the 42S revision C
ROM in saturnus (iteration 15, 2026-10-05).

## Memory map (42S)

| Range | What | Source |
| --- | --- | --- |
| #00000-#1FFFF | 64 KB ROM | [[sources/emu42-pioneer-dump]] 5; [[sources/garnier-hp42s-new-facts]] 1 |
| #20000- | optional second ROM; XFCN calls #20005 if #20000 holds #5AC3F | [[sources/garnier-hp42s-new-facts]] 3.3 |
| #40000-#402FF | display RAM: columns, annunciator words, scratch | [[sources/garnier-hp42s-new-facts]] 2.1-2.2; (42S ROM) |
| #40300-#4030F | control registers | [[sources/garnier-hp42s-new-facts]] 2.3; [[sources/emu42-problems]] |
| #403F7-#403FF | timers | [[sources/garnier-hp42s-new-facts]] 2.4; (42S ROM) |
| #50000- | RAM, 8 KB stock (#50000-#53FFF) | [[sources/emu42-pioneer-dump]] 5; [[sources/hosoda-hp42s]] |

- The 42S ROM addresses #4025F and #40238 in its first instructions after
  reset and never executes CONFIG, UNCNFG or C=ID during boot, normal use or
  the self-test (42S ROM; the CONFIG opcodes a linear disassembly shows sit in
  data). Whatever window logic the chip has, the 42S runs on its reset map.
  The 28S's ROM puts RAM at #C0000 and the display at #FF840/#FFC00 (src:
  [[sources/hp28s-procnotes]]), and Emu42 says "Register and Display/Timer MMU
  configuration isn't separated" (src: [[sources/emu42-problems]]), so the
  windows are configurable; how is open: [[questions/lewis-memory-map]].
- RAM mirrors: with 8 KB the ROM's cold start only works when the RAM repeats
  through #50000-#5FFFF: with nothing above #53FFF its stack arithmetic later
  fails ("Invalid Type" on `2 ENTER 3 +`); with a mirror it sizes memory
  to #54000 (pointers near #53Exx in its RAM header) and works. Inferred: the
  ROM sizes RAM by looking for the alias (42S ROM).

## Display

131 x 16 pixels, two lines of 22 characters of 6 x 8 cells, plus seven
annunciators (src: [[sources/kml20]] Annunciator: seven on Emu42 Lewis).

- Column `c` is four consecutive nibbles, top to bottom, least significant
  bit the top pixel. Two column drivers interleave: column `c` < 66 at the
  address #40000 + 8c, column `c` >= 66 at #40004 + 8(c - 66), up to
  the nibble #4020B (src:
  [[sources/garnier-hp42s-new-facts]] 2.1; confirmed on the "Memory Clear"
  screen, 42S ROM). Seen per byte: byte 4k is the upper line's column k,
  byte 4k+1 the lower line's.
- The ROM clears #40000-#4023F as display (42S ROM, routine at #012C6) and
  its display-RAM self-test ("DRAM") writes a pattern through #4025F.
- #4025F holds a restart code the ROM writes at reset (the P register of
  its reset entry) and keeps across resets; #40250-#4025F are not cleared
  by the display tests (42S ROM).
- Annunciators: 5-nibble words, #FFFFF on and 0 off: #40218 ▲▼, #40220
  shift, #40228 print, #40230 busy ((.)), #40238 battery, #40240 G, #40248
  RAD; #40210 lights all (src: [[sources/garnier-hp42s-new-facts]] 2.2).
  The ROM confirms ▲▼ (lit with a two-row menu), shift, G and RAD (GRAD lights
  both), and battery (the low-battery routine at #00485 writes all ones or
  zeros to #40238) (42S ROM).
- DON is DSPCTL (#40303) bit 3: the ROM sets it at #01666 and clears it
  before turning off at #01682 (42S ROM; the DSPCTL name and the DON bit
  in it: [[sources/emu42-problems]], [[sources/emu42-changes]]).
- Contrast: 5 bits, #40301 bits 0-3 and DSPCTL bit 1 as bit 4. The ROM's
  reset value is #40301 = 6 with DSPCTL bit 1 set: 22 (42S ROM, code
  at #012E3 and #00295), which is the reset contrast KML 2.0 gives for the 42S, range 15-31
  by keyboard (src: [[sources/kml20]] LCD contrast table).

## Registers

| Address | Name | Use (42S) | Source |
| --- | --- | --- | --- |
| #40300 | RATE / "speed" | CPU speed; 7 normal and F fast in Hosoda's tests; the 42S ROM never writes it | [[sources/garnier-hp42s-new-facts]] 2.3; [[sources/hosoda-hp42s]]; [[sources/emu42-manual]] 8.6.1 |
| #40301 | contrast | bits 0-3 | [[sources/garnier-hp42s-new-facts]] 2.3; 42S ROM |
| #40302 | DSPTEST | VDIG LID CLTM1 CLTM0 | [[sources/emu42-problems]] |
| #40303 | DSPCTL | bit 1 contrast bit 4, bit 3 DON; SDAT, BIN | [[sources/emu42-problems]]; 42S ROM |
| #40304-#40307 | CRC | 16 bits, low nibble first | [[sources/garnier-hp42s-new-facts]] 2.3; 42S ROM |
| #40308 | LPD | GRAM, VLBI, LBI; the ROM samples bits 0 and 1 repeatedly (#004BE) | [[sources/emu42-changes]]; 42S ROM |
| #40309 | LPE | EVRAM, RST, EGRAM; the ROM writes 8 at #00511 and around SHUTDN at #00A59 | [[sources/emu42-problems]]; [[sources/emu42-changes]]; 42S ROM |
| #4030B | RAMTST | PLEV XTRA DDP DPC | [[sources/emu42-problems]] |
| #4030C | INPORT | RX SREQ ST1 ST0; the ROM writes 0 at reset, 4 then 0 at #012A4 | [[sources/emu42-problems]]; 42S ROM |
| #4030D | LEDOUT | IR LED: UREG EPD DRL STL | [[sources/emu42-problems]]; [[sources/emu42-changes]] |
| #4030E | TIMER1 control | bits 1 INT and 2 WAKE set and cleared as on the 48's #12E | 42S ROM (inferred role) |
| #4030F | TIMER2 control | bit 0 run (the ROM cold-starts if clear, #0114D), bit 1 INT, written #F | 42S ROM (inferred role) |
| #403F7 | TIMER1 | 4 bits, written 7 | 42S ROM; [[sources/garnier-hp42s-new-facts]] 2.4 |
| #403F8-#403FF | TIMER2 | 32 bits counting down at 8192 Hz; the ROM tests the top bit at #0109D | [[sources/garnier-hp42s-new-facts]] 2.4; 42S ROM |

The control nibbles and timers keep the 48's bit layout, moved: the
48's #12E/#12F/#137/#138 (see [[hardware/timers]]) are the Lewis's #30E/#30F/#3F7/#3F8
(inferred from how the ROM uses them: INT and WAKE in bits 1 and 2, run in
bit 0, the counter's sign bit as expiry). The register block's first nibbles
mirror the 48's #100-#109 too: contrast at offset 1, CRC at 4-7, power at
8-9 (inferred similarity; Garnier calls the layout "close to the HP-28S").

## CRC

Data reads outside the register block feed a 16-bit CRC at #40304. Using the
48's update rule (see [[hardware/crc]]), the 42S ROM's own self-test computes
the value the real self-test would compare: on @ractive's revision C image
it gets #1BE8 where the test wants #FFFF ([[questions/hp42s-rom-crc]]).

## Keyboard, beeper, IR

- Keyboard through OUT and IN like the 48 (src: [[sources/hp28s-procnotes]]
  "I/O Registers"); the 42S matrix is on [[hardware/hp42s]].
- Beeper: the self-test's BEEP step toggles OUT bits 10 and 11 in opposite
  phase (OUT #43F / #83F), a push-pull drive of the piezo (42S ROM). The 48
  uses OUT bit 11 alone ([[hardware/keyboard]]).
- IR: LEDOUT drives the printer LED; the ROM's byte routine is `OUTBYT`
  at #031D6 on revisions B and C (src: [[sources/emu42-pioneer-dump]] 5). Not
  traced further.

## Clock

A 32.768 kHz crystal (src: [[sources/hosoda-hp42s]] note 1), the 8192 Hz
timer (src: [[sources/garnier-hp42s-new-facts]] 2.4), CPU speed set by RATE
at #40300 (src: [[sources/emu42-manual]] 8.6.1, [[sources/hosoda-hp42s]]).
The CPU frequency is usually given as about 1 MHz (unverified, web search
summaries); the firmware measures it into `=CSPEED` and its self-test prints
it as "SPD" (src: [[sources/emu42-manual]] 8.6.3). See
[[questions/lewis-clock-and-rate]].

## Interrupts and sleep

The 42S ROM writes #4030E, does INTOFF and SHUTDN, and wakes on keys and the
timers (42S ROM, #00453-#0046D). Keyboard interrupts, the ON line (the 42S's
EXIT key on IN bit 15) and timer wake behave as on the 48 as far as the
ROM's boot, key entry and self-test show (42S ROM in saturnus).

## Contradictions

- Garnier marks #4030E "beep?"; the ROM uses it like a timer control nibble
  (bits 1 and 2 set and cleared around sleep) and beeps through OUT. Filed
  under [[questions/lewis-registers]].
- Chip number: 1LT8 (Mastracci, the CPU core) versus 1LR2 (Emu42, Hosoda,
  the chip). Not a real conflict if 1LT8 names the core.
