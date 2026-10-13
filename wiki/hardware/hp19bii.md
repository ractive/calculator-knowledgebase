---
title: "HP 19BII"
type: model
models: [19bii]
status: draft
sources:
  - "[[sources/hp19bii-om]]"
  - "[[sources/kml20]]"
  - "[[sources/emu42-pioneer-dump]]"
  - "[[sources/hpmuseum-forum]]"
  - "[[sources/commons-hp19bii-photos]]"
tags: [model, clamshell, lewis, keyboard, display, self-test, clock]
---

# HP 19BII

Clamshell financial calculator (1990), the "Business Consultant II", on
the board of the [[hardware/hp28s]]: two [[hardware/lewis]] chips,
master and slave, and the same fixed memory map
([[hardware/saturn-models]]). Unlike the 28S, the display sits on the
right half, above the number keys; the left half carries a printed
reference card above the letter keys (src: [[sources/hp19bii-om]]
p. 22-23). Emu42 runs it as `Model "Y"` on `Hardware "Lewis"` (src:
[[sources/kml20]] Global).

Page numbers are the manual's book pages ([[sources/hp19bii-om]]).
Facts marked "(19BII ROM)" were found by running @ractive's image in
saturnus (iteration 44, 2026-10-13), on a logging bus where a memory
range is named.

## Summary

| Item | 19BII |
| --- | --- |
| Chips | two Lewis, master and slave, as the 28S ([[hardware/saturn-models]]) |
| ROM | 128 KB at #00000-#3FFFF, two 64 KB halves whose last bytes read "OK-19B-1DK" (#00000-#1FFFF) and "OK-19B-2A" (#20000-#3FFFF) (19BII ROM) |
| RAM | used at #C4000-#C7FFF only: 8 KB (19BII ROM); "approximately 6,600" bytes free for the user (p. 37) |
| Display | 137 x 32 dots: three text lines over a line of six menu labels, six annunciators above (p. 21; 19BII ROM) |
| Contrast | 0-31, reset 22; the ROM keeps keyboard changes within 16-31 ([[sources/kml20]] LCD contrast table); ON held with + (darker) or − (lighter) (p. 20, 22) |
| Keyboard | 35 keys on the letter half, 37 on the display half including ON ([[sources/kml20]] OutIn Codes HP19BII; p. 22-23) |
| Clock | clock and calendar (October 15, 1582 to December 31, 9999), six appointments with alarms and messages (p. 175-181) |
| Printer | one-way infrared, the print annunciator while sending (p. 199) |
| Languages | six; a memory loss asks for one before anything else (p. 20; 19BII ROM) |
| Power | about ten minutes to auto-off (p. 20) |

## Memory map

Traced through cold boot, the language menu, arithmetic, menus, TEXT,
the clock and appointments, MODES and the self-test (19BII ROM):

| Range | What |
| --- | --- |
| #00000-#3FFFF | 128 KB ROM |
| #C4000-#C7FFF | RAM: every RAM write lands here |
| #FF800-#FFBFF | the slave Lewis's block, the 28S's layout: annunciator words from #FF800, display RAM, registers from #FFB00 |
| #FFC00-#FFFFF | the master Lewis's block, the 28S's layout |

- The same windows as the 28S's ([[hardware/hp28s]] "Memory map"):
  nothing is configured, and the display and register blocks sit where
  the 28S's do. The difference is the RAM: the 28S uses #C0000-#DFFFF
  (32 KB, seen twice), the 19BII only #C4000-#C7FFF.
