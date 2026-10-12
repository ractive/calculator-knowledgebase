---
title: "HP 28S"
type: model
models: [28s]
status: draft
sources:
  - "[[sources/hp28s-om]]"
  - "[[sources/hp28s-rm]]"
  - "[[sources/kml20]]"
  - "[[sources/emu42-pioneer-dump]]"
  - "[[sources/hp28s-procnotes]]"
tags: [model, clamshell, lewis, keyboard, display, self-test]
---

# HP 28S

Clamshell RPN scientific calculator (1987-1988), the successor of the
28C: two halves that fold like a book, an alphabetic keyboard on the
left and the calculator keyboard and display on the right (src:
[[sources/hp28s-om]] p. 18-19). Two [[hardware/lewis]] chips, master and
slave ([[hardware/saturn-models]]). Emu42 runs it as `Model "O"` on
`Hardware "Lewis"` (src: [[sources/kml20]] Global).

Page numbers are the manuals' book pages: OM = [[sources/hp28s-om]],
RM = [[sources/hp28s-rm]].

## Summary

| Item | 28S |
| --- | --- |
| Chips | two Lewis, master and slave ([[hardware/saturn-models]]) |
| ROM | 128 KB at #00000-#3FFFF ([[sources/hp28s-procnotes]] Overview); one revision, 2BB (28C: 1BB, 1CC) ([[sources/emu42-pioneer-dump]] CLAMSHEL 1) |
| RAM | 32 KB at #C0000-#CFFFF, aliased at #D0000-#DFFFF ([[sources/hp28s-procnotes]] Overview); "32 Kbytes of user memory", about 400 bytes kept for the system (OM p. 188) |
| Display | 137 x 32 dots, four lines of 23 characters, seven annunciators above (RM p. 153, 269; OM p. 27, 29, 166) |
| Contrast | 0-31, reset 22; the ROM keeps keyboard changes within 16-31 ([[sources/kml20]] LCD contrast table; the 28C: reset 26, 20-31); ON held with + or − (OM p. 21, 216) |
| Keyboard | 35 keys on the left, 37 on the right including ON ([[sources/kml20]] OutIn Codes HP28S; OM p. 26-27) |
| Key buffer | 15 keys (RM p. 197) |
| Printer | one-way infrared to the HP 82240A, diode on the top edge of the right half (RM p. 168; OM p. 19-20) |
| Power | three N cells; about ten minutes to auto-off (OM p. 19-20, 286) |
| Version check | `# 10d SYSEVAL` shows the version and copyright (RM p. 126); in HEX mode `# A SYSEVAL` shows "Version 2BB" on the top line ([[sources/emu42-pioneer-dump]] CLAMSHEL 1) |

## Memory map

Facts marked "(28S ROM)" were found by running the owner's revision 2BB
image in saturnus on a logging bus (iteration 43a, 2026-10-13): cold
boot, arithmetic, menus, typing, printing, System Halt, Memory Reset and
the self-test.

| Range | What | Source |
| --- | --- | --- |
| #00000-#3FFFF | 128 KB ROM | [[sources/hp28s-procnotes]] Overview |
| #C0000-#CFFFF | 32 KB RAM, repeated at #D0000-#DFFFF | [[sources/hp28s-procnotes]] Overview |
| #FF800-#FFBFF | the slave Lewis's block: display RAM, annunciators, registers at #FFB00 | (28S ROM) |
| #FFC00-#FFFFF | the master Lewis's block: display RAM, registers at #FFF00 | [[sources/hp28s-procnotes]] SCR; (28S ROM) |

- The ROM's reset entry runs RESET and then writes the master's INPORT
  (#FFF0C, 4 then 0), both chips' contrast (#FFF01, #FFB01) and both
  chips' LPD (#FFB08, #FFF08) at once; it never executes CONFIG, UNCNFG
  or C=ID and touches nothing outside the four ranges above (28S ROM).
  Each chip's block has the 42S's layout ([[hardware/lewis]]) at another
  base: display RAM from offset 0, registers from offset #300. So each
  ROM uses a fixed map; where the chips take their bases from is open:
  [[questions/lewis-memory-map]].
- The memory-clear code at #005A2 builds its structures from #D0000
  downwards and the ROM keeps system variables from #C0000 (28S ROM);
  the first HOME variable ends at #D0000 ([[sources/hp28s-procnotes]],
  the SYSEVAL example).

