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
  - "[[sources/hp42s-om]]"
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

Functions not printed on the keys (held keys, ⬅ as CLX) and the EXIT
combinations, from the manual and a key sweep in saturnus:
[[hardware/hp42s-keyboard]].

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

A key sweep of 2026-10-25 saw nothing for EXIT + 1/x, and the manual's
memory clear is EXIT + Σ+ + XEQ: [[hardware/hp42s-keyboard]]
"Contradictions".

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

## Held keys: preview and NULL

Holding a key that runs a function shows the function's name; after
"about a second" NULL replaces it, stays until the key is let go, and the
function does not run; let go before NULL, it runs (src:
[[sources/hp42s-om]] p. 75). So the ROM must see a key as held while it
is down, not only when it is released.

How the ROM gets there (42S ROM revision C, traced in saturnus):

- The keyboard interrupt's handler debounces the keys itself: it scans the
  matrix row by row (OUT #01, #02 ... #20, then OUT #3F again) and wants
  six identical scans, each taken when the TIMER2 nibble at #403F9
  changes, about every 2 ms, with interrupts enabled. A new key goes into
  a key buffer; the current key bitmap is kept in RAM (#500A6, 13
  nibbles).
- The main loop (around #009A3) takes keys from the buffer, shows the
  preview and times the hold; a timer interrupt about every 1/16 s keeps
  rescanning until the key is released.
- An emulator whose keyboard interrupt looks at IN through the OUT lines
  the ROM drives sees each row scan as a new key-down: the edge arrives
  while the handler runs, the handler runs again at RTI, and the main loop
  never runs while the key is held (saturnus before 2026-10-13: every
  key acted on release, no preview, no NULL). With the interrupt taken
  from a scan of the whole keyboard ([[hardware/keyboard]] "Scanning
  model") the preview shows at once and NULL after 0.95 s of emulated
  time at 1 MHz.

## Menu labels in RAM

Observed in saturnus on 2026-10-25 with a revision C image (its last
bytes read "OK-42S-EC"; this image's SHA-256 is
841e79a9f0878882f6dd9343d428f1065e3ea12c1da4c11980061db4615b0baf, a
different byte order from the image under "ROM image"). Found by
pressing keys and comparing RAM, then checked against the LCD; no ROM
code was read. Not checked on a real 42S.

- **Label buffer**: the texts of the six menu labels, left to right,
  sit in six entries of 11 nibbles from #5033C (to #5037D). Each entry
  is a length nibble (0 to 5), then up to five characters of two
  nibbles each, low nibble first. CONVERT's first row reads `→DEG`,
  `→RAD`, `→HR`, `→HMS`, `→REC`, `→POL`.
- **Characters** are the 42S's codes, not ASCII throughout: 0x0F is →
  and 0x05 is Σ (`Σ+`, `ALLΣ`).
- **Full label, no mark**: a toggle's label is kept without its ■.
  STAT's second row shows `ALL■` on the screen and holds `ALLΣ`; DISP
  shows `FIX■` and `RDX.■` and holds `FIX` and `RDX.`.
- **Five characters at most**: a program named ABCDEFG shows as
  `ABCDE` in the XEQ menu and its entry holds those five. The nibbles
  after the length can hold stale characters from an earlier label.
- **Blank keys**: an entry of length 0 (STAT's second row keys 3 and 4,
  MODES key 4); the 42S still draws an empty box there. In the ALPHA
  menu's ABCDE level the sixth key holds one space and shows an empty
  box.
- **Not cleared**: after EXIT the entries keep the last menu's labels
  (CONVERT's after shift CONVERT EXIT), and after a label typed with the
  ALPHA menu in program entry they keep the ALPHA labels. Only after a
  cold start were all six empty. So a reader must first know that a
  menu shows.
- **Menu shown**: the nibble at #50201 reads 0 with no menu, 1 in a
  menu and 2 in a submenu: BASE LOGIC, CATALOG FCN and CATALOG PGM, the
  ALPHA menu's letter level, the Solver's and ∫f(x)'s variable menus.
  The variable menu VARMENU shows from the keyboard reads 1. It read 0
  again after EXIT, and 0 in program entry once a step from PGM.FCN was
  keyed and the menu had gone.

Menus checked, each against its screen: CONVERT (both rows), STAT (both
rows), DISP, PROB, BASE, BASE LOGIC, MODES, CATALOG, CATALOG FCN (two
rows), CATALOG PGM, the ALPHA menu (both levels, also inside a LBL
prompt), the XEQ menu (empty: `.END.`; with programs: their labels),
PGM.FCN (all four rows, in program entry), the Solver's program menu and
its variable menu (`X`, `Y` from MVAR), the same for ∫f(x), the menu
VARMENU shows, and the RCL prompt's variable menu (`A`, `REGS`). Some
were reached by two key paths with the same result.

Not checked: CUSTOM and ASSIGN, FLAGS, PRINT, TOP.FCN, CLEAR, MATRIX and
the matrix editor, CATALOG REAL, CPX, MAT and MEM, the Solver's and
∫f(x)'s further menus, complex mode. A programmable menu (a program
running KEY 1 GTO "S", MENU, STOP) showed six empty boxes on the screen
and six empty entries, with #50201 at 1; why "S" did not show was not
found (open).

## ROM image

Revision C, 65536 bytes packed, SHA-256
f4c5f9f0e1d89074b7ca49add99b3ea72ed7fae9370b421de20a0cd8384c08f3. It boots, shows "Memory Clear" on a cold start and computes (2 ENTER 3
+ gives 5). With the 48's CRC rule the self-test's ROM step computed
#1BE8 instead of #FFFF; with the Lewis rule ([[hardware/lewis]] "CRC") it
gives #FFFF, so the image is good: [[questions/hp42s-rom-crc]].

## Cold start

A cold start with blank RAM shows "Memory Clear" on the upper line and
`x: 0.0000` on the lower, the stack ready; no key has to be answered (42S
ROM). Power-on contrast 22 (range 15-31), observed on ROM C after a cold
start (observed in saturnus 2026-10-07; saturnus renders it at about 90 % darkness).

## Clock

None: no time or date functions, and none of the clock strings of the
27S and 17BII ROMs in its ROM ([[hardware/clocks]]).
