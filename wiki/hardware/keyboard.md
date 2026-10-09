---
title: "Keyboard matrix"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
status: draft
sources:
  - "[[sources/mastracci-saturn-guide]]"
  - "[[sources/keyboard-ervin]]"
  - "[[sources/teuwen-gx-hardware]]"
  - "[[sources/keyb49-sylvester]]"
  - "[[sources/saturn-tutorial]]"
  - "[[sources/duchesne-interrupts-en]]"
  - "[[sources/voyage-48gx]]"
  - "[[sources/hp48sx-om]]"
  - "[[sources/hp48g-ug]]"
  - "[[sources/hp49g-um]]"
tags: [saturn, keyboard]
---

# Keyboard

## Scanning model

- The keyboard is a matrix read through the CPU ports: write a row mask to
  OUT (9 bits used), read the column bits from IN (6 bits used). Each OUT bit
  drives one line high; each IN bit reports a line connected to a driven line
  through a pressed key (src: [[sources/mastracci-saturn-guide]] 4.9).
- Row masks can be ORed; OUT=#1FF returns non-zero IN if any key is down
  (4.9).
- The ON key is wired to its own CPU pin and appears as IN bit 15 (#8000)
  whatever OUT holds (4.9, 5.8, 6.3 code).
- Unless INTOFF is in effect, the CPU itself scans the keyboard every 1 ms
  and raises an interrupt on a key-down; the ROM handler then scans row by
  row and arms a 1/16 s timer to track held and released keys (4.9).

- The hardware scan only interrupts on key-down; there is no release
  interrupt. The ROM polls for release by re-arming a 1/16 s timer interrupt
  while any key is held (src: [[sources/keyboard-ervin]] 2).
- The ROM debounces by sampling the whole keyboard every 2 ms until 5
  identical samples, so a keystroke takes more than 10 ms to register, and
  its service loop is synchronised to TIMER1 (src: [[sources/keyboard-ervin]]
  3.3). An emulator's key-press events must last long enough for this.
- The alpha and shift keys are polled outside the keyboard service routine to
  update the annunciators (src: [[sources/keyboard-ervin]] 4.1.2).

- The handler restores OUT from its RAM shadow (#704C3 on S, #80642 on G), so
  a program scanning the keyboard with interrupts on must write its row mask
  to the shadow before each OUT=C (src: [[sources/duchesne-interrupts-en]]
  p. 14).
- ON-key combinations recognised by the handler: ON with +, -, B, A F, 1, 4,
  C, D, E, SPC; ON alone increments the ATTN counter (#70679 S, #807F7 G)
  (p. 13).

- Voyage: ON needs no OUT mask, its press always sets IN bit 15; to test it
  reliably interrupts must be off. The ROM routine at #01EEC does OUT=C then
  C=IN (src: [[sources/voyage-48gx]] p. 80). The interrupt-type register #119
  bit 3 flags a keyboard interrupt (p. 198). With the display off the keyboard
  is inactive (p. 193).
- The buzzer is OUT bit 11: OUT #800 then OUT #000 makes a click (p. 80).

## ROM data structures (48SX)

KeyBuf at #704EA: get pointer, put pointer (one nibble each), then 16 one-byte
key codes; empty when get = put. KeyState at #704DD: 13 nibbles, one bit per
key, bit 0 unused, then + SPC . 0 ' - 3 ... up to B. ORshadow (OUT shadow)
at #704C3, 3 nibbles. KBdisable at #704DC: non-zero skips the keyboard service
routine (src: [[sources/keyboard-ervin]] 3.4). These addresses are specific to
the 48SX ROM.

Key codes count from the top-left: 1 = A (F1), #19 ENTER, #1F 7, #31 +.
Alpha #80, left shift #40, right shift #C0, ORed into the following key; ON has no
code (src: [[sources/keyboard-ervin]] 3.4.1).

## HP48 matrix (OUT bit x IN bit)

| OUT | IN #20 | #10 | #08 | #04 | #02 | #01 |
| --- | --- | --- | --- | --- | --- | --- |
| #100 | - | F2 (B) | F3 (C) | F4 (D) | F5 (E) | F6 (F) |
| #080 | - | PRG | CST | VAR | up | NXT |
| #040 | - | STO | EVAL | left | down | right |
| #020 | - | COS | TAN | SQRT | y^x | 1/x |
| #010 | ON | ENTER | +/- | EEX | DEL | backspace |
| #008 | alpha | SIN | 7 | 8 | 9 | / |
| #004 | left shift | MTH | 4 | 5 | 6 | x |
| #002 | right shift | F1 (A) | 1 | 2 | 3 | - |
| #001 | - | ' | 0 | . | SPC | + |

(src: [[sources/saturn-tutorial]] p. 136 and [[sources/keyboard-ervin]]
Figure 1; [[sources/mastracci-saturn-guide]] 4.9 has the same table with the
two shift keys swapped, which is an error, see Contradictions). The 4.9 table places ON at
OUT #010 / IN #20, but 4.9 text, 5.8 and 6.3 put ON on IN bit 15 regardless
of OUT; the #20 cell is probably a misprint (see Contradictions).

Ervin's Figure 1 puts the same keys in the same cells and confirms ON in "a
column of its own" at IN bit 15 (src: [[sources/keyboard-ervin]] 3, 3.2).

The 5.8 connector table maps OUT bits 0-8 to address lines A9-A16, AR17 and
IN bits 0-5 to A0-A5 on the GX board (src: [[sources/mastracci-saturn-guide]]
5.8). The keyboard shares the address bus pins.

Emu48's KML OutIn codes ("OUT bit, IN value") give the same 48 matrix, with
left shift at OUT bit 2 / IN #20 and ON at IN #8000; the 38G uses this
matrix too (src: [[sources/kml20]] OutIn codes HP48SX).
The 38G has 47 of the 48's 49 key positions; its key-by-key table is on
[[hardware/hp38g]].

## HP49G matrix

The 49G uses 8 OUT lines (#001-#080) and 8 IN lines (#0001-#0080); ON is
IN #8000 for any OUT (src: [[sources/keyb49-sylvester]] 1.1).

| OUT | IN #80 | #40 | #20 | #10 | #08 | #04 | #02 | #01 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| #080 | - | - | - | - | alpha | left shift | right shift | - |
| #040 | - | - | - | - | up | left | down | right |
| #020 | APPS | - | F6 | F5 | F4 | F3 | F2 | F1 |
| #010 | MODE | HIST | y^x | EEX | - | - | - | - |
| #008 | TOOL | CAT | SQRT | +/- | 7 | 4 | 1 | 0 |
| #004 | VAR | EQW | SIN | X (variable key) | 8 | 5 | 2 | . |
| #002 | STO | SYMB | COS | 1/x | 9 | 6 | 3 | SPC |
| #001 | NXT | DROP (backspace key) | TAN | / | * (multiply) | - | + | ENTER |

(src: [[sources/keyb49-sylvester]] 1.1, arranged from the per-key list;
[[sources/saturn-tutorial]] p. 136-137 gives the same positions and confirms
that OUT #001 / IN #40 is backspace, that "X" is the X key and the #001/#08
key is multiply.)

Emu48's OutIn table for the 49G agrees key for key and applies to the 39G
and 40G as well (src: [[sources/kml20]] OutIn codes HP49G). The 39G key
names at each position are on [[hardware/hp39g-40g]].

## Contradictions

Reading the 5.8 connector table with each O/I label pair belonging to the key
row above it, it agrees with the 4.9 table for every key except two:

- **Shift keys.** 4.9 puts left shift at OUT #002 / IN #20 and right shift at
  OUT #004 / IN #20. 5.8 puts "shift left" on A11 (OUT #004) and "shift
  right" on A10 (OUT #002), the reverse (src: [[sources/mastracci-saturn-guide]]
  4.9, 5.8). Ervin labels #004/IN#20 "yel" and #002/IN#20 "blu" (src:
  [[sources/keyboard-ervin]] Figure 1); on the 48SX left shift is the orange
  ("yellow") key and right shift the blue one (unverified here; check a user
  guide), which supports Teuwen/5.8 against the 4.9 table. Teuwen 5.8 is the
  same data as [[sources/teuwen-gx-hardware]] 9. The tutorial's table gives
  left shift 004/0020 and right shift 002/0020 (src:
  [[sources/saturn-tutorial]] p. 136). Resolved: Mastracci 4.9 is wrong. See
  [[questions/keyboard-matrix-shift-keys]] (answered).
- **ON polarity in the tutorial.** p. 138 says "if bit 15 is null, [ON] was
  pressed", but the code on p. 139 loops while bit 15 is 0. Every other
  source says a pressed ON sets IN bit 15.
- **ON.** 4.9 lists ON at OUT #010 / IN #20; 5.8 shows ON between +Vcc and a
  dedicated ON-key pin, and 4.9 text and 6.3 say ON is IN bit 15. Treat ON as
  IN bit 15 only (unverified for the #010/#20 cell).

## HP49G alpha letters (2026-10-05)

In alpha mode the softkeys F1-F6 type A-F, and APPS, MODE, TOOL, VAR, STO,
NXT, HIST, CAT, EQW, SYMB, y^x, square root, SIN, COS, TAN, EEX, +/-, X,
1/x and divide type G to Z, in that order (measured by typing them with
ALPHA locked on the saturnus emulator running ROM 2.15; the saturnng TUI's
letter keys, which follow alpha labels, give the same screens). See
[[hardware/hp49g]].

## Shift colours and printed labels (2026-10-09)

Which shift a printed label belongs to, per model (checked for the
saturnus skins, iteration 30):

- **48SX**: the orange key is the left shift and its labels are orange;
  the blue key is the right shift, its labels blue (OFF is blue, above
  ON) (src: [[sources/hp48sx-om]] p. 1-3). This settles the "unverified
  here" above: left shift is the orange key.
- **48G/GX**: purple left shift, green right shift, labels in the same
  colours (src: [[sources/hp48g-ug]] p. 1-5). The labels printed alone in
  green are the twelve applications (CHARS, EQ LIB, I/O, LIBRARY, MEMORY,
  MODES, PLOT, SOLVE, STACK, STAT, SYMBOLIC, TIME), all right-shifted;
  each one's left-shifted key gives the unlabelled command menu of the
  same name (p. 1-6). The UNITS catalog is right-shifted, the UNITS
  command menu left-shifted (p. 10-1 ff.). Measured on the saturnus
  emulator with ROM R: left shift + 7 shows the SOLVE command menu
  (ROOT DIFFE POLY SYS TVM), right shift + 7 the "Solve equation…"
  choose box of the SOLVE application.
- **49G**: blue left shift (FILES above APPS), red right shift (PASTE
  above NXT) (src: [[sources/hp49g-um]] p. 1-3).
- **38G, 39G/40G, 42S**: one shift key.

## Time awake after a key (2026-10-09)

Measured on the saturnus emulator, not in a manual: each ROM booted to
its start screen, a key held 60 ms and released at 40 phases of the
ROM's timer (7.3 ms apart), then the time until the CPU next sleeps in
SHUTDN. Longest of the 40, in emulated ms, after a shift, a digit,
ENTER and a function key (√x; SIN on the 39G/40G):

| Model | shift | digit | ENTER | function |
|-------|-------|-------|-------|----------|
| 48SX (ROM J) | 583 | 317 | 605 | 753 |
| 48GX (ROM R) | 521 | 197 | 516 | 508 |
| 49G (ROM 2.15) | 5 | 74 | 462 | 89 |
| 38G (A1.67) | 519 | 523 | 968 | 523 |
| 39G, 40G | 5 | 243 | 244 | 243 |
| 42S (rev. C) | 17 | 50 | 62 | 78 |

The 48SX's time after a shift has two values by phase, about 250 ms or
about 525 ms; the 48GX's about 155 or 520 ms; the 38G's about 60 or
520 ms. A key pressed in that time may be lost: on the 48SX a second
left shift pressed about 410 ms after √x was released, while the ROM
was still awake, never lit the annunciator, and the √x after it ran
unshifted. saturnus therefore waits up to the longest time per model
before it sends the next queued key (saturnus decision log, "Key waits
per model").
