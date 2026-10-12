---
title: "HP 27S"
type: model
models: [27s]
status: draft
sources:
  - "[[sources/hp27s-om]]"
  - "[[sources/commons-pioneer-photos]]"
  - "[[sources/kml20]]"
  - "[[sources/emu42-pioneer-dump]]"
tags: [model, pioneer, lewis, keyboard]
---

# HP 27S

Pioneer-series scientific calculator (1988) with algebraic entry, a
Solver, statistics, TVM, base conversions, and a clock with appointments.
One [[hardware/lewis]] chip on the [[hardware/hp42s]] board, the same
keyboard matrix as the [[hardware/hp17bii]]. No RPN mode.

Page numbers below are the manual's own book pages, except "p. 2", the
keyboard figure, which is the PDF page.

## Summary

| Item | 27S |
| --- | --- |
| Chip | Lewis ([[hardware/saturn-models]]) |
| ROM | 64 KB, revisions A, B ([[sources/emu42-pioneer-dump]] PIONEER) |
| RAM | 8 KB (as 42S; [[hardware/saturn-models]]) |
| Display | 131 x 16 dots: calculator line and the previous result, or a menu line (src: [[sources/hp27s-om]] p. 2, 22) |
| Contrast | 0-31, reset 22, keyboard 15-31 ([[sources/kml20]] LCD contrast table); hold CLR and press + or − ([[sources/hp27s-om]] p. 15) |
| Entry logic | algebraic; INPUT can stand in for = ([[sources/hp27s-om]] p. 21) |
| Clock | quartz, within three minutes a month ([[sources/hp27s-om]] p. 196) |
| Power | three button cells, ten-minute auto-off ([[sources/hp27s-om]] p. 15) |
| Emu42 model | `Model "M"` ([[sources/kml20]] Global) |

## Keyboard matrix

(src: [[sources/kml20]] OutIn Codes HP27S.) OUT bit, IN value:

| IN \ OUT bit | 5 | 4 | 3 | 2 | 1 | 0 |
| --- | --- | --- | --- | --- | --- | --- |
| 64 | menu 1 | menu 2 | menu 3 | menu 4 | menu 5 | menu 6 |
| 32 | STO | RCL | SIN | COS | TAN | EXIT |
| 16 | - | INPUT | +/- | ( | ) | ← |
| 8 | ▲ | - | 7 | 8 | 9 | ÷ |
| 4 | ▼ | - | 4 | 5 | 6 | × |
| 2 | shift | - | 1 | 2 | 3 | − |
| 1 | - | - | 0 | . | = | + |

CLR is "0 32768", IN bit 15 (the ON line).

## Key labels

Left to right, from the manual's colour keyboard drawing, matched against
photograph P3 (src: [[sources/hp27s-om]] p. 2;
[[sources/commons-pioneer-photos]] P3). Every key except ▲, ▼, ← and the
shift key has a blue label above it:

| Row | Key | Shifted (blue, above) | Lower |
| --- | --- | --- | --- |
| 1 | menu 1 (printed ^) | √x | - |
| 1 | menu 2 | x² | - |
| 1 | menu 3 | e^x | - |
| 1 | menu 4 | LN | - |
| 1 | menu 5 | y^x | - |
| 1 | menu 6 | 1/x | - |
| 2 | STO | 10^x | - |
| 2 | RCL | LOG | - |
| 2 | SIN | ASIN | - |
| 2 | COS | ACOS | - |
| 2 | TAN | ATAN | - |
| 2 | EXIT | MAIN | - |
| 3 | INPUT (two keys wide) | CLEAR DATA | - |
| 3 | +/- | E | - |
| 3 | ( | % | - |
| 3 | ) | π | - |
| 3 | ← | - | - |
| 4 | ▲ | - | - |
| 4 | 7 | SOLVE | - |
| 4 | 8 | STAT | - |
| 4 | 9 | TVM | - |
| 4 | ÷ | TIME | - |
| 5 | ▼ | - | - |
| 5 | 4 | BASE | - |
| 5 | 5 | PROB | - |
| 5 | 6 | HYP | - |
| 5 | × | CONVERT | - |
| 6 | shift (blank blue key) | - | - |
| 6 | 1 | %CHG | - |
| 6 | 2 | PARTS | - |
| 6 | 3 | MODES | - |
| 6 | − | PRINTER | - |
| 7 | CLR | OFF | ON |
| 7 | 0 | MEM | - |
| 7 | . | SHOW | - |
| 7 | = | LAST | - |
| 7 | + | PRT | - |

- The menu keys' shifted functions are fixed and also work unshifted
  when no menu labels are shown (src: [[sources/hp27s-om]] p. 22).
- The shifted labels over the digit block (rows 4-6) name menus:
  applications (SOLVE, STAT, TVM, TIME) and numeric or control menus
  (BASE, PROB, HYP, CONVERT, %CHG, PARTS, MODES, PRINTER); on the case
  they sit on small grey bars (p. 2, 22; P3).