## Two chips

- Both chips have the 42S's registers: contrast at offset #301, DSPCTL
  at #303 (DON in bit 3, contrast bit 4 in bit 1), LPD at #308, LPE at
#309, INPORT at #30C, LEDOUT at #30D, the timer controls at #30E/#30F
  and the timers at #3F7/#3F8 (28S ROM; layout from [[hardware/lewis]]).
  The ROM sets both chips' contrast to 6 with DSPCTL #A: 22, the KML 2.0
  reset value, and DON on both (28S ROM).
- The master's TIMER2 (#FFFF8) is the clock and auto-off timer, its CRC
  (#FFF04) serves the self-test, its LEDOUT (#FFF0D) drives the IR LED
  (ON + L writes it thousands of times) (28S ROM).
- The slave has timers of its own: the reset code loads its TIMER2
  (#FFBF8) with #E67, the interrupt code at #00A8F polls both chips'
  TIMER2 control nibbles, and while the command line is open the ROM
  rearms the slave's TIMER2 #E67 ticks (about 0.45 s at 8192 Hz) ahead
  each time it expires and sets RUN, INT and WAKE (#FFB0F = 7) before
  sleeping: the cursor blinks on it (28S ROM). The ROM reads the
  slave's LPD once and its battery routine (#00D30) reads the master's.
  Emu42's change log has the slave's power bits always 0 and a DON
  change on the slave affecting the annunciators (src:
  [[sources/emu42-changes]]), which fits the annunciators living in
  the slave's RAM (below).

## Display

- 137 pixels across, 32 down: the plot coordinates number the pixel
  columns 0-136 and the rows 0-31 from the lower left, with (68, 15) as
  the centre pixel (RM p. 153); DRAW evaluates at 137 points, one per
  column (RM p. 159).
- Four text lines of eight pixel rows. A display image (→LCD, LCD→) is
  548 bytes: 137 bytes per line, top line first, each byte one
  eight-pixel column with its least significant bit at the top (RM
  p. 269).
- 23 characters per line: the command line scrolls after 23 (OM p. 166),
  and a 23-character string fills a line right-justified (OM p. 257, from
  the PAD example). That makes a character cell six columns wide, the
  last cell one column short (inferred: 23 x 6 = 138).
- The seven annunciators sit in a row above the dot matrix, not inside
  it (OM p. 27 drawing).
- Display RAM: each column is eight nibbles, 32 bits, least significant
  bit the top row. The slave shows the left 68 columns from #FF840
  (offset #40), the master the right 69 from #FFC00, each half's
  address growing to the right ([[sources/hp28s-procnotes]] SCR, "each
  1/2 of LCD is 34*2 columns (+1 on right)"; the orientation is
  confirmed by the ROM's text reading right in both halves: "Memory
  Lost" in the left half, the 5 of 2 ENTER 3 + at the right edge; 28S
  ROM). The master's #FFE28-#FFE5F and the slave's #FFA60-#FFA7F are
  scratch the ROM writes, not shown (28S ROM).

### Annunciators

Left to right as drawn above the display, with the manual's meanings
(OM p. 27, 29):

1. Small circle: a suspended program.
2. Bent arrow: the shift key was pressed.
3. α: Alpha entry mode.
4. ((•)): busy, not ready for input.
5. Battery outline: low battery; at least ten hours of use remain, or
   about a month of memory if switched off at once (OM p. 286).
6. (2π): radians mode.
7. Printer symbol: sending data to the printer, whether or not a printer
   receives it (RM p. 168).

The segments are 5-nibble words in the slave's first #40 nibbles,
eight nibbles apart, written #FFFFF (on) or 0 (off), as the 42S's at
#40210 (28S ROM, each seen lit):

| Word | Annunciator | Seen lit |
| --- | --- | --- |
| #FF800 | busy | for about 0.2 s while it computes |
| #FF808 | α | with the α key |
| #FF810 | battery | with LPD's low-battery bit set |
| #FF818 | shift | after the shift key |
| #FF820 | (2π) | after RAD |
| #FF828 | suspended | after « HALT » EVAL |
| #FF830 | printer | while ON + L sends the display |

No word lights them all, unlike the 42S's #40210 (none was seen
written).

### Stack screen

With no command line and no menu, the four lines show levels 4: to 1:,
level number at the left, object right-justified; typing opens the
command line on the bottom line and the stack shows levels 3: to 1:;
a menu takes the bottom line for six inverse labels and pushes the
stack up one more line (OM p. 22-23). So `2 ENTER 3 +`:

1. Empty stack: `4:` `3:` `2:` `1:` down the left edge.
2. Typing 2: `3:` `2:` `1:` and the command line `2` with the cursor.
3. ENTER: `4:` `3:` `2:` `1:`, 2 at the right of level 1.
4. Typing 3: levels 3-1 with 2 in level 1, `3` on the command line.
5. +: four levels again, 5 at the right of level 1.

(The manual's own run is 15 ENTER 23 + giving 38; OM p. 22-23. The 3 can
follow 2 without a second ENTER, OM p. 24.)

After a memory reset the top line shows "Memory Lost" until a key is
pressed (OM p. 21, 217).

## Keyboard

The left (alpha) keyboard has six rows of six keys, SPACE taking two
places; the right keyboard has seven rows: six blank menu keys under the
display, then rows of six, five and five keys (ENTER takes two places).
The shift key is a blank coloured key, the first key of the right-hand
second row. Shifted functions are printed in colour above the keys
(OM p. 26-27, 29). Matrix codes are Emu42's, given as OUT bit and IN
value (src: [[sources/kml20]] OutIn Codes HP28S); the OUT register is 12
bits and the IN register 16 ([[sources/hp28s-procnotes]] I/O Registers).

### Matrix pattern

- Left keyboard: rows 1-5 (A-F, G-L, M-R, S-X, Y-( ) are OUT bits 6, 5,
  4, 3, 2; the bottom row is OUT bit 1. Across a row the six columns read
  IN #100, #800, #40, #20, #1, #400 (256, 2048, 64, 32, 1, 1024).
- Right keyboard: rows 1-7 are OUT bits 7, 6, 5, 4, 3, 2, 1. Across a
  row the columns read IN #2, #80, #200, #4, #8, #10 (2, 128, 512, 4, 8,
  16); rows of five keys use the last five.
- ON is IN bit 15 (#8000); KML writes it as OUT 0, IN 32768, i.e. no
  OUT line is needed to read it (inferred, as on the
  [[hardware/hp42s]]).
- So the two keyboards share OUT lines 1-6 and split the IN lines:
  left on IN bits 0, 5, 6, 8, 10, 11; right on bits 1, 2, 3, 4, 7, 9.
  OUT bit 7 serves only the menu keys; no key sits on OUT bit 0.

### Left keyboard

Key, shifted label, then OUT bit / IN value (OM p. 26; [[sources/kml20]]):

| Key | Shifted | OUT / IN |
| --- | --- | --- |
| A | ARRAY | 6 / 256 |
| B | BINARY | 6 / 2048 |
| C | COMPLX | 6 / 64 |
| D | STRING | 6 / 32 |
| E | LIST | 6 / 1 |
| F | REAL | 6 / 1024 |
| G | STACK | 5 / 256 |
| H | STORE | 5 / 2048 |
| I | MEMORY | 5 / 64 |
| J | ALGBRA | 5 / 32 |
| K | STAT | 5 / 1 |
| L | PRINT | 5 / 1024 |
| M | CONTRL | 4 / 256 |
| N | BRANCH | 4 / 2048 |
| O | TEST | 4 / 64 |
| P | - | 4 / 32 |
| Q | CATALOG | 4 / 1 |
| R | UNITS | 4 / 1024 |
| S | ≤ | 3 / 256 |
| T | ≥ | 3 / 2048 |
| U | → | 3 / 64 |
| V | Σ | 3 / 32 |
| W | ° | 3 / 1 |
| X | µ | 3 / 1024 |
| Y | < | 2 / 256 |
| Z | > | 2 / 2048 |
| # | " | 2 / 64 |
| { | } | 2 / 32 |
| [ | ] | 2 / 1 |
| ( | ) | 2 / 1024 |
| SPACE (two wide) | NEWLINE | 1 / 2048 |
| « | » | 1 / 64 |
| = | ≠ | 1 / 32 |
| LC | ? | 1 / 1 |
| α | MENUS | 1 / 1024 |

- The shifted labels of rows 1-3 select menus; CONTRL, BRANCH and TEST
  are bracketed as PROGRAM menus (OM p. 26, 28).
- LC toggles lower case; α picks the entry mode by hand; shifted α
  (MENUS) is Menu Lock, which swaps shifted and unshifted on the top
  three rows so menus need no shift and letters do (OM p. 28-29, 168-169).
- The left keyboard is not a menu-key row: A-F are letters.

### Right keyboard

| Key | Shifted (above) | OUT / IN |
| --- | --- | --- |
| menu 1 (INS in white above) | - | 7 / 2 |
| menu 2 (DEL) | - | 7 / 128 |
| menu 3 (▲) | - | 7 / 512 |
| menu 4 (▼) | - | 7 / 4 |
| menu 5 (◀) | - | 7 / 8 |
| menu 6 (▶) | - | 7 / 16 |
| shift (blank, coloured) | - | 6 / 2 |
| cursor-menu key (four-arrow symbol) | MODE | 6 / 128 |
| TRIG | LOGS | 6 / 512 |
| SOLV | PLOT | 6 / 4 |
| USER | CUSTOM | 6 / 8 |
| NEXT | PREV | 6 / 16 |
| ENTER (two wide) | EDIT | 5 / 128 |
| CHS | VIEW↑ | 5 / 512 |
| EEX | VIEW↓ | 5 / 4 |
| DROP | ROLL | 5 / 8 |
| ← (backspace) | SWAP | 5 / 16 |
| ' | VISIT | 4 / 2 |
| 7 | COMMAND | 4 / 128 |
| 8 | UNDO | 4 / 512 |
| 9 | LAST | 4 / 4 |
| ÷ | 1/x | 4 / 8 |
| STO | RCL | 3 / 2 |
| 4 | PURGE | 3 / 128 |
| 5 | ∫ | 3 / 512 |
| 6 | d/dx | 3 / 4 |
| × | ^ | 3 / 8 |
| EVAL | →NUM | 2 / 2 |
| 1 | CONT | 2 / 128 |
| 2 | % | 2 / 512 |
| 3 | %CH | 2 / 4 |
| − | √x | 2 / 8 |
| ON (ATTN printed below) | OFF | IN bit 15 |
| 0 | CLEAR | 1 / 128 |
| . | π | 1 / 512 |
| , | CONVERT | 1 / 4 |
| + | x² | 1 / 8 |

- The six menu keys take their meaning from the six labels on the bottom
  display line. With no labels showing they run the cursor menu printed
  in white above them: INS (insert/replace), DEL, and cursor up, down,
  left, right. Shifted, INS and DEL delete everything left of the
  cursor or from it rightwards, and the arrows jump to the top, bottom,
  left or right end of the command line (OM p. 28-29, 166-168).
- The key KML 2.0 calls "<^v>" is the **cursor-menu key**, drawn with a
  four-arrow symbol: "Cursor Menu On/Off" turns the cursor menu on, or
  back to the previous menu (OM p. 168); KEY names it "CURSOR" (RM
  p. 197); shifted it is MODE (OM p. 27).
- ON turns the calculator on; while on it is ATTN (clear the command
  line, stop a program); shift ON is OFF (OM p. 20, 30).
- KEY returns the key-top text except INS, DEL, UP, DOWN, LEFT, RIGHT,
  CURSOR, BACK (backspace) and a space for SPACE (RM p. 197).

## ON chords

Every system operation starts with ON held; ON + DEL before releasing ON
cancels it (OM p. 215). Matrix codes as above.

| Chord | Keys in the matrix | Effect |
| --- | --- | --- |
| ON + L | ON, 5/1024 | print the display (PRINT is above L) (OM p. 149, 216) |
| ON + + / ON + − | ON, 1/8 / ON, 2/8 | contrast darker / lighter; hold ON and repeat (OM p. 21, 216) |
| ON | ON | Attention: clear the command line, stop programs, leave FORM, PLOT and catalogs (OM p. 216) |
| ON + ▲ | ON, 7/512 | System Halt (OM p. 217) |
| ON + INS + ▶ | ON, 7/2, 7/16 | Memory Reset (OM p. 20, 217) |
| ON + ◀ | ON, 7/8 | repeating self-test (OM p. 218) |
| ON + NEXT | ON, 6/16 | keyboard test (OM p. 219) |
| ON + ▼ | ON, 7/4 | a factory test that waits for keyboard input (OM p. 291) |
| ON + DEL | ON, 7/128 | cancel the pending system operation (OM p. 215) |

The arrow keys here are the menu keys with those white labels.

### System Halt (warm reset)

Hold ON, press the ▲ menu key, release ON. Besides the effects of
Attention it clears suspended programs and local variables, the stack,
the saved CMD/UNDO/LAST copies and the custom menu, makes HOME the
current directory, turns the cursor menu on and trace printing off. It
is the way out of an endless loop that ON does not stop (OM p. 217,
286).

### Memory Reset (cold start)

Hold ON; hold INS (the left menu key); press and release ▶ (the right
menu key); release INS; release ON. The manual's chapter 25 has INS and
▶ released together. The calculator beeps and shows "Memory Lost" in
display line 1 until a key is pressed. All user variables and
directories go, user flags and modes return to their defaults, and the
System Halt effects apply. To back out, keep ON down, press DEL, then
release ON (OM p. 20-21, 217).

## Self-test

Repeating test: hold ON, press the ◀ menu key, release ON.

In saturnus (28S ROM) the pass ends in "8 FAIL": the failure number is
the ST bits 8-11 nibble, and its ROM step sets ST bit 11. The step
(#0169B, CRC routine #01770) clears the master's CRC, reads
#0001C-#1FFFB as three blocks of two interleaved halves #5550 nibbles
apart in 16-nibble reads, and wants #FFFF; with the 48's CRC rule the
owner's image gives #185A. Then it checks #00000-#00FFF, #2001C-#3FFFB
and #20000-#20FFF the same way. The same step fails on every Lewis ROM
so far: [[questions/hp42s-rom-crc]].

 It shows
horizontal and vertical lines, a blank display, a random pattern, then
the result briefly, and starts again. "OK-28S" means pass; a message
such as "1 FAIL" is a failure, the number naming the fault. A key press
during the test gives a meaningless failure. With a printer on, it
prints numbers for service. Leave it with System Halt (ON + ▲) (OM
p. 218, 290-291).

Keyboard test: hold ON, press NEXT, release ON. The display reads
"KEYBOARD TEST". Press every key in order: the left keyboard row by row
from A, then the right keyboard row by row from INS, ON in its place
(it does not end the test). "OK-28S" on success, "n FAIL" on a wrong
order or a fault; ON ends (OM p. 219). Both tests perform a System Halt
(OM p. 218).

## Printer

One-way infrared to the HP 82240A from the top edge of the right half;
the calculator cannot tell whether anything prints. It paces its output
for a printer on weak batteries; flag 52 selects the faster rate for a
mains-powered printer (RM p. 168-169). ON + L or PRLCD prints the
display (OM p. 149). Trace mode prints a record of the calculations and
slows the keyboard (RM p. 168).

## Memory

32 Kbytes of user memory, about 400 bytes reserved for the system (OM
p. 188); MEM gives the free bytes. Under 128 free bytes "Low Memory!"
flashes in the top line at every key; worse, the stack lines show only
object types ("No Room to Show Stack"), and at the end an "Out of
Memory" purge dialogue takes over (OM p. 188-189).

## ROM image

The owner's image (128 KB, SHA-256 51589fa3...1e921c, "Version 2BB" in
the copyright message) is in dump order: 256 units of 512 bytes, last
first, as the Pioneer images ([[hardware/saturn-models]] "ROM image
forms"). In address order the ROM starts with a GOSBVL to its reset
code at #004BD, ends with "Copyright HP 1986, 1987" from an odd nibble
(#3FFB7), and keeps the self-test's "OK-28S" at #01C9F and "FAIL" at
#01C8D (28S ROM).

## Not found

- Other self-test failure numbers than 8 (the ROM's), and what the
  factory test on ON + ▼ checks.
- A character-cell width stated outright (six columns is inferred).
