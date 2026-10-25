---
title: "HP 48S/SX keyboard: functions not printed on the keys, and the ON-key combinations"
type: rom-behaviour
models: [48sx]
status: draft
sources: ["[[sources/hp48sx-om]]", "[[sources/hp48-faq]]"]
tags: [keyboard, rom, self-test, server]
---

# HP 48S/SX keyboard: unprinted functions and ON combinations

What the 48S/SX keys do beyond their printed labels, and what ON does
held together with another key. The G/GX counterpart is
[[hardware/hp48gx-keyboard]]; the shift colours and the matrix are on
[[hardware/keyboard]]; the model is [[hardware/hp48sx]].

On the SX the orange left shift carries the application names (SOLVE,
PLOT, ALGEBRA, TIME, STAT, UNITS, I/O, MODES, MEMORY, LIBRARY, PRINT);
the blue right-shift slot above most of these keys is empty. The
emptiness is misleading: most of those right-shift positions do
something, and the Owner's Manual uses several of them, marking them
"(right shift)" in its first chapter.

## How the facts were obtained

- **Manual**: HP 48SX Owner's Manual, by book page
  ([[sources/hp48sx-om]]; pages checked against the scan).
- **FAQ**: the comp.sys.hp48 FAQ 4.62 by section ([[sources/hp48-faq]]).
- **Observed (saturnus, ROM J, 2026-10-25)**: a brute-force sweep on the
  clean-room saturnus emulator running the real 48SX ROM, revision J
  (`sxrom-j`, see [[hardware/hp48sx]]). 1323 key combinations, each from
  a fixed state: the stack holding 3, 2, 1 with the MTH menu showing; an
  open one-line and a multi-line command line; the interactive stack;
  and a "used" state (a previous command and menu). Each key unshifted,
  after each shift, after ALPHA and ALPHA plus each shift, with a shift
  or ON held down, and as a long press; then about 25 follow-up
  sequences. Results read from the emulator's memory and screen
  (black-box observation, no ROM code read). Not checked on a real 48SX.

Not covered: input forms of the applications, the EquationWriter, the
MatrixWriter, PICTURE, catalogs with entries, VAR menus with variables,
USER mode.

## Right shift on the application keys

The right-shift slot is empty on every key below. From the stack, no
command line (observed, saturnus, ROM J, 2026-10-25):

| Keys | Printed left-shift label | Right shift gives | Manual |
| --- | --- | --- | --- |
| → PRG | I/O | Kermit server: "Awaiting Server Cmd.", serial port opened | p. 33-9: "→ I/O or ← I/O SERVE" |
| → CST | MODES | MODES customization menu: ASN STOK RCLK DELK MENU CST | p. 15-5 |
| → VAR | MEMORY | MEMORY arithmetic menu: STO+ STO− STO× STO/ INCR DECR | p. 6-10 |
| → 7 | SOLVE | SOLVR menu of the current equation ("No Current Equation" error without one) | p. 1-32, 17-2 |
| → 8 | PLOT | PLOTR menu (ERASE DRAW AUTO XRNG YRNG INDEP) with the plot status | p. 1-33, 1-38 |
| → 9 | ALGEBRA | Equation Catalog (PLOTR SOLVR EQ+ EDIT →STK VIEW) | p. 17-2 |
| → 4 | TIME | Alarm Catalog (PURG EXECS EDIT →STK VIEW) | observed only |
| → 5 | STAT | statistics commands (TOT MEAN SDEV MAXΣ MINΣ BINS) with the data status | observed only |
| → 6 | UNITS | UNITS command menu: CONV UBASE UVAL UFACT →UNIT | p. 13-1 |
| → MTH | PRINT | nothing | |
| → ▲ | LIBRARY | nothing | |

The left-shifted keys give the printed menus: ← 7 the SOLVE menu
(SOLVR ROOT NEW EDEQ STEQ CAT), ← 8 PLOT, ← 9 ALGEBRA (COLCT EXPA ISOL
QUAD SHOW TAYLR), ← 4 TIME (SET ADJST ALRM ACK ACKA CAT), ← 5 STAT,
← 6 the UNITS catalog, ← PRG I/O (SEND RECV SERVE KGET FINIS SETUP),
← MTH PRINT, ← CST MODES, ← VAR MEMORY, ← ▲ LIBRARY (observed). The
manual describes each application as two menus, one per shift: SOLVE
and SOLVR (p. 17-2), the UNITS catalog and the UNITS command menu
(p. 13-1).

## Other unprinted functions

- **→ DEL** (printed left-shift label PURGE) types `CLVAR` into the
  command line, a typing aid for clearing all variables; ENTER runs it
  (src: [[sources/hp48sx-om]] p. 6-9; observed: with a command line open
  it inserts `CLVAR` at the cursor).
- **→ NXT** (printed left-shift label PREV) goes to the first page of
  the current menu (src: [[sources/hp48sx-om]] p. 2-12; observed with a
  three-page custom menu).