- An object-walking routine (#02EA9-#02EB2) reads 5-nibble words at
  scattered addresses across the whole space, #40000-#FF7FF included,
  while it searches for an object; those reads are of data, not a memory
  probe, and nothing else touches the unmapped ranges.
- Whether the RAM chip also answers elsewhere in #C0000-#DFFFF is not
  known: the ROM never looks.

## Display

- 137 columns: the language menu's two columns of text run across the
  two chips' halves without a gap, and the six menu labels are inverse
  boxes 22 pixels wide, 23 apart, filling columns 0-136, as on the 28S
  (19BII ROM). The "132 x 32" in [[hardware/saturn-models]] was wrong.
- 32 rows in four lines of eight: lines 1 and 2 for messages, results
  and lists, line 3 the calculator line, line 4 the menu labels (p. 21).
- Display RAM as on the 28S: the slave shows the left 68 columns, the
  master the right 69 ([[hardware/hp28s]] "Display"; 19BII ROM).

### Annunciators

Left to right above the display (p. 21): shift, alpha (α), past-due
appointment ((•)), low battery, radians (2π), printer. They are the
28S's slave words, written #FFFFF (on) or 0 (off):

| Word | Annunciator | Seen lit |
| --- | --- | --- |
| #FF800 | ((•)) appointment | from the moment an appointment came due (19BII ROM) |
| #FF808 | α | in TEXT, where letter keys type (19BII ROM) |
| #FF810 | battery | not seen (the 28S's battery word) |
| #FF818 | shift | after the shift key (19BII ROM) |
| #FF820 | (2π) | after MODES and its first menu key (19BII ROM) |
| #FF828 | none on the glass | only during the self-test's display steps (19BII ROM) |
| #FF830 | printer | not seen (the 28S's printer word) |

The self-test lights the words in pairs (#FF800 with #FF820, #FF808
with #FF828) and one at a time (19BII ROM).

## Keyboard

Matrix codes are Emu42's, as OUT bit and IN value (src:
[[sources/kml20]] OutIn Codes HP19BII); key labels and shifted labels
from the manual's figure (p. 22-23). The pattern is the 28S's
([[hardware/hp28s]] "Matrix pattern"): the letter half on OUT bits 6-1
and IN #100 #800 #40 #20 #1 #400 across, the display half on OUT bits
7-1 and IN #2 #80 #200 #4 #8 #10 across, ON on IN bit 15. Every code
below was checked on the ROM by pressing it on the cold-started MAIN
menu (19BII ROM).

- KML 2.0 prints INPUT as `6 128`, which is STO's code; the ROM answers
  INPUT at `5 128`, two columns wide like the 28S's ENTER.
- Keys act on release, as on the Pioneers: a digit held down shows
  nothing until it is let go (19BII ROM). The manual has MEM showing the
  free memory "until you release" it (p. 37); saturnus does not show it
  while held (open).

### Letter half

| Key | Shifted | OUT / IN |
| --- | --- | --- |
| A, B, C, D, E, F | Ä, Ã, Ç, Ø, Æ, Å | 6 / 256, 2048, 64, 32, 1, 1024 |
| G, H, I, J, K, L | ;, °, {, }, [, ] | 5 / same columns |
| M, N, O, P, Q, R | ∡, Ñ, Ö, ~, ', " | 4 / same |
| S, T, U, V, W, X | @, &, Ü, !, /, * | 3 / same |
| Y, Z, ?, $, #, : | <, >, ¿, £, comma, · | 2 / same |
| SPACE (two wide), INS, DEL, ◀, ▶ | Σ over SPACE; ◀◀ and ▶▶ (to the ends) over the arrows | 1 / 2048, 64, 32, 1, 1024 |

Above the keys sits a printed card: the MAIN menu's six labels (FIN,
BUS, SUM, TIME, SOLVE, TEXT) and what they hold, the CLEAR keys, the
contrast keys, and MATH, the number of decimal places and the language
(p. 22).

### Display half

| Key | Shifted (above) | OUT / IN |
| --- | --- | --- |
| six menu keys | - | 7 / 2, 128, 512, 4, 8, 16 |
| shift (blank, gold) | - | 6 / 2 |
| STO | - | 6 / 128 |
| RCL | - | 6 / 512 |
| DISP | MODES | 6 / 4 |
| PRNT | PRINTER | 6 / 8 |
| EXIT | MAIN | 6 / 16 |
| INPUT (two wide) | CLEAR DATA | 5 / 128 |
| +/− | E | 5 / 512 |
| ( | R↓ | 5 / 4 |
| ) | x≷y | 5 / 8 |
| ← (backspace) | CLEAR | 5 / 16 |
| ▲ | - | 4 / 2 |
| 7, 8, 9 | - | 4 / 128, 512, 4 |
| ÷ | 1/x | 4 / 8 |
| ▼ | - | 3 / 2 |
| 4, 5, 6 | - | 3 / 128, 512, 4 |
| × | ^ (power) | 3 / 8 |
| % | MATH | 2 / 2 |
| 1, 2, 3 | - | 2 / 128, 512, 4 |
| − | √x | 2 / 8 |
| ON | - | IN bit 15 |
| 0 | MEM | 1 / 128 |
| . | SHOW | 1 / 512 |
| = (ENTER in RPN, printed below) | LAST | 1 / 4 |
| + | x² | 1 / 8 |

