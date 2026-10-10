---
title: "HP 42S"
type: model
models: [42s]
status: draft
sources:
  - "[[sources/garnier-hp42s-new-facts]]"
  - "[[sources/hosoda-hp42s]]"
  - "[[sources/emu42-manual]]"
  - "[[sources/emu42-pioneer-dump]]"
  - "[[sources/kml20]]"
tags: [model, pioneer, lewis]
---

# HP 42S

RPN scientific calculator of the Pioneer series (1988), the HP-41's
successor in keystroke programming. One [[hardware/lewis]] chip runs it.
HP never published its ROM; images come from dumping one's own calculator
(src: [[sources/emu42-manual]] 3, [[sources/emu42-pioneer-dump]]).

## Summary

| Item | 42S |
| --- | --- |
| Chip | Lewis 1LR2: CPU, display driver, timers, CRC, IR, memory interface (src: [[sources/emu42-manual]] 1; [[sources/hosoda-hp42s]]) |
| Clock | 32.768 kHz crystal (src: [[sources/hosoda-hp42s]] note 1); CPU about 1 MHz (unverified), set through RATE ([[questions/lewis-clock-and-rate]]) |
| ROM | 64 KB at #00000, revisions A, B, C (src: [[sources/emu42-pioneer-dump]] 3, 5) |
| RAM | 8 KB SRAM (S-MOS SRM2264) at #50000; 32 KB fits after a jumper change (src: [[sources/hosoda-hp42s]]); Emu42 has a "32 KB" class (src: [[sources/kml20]] Global) |
| Display | 131 x 16 dots, two lines, seven annunciators (src: [[sources/garnier-hp42s-new-facts]] 2.1-2.2; [[sources/kml20]]) |
| Contrast | 0-31, reset 22, keyboard range 15-31 (src: [[sources/kml20]] LCD) |
| Keyboard | 37 keys on 6 OUT x 7 IN lines plus EXIT on IN bit 15 (src: [[sources/kml20]] OutIn Codes HP42S) |
| I/O | IR LED to the HP 82240 printer only; no serial port, no card port (src: [[sources/emu42-manual]] 8.6.4) |
| Emu42 model | `Model "D"`, `Class 32` for 32 KB RAM (src: [[sources/kml20]] Global) |

## Memory map

ROM #00000-#1FFFF, display and registers #40000-#403FF, RAM from #50000,
details and evidence on [[hardware/lewis]]. With 8 KB the RAM mirrors through
#5FFFF (inferred from the ROM, see there).

## Keyboard

KML OutIn codes (OUT bit, IN value) (src: [[sources/kml20]] OutIn Codes
HP42S):

| IN \ OUT bit | 5 | 4 | 3 | 2 | 1 | 0 |
| --- | --- | --- | --- | --- | --- | --- |
| 64 | Σ+ | 1/x | √x | LOG | LN | XEQ |
| 32 | STO | RCL | R↓ | SIN | COS | TAN |
| 16 | - | ENTER | x≷y | +/- | E | ← |
| 8 | ▲ | - | 7 | 8 | 9 | ÷ |
| 4 | ▼ | - | 4 | 5 | 6 | × |
| 2 | shift | - | 1 | 2 | 3 | − |
| 1 | - | - | 0 | . | R/S | + |

EXIT is "0 32768": IN bit 15, the ON line. On the case the rows are Σ+ to
XEQ, STO to TAN, ENTER (two keys wide) x≷y +/- E ←, then ▲ 7 8 9 ÷, ▼ 4 5 6
×, shift 1 2 3 −, EXIT 0 . R/S +. The top row doubles as the six menu keys.

## Key chords (42S ROM in saturnus, revision C)

| Chord | Effect | Source |
| --- | --- | --- |
| EXIT + 1/x | "Memory Clear" (cold start) | 42S ROM |
| EXIT + √x | "Machine Reset"; also leaves the test mode | 42S ROM; [[sources/garnier-hp42s-new-facts]] 1 |
| EXIT + LOG | test mode; ← then enters the memory browser | [[sources/emu42-pioneer-dump]] 2; [[sources/garnier-hp42s-new-facts]] 1 |
| EXIT + LN | continuous self-test | [[sources/hosoda-hp42s]]; 42S ROM |
| EXIT + + + XEQ | deep sleep | [[sources/hosoda-hp42s]] note 15 |

The self-test steps (42S ROM): SPD (the measured speed, "SPD 08847" to
"SPD 08974" in saturnus at 1 MHz), BEEP, DISP (display patterns and the annunciators), ROM
(the hardware CRC over #0001C-#1FFFB, expected #FFFF; on failure it prints
the CRC), DRAM (display RAM), URAM (user RAM), an IR or further test that
is skipped when status flag 8 is set, then "OK-42S" or "FAIL" with a code
(strings at #1FFE6 and #1FEE6), then "COPYRIGHT HP 1988", and it starts over.
Real units print "ROM O.K.", "URAM O.K." (src: [[sources/hosoda-hp42s]]).

## @ractive's ROM image

Revision C, 65536 bytes packed, SHA-256
f4c5f9f0e1d89074b7ca49add99b3ea72ed7fae9370b421de20a0cd8384c08f3. It boots, shows "Memory Clear" on a cold start and computes (2 ENTER 3
+ gives 5), but the self-test's ROM step computes #1BE8 instead of #FFFF and
the summary reads FAIL: [[questions/hp42s-rom-crc]].

## Cold start

A cold start with blank RAM shows "Memory Clear" on the upper line and
`x: 0.0000` on the lower, the stack ready; no key has to be answered (42S
ROM). Power-on contrast 22 (range 15-31), observed on ROM C after a cold
start (observed in saturnus 2026-10-07; saturnus renders it at about 90 % darkness).