- **→ ▼** acts like → +/- (VISIT): the object named or numbered in
  level 1 opens in the editor; with too few levels it reports "Too Few
  Arguments" (observed, saturnus, ROM J, 2026-10-25). The printed ← ▼
  REVIEW lists the variables of the VAR menu with their contents
  (p. 6-7) or an application's status (p. 18-21); observed in the MTH
  menu, it lists the six labels in full (MATRX for MATR).
- **The "LAST" keys.** The skin shows STACK and CMD above 2 and 3, ARG
  and MENU in the right-shift slots; on the calculator these are LAST
  STACK, LAST CMD, LAST ARG and LAST MENU (src: [[sources/hp48sx-om]]
  p. 1-3 key index, p. 3-6). Observed: ← 2 restores the stack as it was
  before the last command line was run (5 6 + gives 11, ← 2 gives back
  5); → 2 returns the last arguments (6 and 5); ← 3 copies the last
  command line back into the editor; → 3 returns to the last menu, but
  not to MTH or PRG, which only list other menus.
- **In the interactive stack** the menu keys run ECHO, VIEW, PICK and
  the rest of the interactive stack menu; +/- edits the pointed level
  (observed).
- **Alpha shifts.** α ← gives lowercase letters and symbols, α → Greek
  letters and other characters; none of them are printed on the keys.
  Observed: α ← 1 types `==`, α ← 2 `<`, α ← 3 `≤`, α → 1 `≠`, α → 2 `>`,
  α → 3 `≥`, α ← DEL `!`, α ← ⬅ `?`, α → A to F `α ß Δ δ ε θ`, α → ON
  turns the calculator off, and α with either shift on 7, 8 or 9 does
  nothing (saturnus, ROM J, 2026-10-25).

## Printed labels that do something else, and keys with no cursor

- **Cursor keys with no command line** run other functions unshifted:
  ◀ GRAPH (the PICT screen), ▲ the interactive stack, ▼ edits level 1,
  ▶ SWAP, ⬅ DROP. DEL does nothing (on the GX it clears the stack)
  (observed, saturnus, ROM J, 2026-10-25).
- **In a command line**, → ◀ and → ▶ move the cursor to the start and
  end; in a multi-line command line → ▲ and → ▼ move to the top and the
  bottom line (observed). With a one-line command line → ▼ enters the
  line and acts as VISIT.
- **→ ⬅ "CLR"** clears the whole stack at once, with no confirmation;
  in a command line it discards the line too (observed).
- **→ ON** is OFF; the stack is kept (observed).

## ON-key combinations

ON is held down and the second key pressed and released before ON is
released. Observed (saturnus, ROM J, 2026-10-25), from the stack holding
3, 2, 1:

| Keys | Manual / FAQ | Observed (saturnus, ROM J) |
| --- | --- | --- |
| ON+A+F | Memory reset: "Try To Recover Memory?" (manual p. A-11; FAQ 4.6) | "Try To Recover Memory?" with YES and NO when A and F were held together with ON for a second or more; a half-second hold did nothing |
| ON+B | Cancels a pending ON sequence (FAQ 4.6) | Taken by the ON handler (an open command line stays), no visible effect |
| ON+C | System halt: empty stack display (manual p. A-11) | Stack cleared, variables kept, command line discarded |
| ON+D | Interactive tests; E then starts the keyboard test (manual p. A-13) | Test mode starts |
| ON+E | Continuous self-test, until a system halt (manual p. A-12) | Self-test starts |
| ON+MTH | Prints the display; the key's left-shift label is PRINT (manual p. 32-4) | Transmit annunciator lit |
| ON+1 | FAQ 4.6 gives ON+1 for the screen dump | Nothing: on the SX the dump is ON+MTH |
| ON+4 | Cancels repeating alarms (FAQ 4.6) | Taken by the ON handler, no visible effect with no alarms set |
| ON++, ON+− | Contrast darker, lighter (manual p. 1-4) | Contrast 11 to 12, and 11 to 10 |
| ON+SPC | "Coma" mode (FAQ 4.6) | Display off; the next ON comes back with the stack cleared, variables kept |
| ON+any other key | not listed | Acts as plain ON (ATTN) |

The FAQ's list is written for both series; its ON+1 matches the GX (see
[[hardware/hp48gx-keyboard]]), not the SX.

## Holding a shift

Holding ← or → down while pressing a key does the same as tapping the
shift first; a long press only adds auto-repeat on the cursor and
delete keys (observed, saturnus, ROM J, 2026-10-25). The manual says the
same for ALPHA: hold it while typing letters (p. 1-11).

## Open

- Nothing here was checked on a real 48SX.
- ON+B and ON+4 showed nothing; cancelling a slow ON sequence and a
  repeating alarm were not tried.
- The application screens' own keys (catalogs, the plotter, input via
  the EquationWriter) were not swept.
