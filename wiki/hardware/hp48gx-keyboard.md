---
title: "HP 48G/GX keyboard: functions not printed on the keys, and the ON-key combinations"
type: rom-behaviour
models: [48gx]
status: draft
sources: ["[[sources/hp48g-ug]]", "[[sources/hp48-faq]]", "[[sources/hpmuseum-forum]]"]
tags: [keyboard, rom, self-test, server]
---

# HP 48G/GX keyboard: unprinted functions and ON combinations

What the 48G/GX keys do beyond their printed labels, and what ON does
held together with another key. Which shift owns which printed label,
and the keyboard matrix, are on [[hardware/keyboard]]; the model is
[[hardware/hp48gx]]; what the keys type into the command line is on
[[hardware/command-line]].

HP never printed a list of these. Users on the HP Museum forum (2018)
could not find one in the User's Guide or the AUR; the closest is the
User's Guide Appendix G, which gives the keys for every operation but
is not organised by key (src: [[sources/hpmuseum-forum]] F7). The 48 FAQ
lists the ON-key combinations (src: [[sources/hp48-faq]] 4.6) and two
G/GX keys it calls undocumented (4.19).

## How the facts were obtained

Three kinds of source, marked on each fact:

- **Manual**: the HP 48G Series User's Guide, 8th edition, by book page
  ([[sources/hp48g-ug]]).
- **FAQ / forum**: the comp.sys.hp48 FAQ 4.62 by section
  ([[sources/hp48-faq]]) and an HP Museum forum thread
  ([[sources/hpmuseum-forum]] F7).
- **Observed (saturnus, ROM R, 2026-10-25)**: a brute-force sweep on the
  clean-room saturnus emulator running the real 48GX ROM, revision R
  (`gxrom-r`, see [[hardware/hp48gx]] for its SHA-256). 1274 key
  combinations were pressed, each from a fixed starting state: the stack
  holding 3, 2, 1 with the MTH menu showing; an open one-line and a
  multi-line command line; the interactive stack; and a "used" state.
  Each key was tried unshifted, after each shift, after ALPHA and ALPHA
  plus each shift, with a shift or ON held down, and as a long press.
  The result was read from the emulator's memory and screen (black-box
  observation only). This is the ROM on an emulator, not checked on a
  real calculator.

Not covered by the sweep: input forms, the equation and matrix writers,
the PICTURE environment, choose boxes, VAR menus holding variables, and
USER mode.

## Left shift on the application keys

The twelve application labels are printed in green alone (right shift).
The guide says each application's left-shifted key gives that
application's command menu, without listing them all (src:
[[sources/hp48g-ug]] p. 1-7); Appendix G uses these unprinted keys in its
key paths, for example ← I/O SRVR SERVE for SERVER (p. G-42) and ← MODES
MENU for MENU (p. G-26).

Observed (saturnus, ROM R, 2026-10-25), from the stack with no command
line:

| Keys | Printed right-shift label | Left shift gives |
| --- | --- | --- |
| ← PRG | CHARS | CHARS command menu: SUB REPL POS SIZE NUM CHR |
| ← CST | MODES | MODES menu: FMT ANGL FLAG KEYS MENU MISC |
| ← VAR | MEMORY | MEMORY command menu |
| ← ▲ | STACK | STACK command menu |
| ← 7 | SOLVE | SOLVE command menu |
| ← 8 | PLOT | PLOT command menu |
| ← 9 | SYMBOLIC | SYMBOLIC command menu |
| ← 4 | TIME | TIME command menu |
| ← 5 | STAT | STAT command menu |
| ← 6 | UNITS | UNITS command menu: CONV UBASE UVAL UFACT →UNIT |
| ← 1 | I/O | I/O command menu: SRVR IOPAR PRINT PR1 SEND RECV |
| ← 2 | LIBRARY | LIBRARY command menu: PORTS PVARS LIBS MERG FREE1 DETAC |
| ← 3 | EQ LIB | EQ LIB command menu |

The right-shifted keys start the applications themselves; → 6 opens the
units catalog and → 2 a menu of the attached libraries (observed). The
FAQ puts it the same way for the digit keys: left shift gives softkey
menus as on the S/SX, right shift the new full-screen versions (src:
[[sources/hp48-faq]] 10.1).

## Other unprinted functions

- **→ ▶ with no command line starts the Kermit server.** Observed: the
  screen shows "Awaiting Server Cmd.", the serial port is opened and the
  busy annunciator lit (saturnus, ROM R, 2026-10-25). The FAQ lists it
  as missing from the User's Guide; it is given in the AUR's SERVER
  entry (src: [[sources/hp48-faq]] 4.19). Appendix G gives only
  ← I/O SRVR SERVE (src: [[sources/hp48g-ug]] p. G-42). A forum post of
  2025 also mentions it (src: [[sources/hpmuseum-forum]] F7). Server
  behaviour: [[protocols/server-commands]].
- **→ ▼ (right shift on the key labelled VIEW) shows the current menu's
  labels in full.** Appendix G lists it as "→VIEW": it displays the menu
  keywords of the current menu, with their values if they are variables
  (src: [[sources/hp48g-ug]] p. G-51, pointing to p. 3-7); the FAQ calls
  it "review menu keys" (src: [[sources/hp48-faq]] 4.19). Observed: the
  six labels appear as a full-screen list with longer names where the
  menu key is abbreviated (MATRX for MATR in the MTH menu); the next key
  clears the list and then acts as usual (saturnus, ROM R, 2026-10-25).