## Case and colours

Measured on square-on photographs ([[sources/commons-hp19bii-photos]]
P1, checked on P2); approximate.

- Two halves of nearly equal size (on P1 the letter half 845 x 1451 px,
  the display half 905 x 1508, the display half nearer the lens). The
  hinge's barrel belongs to the letter half, on its right edge.
- Key pitch on the display half: menu keys 108.8 px apart, number keys
  135.6 px, rows 109.3 px; on the letter half 102.4 px across and 121.5
  px down, caps about 73 x 58 px.
- The reference card sits in a recess above the letter keys, in four
  grey-olive groups (MAIN MENU, CLEAR, DISPLAY CONTRAST, OTHER).
- Colours: case near-black (about #2b2a2b); grey keys (about #5d5b57)
  with white labels for the letters, STO, RCL, DISP, PRNT, INPUT, +/−,
  ( ), ←, ▲, ▼, % and ON; cream keys (about #e4e1d2) with dark labels
  for the menu keys, digits, operators and EXIT; the shift key blank,
  orange-yellow (about #eda646); shifted labels orange (about #eaa54b);
  ENTER printed light under =.

## Clock

- In saturnus the TIME menu's time advances once a second and an
  appointment set a minute ahead came due on time (19BII ROM); which
  chip's timer keeps the time was not traced.
- It is set in TIME SET, as on the 17BII ([[hardware/clocks]]): the
  time keyed as HH.MMSS and TIME, the date in the current format and
  DATE; M/D and 12/24 switch the formats (p. 176-177). The SET menu's
  keys are DATE, TIME, A/PM, M/D, 12/24, HELP.
- The format nibble is #C41E1: bit 0 set for day.month.year, bit 2 for
  the 24-hour clock, as the Pioneers' (found by pressing M/D and 12/24
  and watching RAM; 19BII ROM).
- A cold start sets 12:00 AM, Monday 01/01/1990 (19BII ROM).

## Cold start and language

After a memory loss the ROM asks for a language before anything else:
the menu keys, labelled D E F I P ENG, pick Deutsch, Español, Français,
Italiano, Português and English, in that order ([[sources/commons-hp19bii-photos]]
P2 shows the same prompt); English shows "MEMORY LOST" over 0.00 and the
MAIN menu (19BII ROM). The nibble #C402B reads 0 on that prompt and 2 in
every menu tried after it (19BII ROM), as #50092 does on the
international 17BII ([[hardware/clocks]]).

## Self-test

ON held while pressing the fifth menu key starts a repeating test: lit
patterns, then a pass message; any other key stops it with a FAIL
message, ON with the third menu key with MACHINE RESET (p. 279). The
fourth menu key starts the factory test. In saturnus the pass shows
"OK-19B II" on the top line about 7.6 seconds in, the ROM step included
(19BII ROM; the Lewis CRC rule of [[hardware/lewis]] "CRC").

## ROM image

The 19BII has no memory scanner, so its ROM cannot be read out without
opening the chips; the known images came from people at HP
([[hardware/saturn-models]] "ROM availability", [[sources/hpmuseum-forum]]
F3). @ractive's image is 131072 bytes in dump order (256 units of 512
bytes, last first) and carries "COPYRIGHT HP 198" in its first unit, as
the Pioneers' images do; the Lewis CRC over #00000-#1FFFF gives #FFFF in
address order. The self-test's pass message "OK-19B II" is kept whole
in the ROM (address order, byte #16F50), next to " FAIL"; the halves'
own tags "OK-19B-1DK" and "OK-19B-2A" start like a 19B's might, so the
whole message is what names a 19BII image (19BII ROM)
([[hardware/saturn-models]] "ROM image forms";
[[sources/emu42-pioneer-dump]]).
