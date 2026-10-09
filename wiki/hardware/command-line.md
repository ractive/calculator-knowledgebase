---
title: "The command line (edit line) in RAM, its keys and typing speed (48SX, 48GX, 49G)"
type: hardware
models: [48sx, 48gx, 49g]
status: draft
sources: ["[[sources/hp48sx-om]]", "[[sources/rplman]]", "[[sources/hp48-internals-address-list]]"]
tags: [ram, keyboard, command-line, rpl, memory]
---

# The command line (edit line) in RAM

Where the ROM keeps the text being edited, its cursor and the state of
the editor (active, entry mode, alpha and shifts), what the keys type into
it, and how fast it takes key presses. Found for saturnus iteration 19
(the typing engine of the command palette) by observation in saturnus, as
[[hardware/hp48-system-ram]] was: type known text, scan RAM for it, diff
system RAM across key presses. ROMs: 48SX J, 48GX R, 49G 2.10. RPLMAN
documents the edit line only as an argument of `InputLine` (initial text
and cursor) (src: [[sources/rplman]] p. 7474-7560 of RPLMAN.TXT); no
document found gives its location. The 1991 address list has no entry for
it (src: [[sources/hp48-internals-address-list]]).

## Text

- The text is stored **reversed and NUL-terminated, just below the
  temporary environments**: the first character is the byte ending at
  `[LAM] - 1`, the next one two nibbles lower, and so on, until a 00 byte.
  `LAM` is the 5-nibble pointer the 1991 list calls `local_vars`: #70583
  on the 48SX, #80702 on the 48GX and 49G. Typing "ABCDEF" on the 48SX
  after a memory clear leaves `645444342414` at #7FED7-#7FEE2 with
  [#70583] = #7FEE3 (observed).
- No length field: changing only the length (inserting a character at the
  start with the cursor left at 0) changes no system RAM except the text
  (observed, 48SX). The area between the end of the data stack ([#7057E],
  [#806FD]) and the text is slack that the ROM grows downward, moving the
  data stack, as the line gets longer; there is always at least one 00
  byte below the text.
- The editor refuses the NUL character: echoing character 0 from the 48GX
  CHARS application shows "Can't Edit Null Char." (observed). This agrees
  with the terminator.
- The text stays in RAM after the line is entered or cancelled: read it
  only while the active bit (below) is set.
- A newline in the line is character 10 (right-shift `.` on the 48 and
  49G types it).

## Cursor and editor state

| | 48SX | 48GX | 49G |
| --- | --- | --- | --- |
| Text: pointer to the end (`local_vars`) | #70583 | #80702 | #80702 |
| Cursor, 5 nibbles, 0 = before the first character | #70704 | #80882 | #80F61 |
| Entry nibble: bit 1 insert mode, bit 2 algebraic entry | #70685 | #80803 | #80EC2 |
| Line nibble: bit 1 command line active, bit 2 lowercase lock | #70687 | #80805 | #80EC4 |
| Keys nibble: bit 0 left shift, bit 1 right shift, bit 2 alpha | #706C4 | #80842 | #80F01 |
| Alpha lock, bit 0 | #70793 | #80911 | #80FF0 |
| Program entry, bit 0 | #70794 | #80912 | #80FF1 |

- Cursor: counts characters, newlines included; it reached #0010E with
  270 characters typed (48SX), so it is wider than two nibbles. A copy sits
  at #7070E (48SX), #8088C (48GX), #80F6B (49G); not used.
- Active: set while a command line is open, also when it was emptied with
  backspace (ENTER on an emptied line does not duplicate level 1 on the
  48SX) and in an `EDIT` session; clear after ENTER and ATTN, and while
  the 48GX CHARS application or the 49G's error box has the screen.
- Insert mode: the 48SX EDIT menu's INS key clears bit 1 (replace mode).
- Program entry: set by `«`; algebraic entry by `'`.
- The 48GX offsets are those of the 48SX plus #1017E, except the text
  pointer (plus #1017F, as the stack pointers); the 49G's are the 48SX's
  plus #1083D (entry, line and keys nibbles) or #1085D (cursor, alpha lock,
  program entry). Observed, not explained.

## What the keys type (alpha mode)

Surveyed by pressing every key, plain and after each shift, on a command
line in immediate, algebraic and program entry modes, with alpha locked
and without, and reading the line back (saturnus iteration 19; the
committed tables are generated from such a survey).

- With alpha locked, 121 characters (48SX, 48GX) and 110 (49G) are typed
  by one key with at most one shift, the same in all three entry modes and
  on the 49G in both its algebraic and RPN operating modes. Without alpha,
  most non-digit keys are commands, which execute in immediate entry mode.
- In alpha mode the left-shifted keys for `( )`, `[ ]`, `{ }` and `« »`
  insert both delimiters with the cursor between them, without the spaces
  and newlines the non-alpha `«` key adds on the 48 (`« ` newline `» `).
- Accents: on the 48SX, 48GX and 49G the alpha-shifted 7, 8 and 9 keys
  change the character left of the cursor: `A` then left-shift 7 gives
  `À`; 61 Latin-1 letters are typed this way (src: [[sources/hp48sx-om]]
  p. 2-9 describes the method; the pairs observed).
- On the 48 in alpha mode the arrow keys type letters; on the 49G they move
  the cursor.
- Typed in program entry mode (non-alpha), the power, square root and
  right-shifted SIN, COS, TAN keys of the 48 insert `^ √ ∂ ∫ Σ` (the 49G:
  `∂ ∫ Σ` on its right-shifted SIN, COS, TAN and `≤ ≥ ≠` on left-shifted
  X, 1/x, +/-), with a space before the character when the one left of
  the cursor is not a space and a space or `()` after it; in algebraic
  entry inside a program without the spaces. In immediate entry mode the
  same keys execute (closing the line). The ENTRY key (right-shift
  alpha) switches immediate entry to program entry
  (src: [[sources/hp48sx-om]] p. 3-16).
- Not typable on the 48SX by any key: the control characters except
  newline, `;`, backslash, backquote, DEL (127), `∇ ▶ ■` and 23 Latin-1
  signs (160, 166-170, 172-175, 178-180, 182-186, 188-190, 215, 247);
  195 of characters 1-255 are. The 48GX and 49G reach all of 1-255, the
  rest through CHARS.
- 48GX CHARS (right-shift PRG): always opens on character 128 in pages of
  64 (four rows of 16); `-64`/`+64` (softkeys D, E) change page, the arrows
  move by 1 and 16, ECHO (F) inserts the character at the cursor and stays,
  ATTN returns to the command line with the text intact (observed).
- 49G CHARS (right-shift CAT): a 16-column grid of all 256 characters that
  opens on the character chosen last time (0 after a memory clear); the
  position is the byte at #818CF (observed, kept across uses); ECHO (F)
  inserts and stays, ATTN returns.

## Clearing inside EDIT

`EDIT` (48: left-shift +/-; 49G: ▼ with no command line) opens the object
with the cursor at 0. Deleting every character (DEL for those after the
cursor, backspace for those before) keeps the session: typing new text and
ENTER replaces the edited stack level, depth unchanged (observed on all
three, RPN mode on the 49G).

## Errors after ENTER

- A line that does not parse stays open and the ROM shows the message:
  "Invalid Syntax" in the header on the 48SX and 48GX, in a box with an OK
  key on the 49G (the active bit is clear while the box is shown).
- No error number is stored for a parse error (system RAM diffed on the
  48SX). A run-time error stores its number as 5 nibbles at #706FF
  (48SX), #8087D (48GX), #80F5C (49G) (`DROP` on an empty stack: #201);
  the 1991 list calls #706FF "Save Last Err#"
  (src: [[sources/hp48-internals-address-list]]).
- While the 48 shows a message in the header, bit 0 of the nibble at the
  address #7068B (48SX) or #80809 (48GX) is set; the next key clears it. On the 49G an error box
  makes #80EC8 (run-time error) or #80EC9 (parse error) read #F while
  the line's active bit is clear; ATTN closes the box and returns to the
  line.
- The message text is in RAM as string objects the ROM built to show
  it, one per line ("DROP Error:", "Too Few Arguments"; on the 49G the
  box's word-wrapped copy too): for "Invalid Syntax" at #700F2 on the
  48SX and #800F6 on the 48GX (a buffer that later display work
  overwrites), otherwise in temporary memory. Menu redraws build strings
  of their labels as well (observed).

## Key timing

Measured on the three ROMs by typing digit sequences with a given hold
time, waiting until the CPU sleeps in SHUTDN after each release, then a
gap, and reading the line back:

- Without waiting for SHUTDN between presses, keys are lost even with
  60 ms holds and 30 ms gaps.
- With the wait: holds of 15 ms lose keys on the 48SX; 25 ms holds were
  reliable on all three for 150-key random sequences.
- A repeated key needs a longer pause before its next press, since the ROM
  sees the release only at its next poll (1/16 s, see
  [[hardware/keyboard]]): the 49G loses repeats with gaps under 70 ms, the
  48SX with 30 ms; 80 ms was reliable on all three.
- Time per key (hold 25 ms, gap 20 ms, 80 ms before a repeat), mostly the
  ROM's own work after each key: about 250 ms on the 48SX, 175 ms on the
  48GX and 100 ms on the 49G with lines of 100-150 characters.
- With a command line open the ROM wakes every 1/16 s (asleep about
  55 ms, awake 8 ms, 48SX and 49G), so "asleep for 300 ms" never happens
  while a line is open; after ENTER the CPU stays awake while it
  evaluates.
- A key in the middle of a long 48GX line kept the ROM busy for more than
  5 s once (a garbage collection, presumably; not traced).

## Open

- Other ROM revisions are not checked.
- The 38G, 39G and 40G home command line is not covered.