- **CHARS** (→ PRG) is a character browser that shows each character's
  NUM code and its keyboard sequence, if it has one (src:
  [[sources/hp48-faq]] 10.1; [[sources/hp48g-ug]] p. 1-5 mentions the
  application). Details: [[hardware/command-line]] "CHARS".
- **→ VAR** opens the Variable Browser, a memory manager with marking,
  copy, move and purge (src: [[sources/hp48-faq]] 10.1); the guide calls
  it the MEMORY application (p. 1-6).
- **In the interactive stack**, +/- and → +/- act as VIEW and → VIEW
  (observed, saturnus, ROM R, 2026-10-25).
- **Alpha shifts.** α ← and α → give lowercase letters, Greek letters
  and other symbols that are mostly not printed; the guide shows them in
  its alpha keyboard illustration (src: [[sources/hp48g-ug]] p. 2-2,
  2-3). Observed: α ← 1 types `==`, α → 1 types `≠`, α → ON turns the
  calculator off, and α ← or α → on 7, 8 or 9 does nothing (saturnus,
  ROM R, 2026-10-25).

## Printed labels that do something else, and keys with no cursor

- **→ EEX "ARG"** runs LASTARG (the arguments of the last command), not
  the ARG function (polar angle) (src: [[sources/hp48g-ug]] p. 3-5;
  Appendix G lists LASTARG, with the page, on p. G-23, and ARG as a
  separate entry under A).
- **→ NXT "MENU"** returns to the last menu and its page. Menus that only
  list other menus, such as MTH and PRG, are not remembered as the last
  menu (src: [[sources/hp48g-ug]] p. 1-10).
- **Cursor keys with no cursor showing** run their shifted labels
  unshifted: ◀ PICTURE, ▶ SWAP, ▲ the interactive stack, ▼ VIEW, DEL
  CLEAR, ⬅ DROP (src: [[sources/hp48g-ug]] p. 1-8). Observed: DEL clears
  the stack at once, with no confirmation; ▼ on a real number in level 1
  opens it in the editor (saturnus, ROM R, 2026-10-25).
- **Right shift on ◀, DEL and ⬅ with no command line** does nothing to
  the stack (observed, saturnus, ROM R, 2026-10-25). These keys have no
  right-shift label; their right-shift functions belong to the editor.
- **In a command line**, right shift with ◀ ▶ ▲ ▼ moves the cursor to the
  start, the end, the top and the bottom; → DEL deletes to the end and
  → ⬅ to the start (src: [[sources/hp48g-ug]] p. 1-7). Observed with a
  one-line command line: ← ▼ does nothing and ← ▲ opens the STACK
  command menu (saturnus, ROM R, 2026-10-25).
- **→ ON** is OFF; the stack is kept (observed, saturnus, ROM R,
  2026-10-25).

## ON-key combinations

ON is held down and the second key pressed before ON is released. The
FAQ's list (src: [[sources/hp48-faq]] 4.6) and what the sweep saw
(observed, saturnus, ROM R, 2026-10-25; one key at a time with ON held,
from the stack holding 3, 2, 1, plus follow-up runs):

| Keys | FAQ 4.6 | Observed (saturnus, ROM R) |
| --- | --- | --- |
| ON+A+F | Cold start, offers "Try to recover memory?" | "Try To Recover Memory?" with YES and NO, in every run that held A and F down together with ON; ON with A alone or F alone acts as plain ON |
| ON+B | Cancels a pending ON sequence | Taken by the ON handler (an open command line stays), no visible effect |
| ON+C | Warm start: stack and PICT lost, memory kept | System halt: stack cleared, variables kept |
| ON+D | Interactive self-test | Interactive self-test starts |
| ON+E | Looping self-test | Continuous self-test starts |
| ON+1 | Screen dump | Prints the display (transmit annunciator lit) |
| ON+4 | Cancels repeating alarms | Taken by the ON handler (an open command line stays), no visible effect with no alarms set |
| ON++, ON+− | Contrast up, down | Contrast 14 to 15, and 14 to 13 |
| ON+SPC | "Coma": clocks stopped, warm-start log cleared, stack lost on wake-up | Display off; the next ON comes back through a system halt, stack cleared, variables kept |
| ON+any other key | not listed | Acts as plain ON (CANCEL) |

ON+SPC differs from → ON (OFF), which keeps the stack. The self-test
menus and their serial output are on [[hardware/uart]] (src:
[[sources/hp48-faq]] 4.7).

## Holding a shift

Holding ← or → down while pressing a key does the same as tapping the
shift first; a long press of the key only adds auto-repeat (observed,
saturnus, ROM R, 2026-10-25). The guide says the same for ALPHA: hold it
while typing, or press it first (src: [[sources/hp48g-ug]] p. 2-3).

## Open

- Nothing here was checked on a real 48GX.
- ON+B and ON+4 did nothing visible in the sweep; whether ON+B cancels
  a slow ON sequence (FAQ 4.6) and ON+4 a repeating alarm was not tried.
- Input forms, the equation and matrix writers, PICTURE, choose boxes,
  VAR menus with variables and USER mode were not swept.