## Colours

Approximate, sampled from photograph P3
([[sources/commons-pioneer-photos]]):

| Part | 27S |
| --- | --- |
| Case body | #1c1a18 black |
| Keyboard faceplate | #292623 dark grey |
| Display surround | dark grey band #4f4944; "27S" and "SCIENTIFIC" in white |
| LCD window frame | brushed silver, #cbc1b0 |
| LCD background | #66654d (lit, in shadow) |
| Ordinary keys, INPUT, digits | all the same, near-black, about #151413 |
| Key label text | off-white #cfd1c1 |
| Shifted labels | light blue #82acc4 |
| Grey bars behind the digit-block shifted labels | #48443f |
| Shift key | blank, blue #5e94bc |
| Lower label (ON) | off-white |

The manual: "Most keys have a second function printed in blue above the
key. The blue shift key accesses these operations." (src:
[[sources/hp27s-om]] p. 21).

## Display and annunciators

Five annunciators per the manual: shift (bent arrow), printing (printer
symbol), appointment past due (((•))), low battery, and RAD for radians
(src: [[sources/hp27s-om]] p. 20). No GRAD mode: the angle modes are
degrees and radians only (p. 53-54). Photograph P3 shows ▲▼ lit at the
end of the self-test, a sixth segment the manual does not name.

Mapping, inferred from the 42S's positions ([[hardware/lewis]]):
▲▼ at #40218 (undocumented, lit in the self-test), shift #40220, print #40228,
appointment ((•)) #40230 (the 42S busy symbol), battery #40238, unused
at #40240 (G on the 42S), RAD #40248. See
[[questions/pioneer-annunciator-mapping]].

## Typing letters

ALPHA menu on the six menu keys, as on the 17BII: two presses per
letter, groups ABCDE, FGHI, JKLM, NOPQ, RSTUV, WXYZ, each with OTHER
(seven pages of further characters); ALPHA-edit has DEL and cursor moves
(src: [[sources/hp27s-om]] p. 36-39). BASE's HEX menu types the digits A-F
(p. 63).

## Key chords

(src: [[sources/hp27s-om]] p. 15, 195-199)

| Chord | Matrix | Effect |
| --- | --- | --- |
| hold CLR, press + or − | ON + OUT 0/IN 1, ON + OUT 0/IN 2 | contrast |
| CLR + third menu key | ON + OUT 3/IN 64 | "MACHINE RESET"; may need repeating; memory kept |
| CLR + first + sixth menu key | ON + OUT 5/IN 64 + OUT 0/IN 64 | erase continuous memory: "MEMORY LOST" on release |
| CLR + fifth menu key | ON + OUT 1/IN 64 | continuous self-test |
| CLR + fourth menu key | ON + OUT 2/IN 64 | a factory self-test; leave with CLR + third menu key |

Erasing memory clears the calculator line and history, equations,
variables, STAT lists and appointments; sets month/day/year, 12-hour
clock, 4 decimals, period as decimal point, degrees, printer options off,
beeper on; reseeds RAN# from the clock; keeps time and date (p. 196).

### Self-test

Turn on, go to MAIN (shift, EXIT), hold CLR and press the fifth menu key.
The test beeps, shows patterns and characters and prints to an IR printer
if one is on; at the end of each cycle it shows "OK-27S-E" or FAIL with a
five-digit number, then repeats. CLR + third menu key stops it with
"MACHINE RESET"; another key stops it with a meaningless FAIL (p.
198-199). Photograph P3 shows "OK-27S-E" on the upper line with ▲▼ lit.

## Arithmetic

2 + 3: `2 + 3 =` (or `2 + 3 INPUT`) (src: [[sources/hp27s-om]] p. 21).

## Clock and appointments

As on the 17BII ([[hardware/hp17bii]] Clock and alarms), reached by
shift, ÷ (TIME): SET takes the date (MM.DDYYYY) and time (HH.MMSS), A/PM,
12/24 and M/D switch formats, ADJST nudges by hours, minutes or seconds;
ten appointments with message (22 characters) and repeat interval; the
((•)) annunciator and beeping when due, even when off (src:
[[sources/hp27s-om]] p. 138-143). Clock hardware:
[[questions/pioneer-clock-alarm-hardware]].

## ROM images seen

@ractive's image, run in saturnus (iteration 42): 65536 bytes, SHA-256
df64a595e8a86e557eb48609bbbc3b67f9f4fd646f3319861aaa26623510e380, in dump
order ([[hardware/saturn-models]] "ROM image forms"). Its last 32 bytes
hold "COPYRIGHT HP 1987" and "OK-27S-EB", the self-test pass text with
revision B.

- A first start shows "MEMORY LOST"; `2 + 3 =` shows 5.0000.
- The self-test runs SPD, BEEP, DISP, ROM, DRAM, URAM and a summary; the
  ROM step prints "ROM 05DFB" and fails with saturnus's CRC rule
  ([[questions/hp42s-rom-crc]]).
