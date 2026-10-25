---
title: "HP 49G keyboard: held shifts, functions not printed on the keys, and the ON-key combinations"
type: rom-behaviour
models: [49g]
status: draft
sources: ["[[sources/hp49g-um]]", "[[sources/hp49g-faq]]"]
tags: [keyboard, rom, self-test, server]
---

# HP 49G keyboard: held shifts, unprinted functions and ON combinations

What the 49G keys do beyond their printed labels. Unlike the 48 series,
the 49G gives many keys a second meaning when the shift key is **held
down** while the key is pressed, instead of tapped before it. None of
these are printed. The 48 counterparts are [[hardware/hp48gx-keyboard]]
and [[hardware/hp48sx-keyboard]]; shift colours and the matrix are on
[[hardware/keyboard]]; the model is [[hardware/hp49g]].

## How the facts were obtained

- **Manual**: HP 49G User's Manual by page ([[sources/hp49g-um]]).
- **FAQ**: the HP 49G FAQ 1.10 by section ([[sources/hp49g-faq]],
  <https://www.hpcalc.org/hp49/docs/faq/>): 4.10 (plot keys in RPN
  mode), 4.14 (hidden key combinations), 5.8 (ON-D).
- **Observed (saturnus, ROM 2.10, 2026-10-25)**: a brute-force sweep on
  the clean-room saturnus emulator running hpcalc.org's 49G build of ROM
  2.10 (see [[hardware/hp49g]] "Flash chip and ROM images"). 1377 key
  combinations, each from a fixed state: RPN mode with the stack holding
  3, 2, 1 and the default stack menu (EDIT VIEW RCL STO PURGE CLEAR); an
  open one-line and a multi-line command line; the interactive stack;
  and algebraic mode with an empty stack. Each key unshifted, after each
  shift, after ALPHA and ALPHA plus each shift, with a shift or ON held
  down, and as a long press; then about 25 follow-up sequences. Default
  flags: CHOOSE boxes rather than soft menus (flag −117 clear). Results
  read from the emulator's memory and screen (black-box observation, no
  ROM code read). Not checked on a real 49G.

Not covered: other ROM versions (1.19-6, 2.15), soft-menu mode (flag
−117 set), the applications' own screens, the Equation Writer and
Matrix Writer, the File Manager, USER mode.

## Held shift: a second function per key

The FAQ says the 49G "has many undocumented key combinations involving a
held-down shift key" (src: [[sources/hp49g-faq]] 4.14). Observed, from
the RPN stack (saturnus, ROM 2.10, 2026-10-25):

| Keys | Tapped (shift, then key) | Shift held down while the key is pressed |
| --- | --- | --- |
| ← F1 to F6 (Y=, WIN, GRAPH, 2D/3D, TBLSET, TABLE) | the current soft menu's left-shift action; with the stack menu, nothing | the printed plot screen: Y= opens the PLOT FUNCTION list, WIN and 2D/3D and TBLSET their input forms, GRAPH and TABLE plot or tabulate ("No Current Equation" with none) |
| ← MODE (CUSTOM) | CUSTOM menu | MODES command menu: FORMAT ANGLE FLAG KEYS MENU MISC |
| → CAT (CHARS) | CHARS character table | CHARS command menu: SUB REPL POS SIZE NUM CHR |
| → 7 (NUM.SLV) | numeric solver choose box | SOLVE command menu as soft keys: ROOT DIFFE POLY SYS TVM |
| → 9 (TIME) | time application: browse alarms, set alarm, set time, tools | TIME command menu: DATE →DATE TIME →TIME TICKS ALRM |
| ← VAR (UPDIR) | up one directory | HOME |
| ← NXT (PREV) | previous menu page | last menu |
| ← TOOL (i) | types i, asking "Complex mode on?" with a real in level 1 | toggles complex mode (flag −103; the header shows C= instead of R=) |
| → ENTER (→NUM) | →NUM | toggles exact and approximate mode (flag −105) |
| → EQW (') | types `''` | types back quotes |
| → SPC (,) | types `,` | types `;` |
| → ▶ (no label) | XModem server: "Xmodem Server Waiting for command" | Kermit server: "Awaiting Server Cmd." |

The FAQ lists ← NXT, ← TOOL, ← VAR, → ENTER, → EQW, → ▶ (both
servers), → 9, ← ▼ and → ▼ among the held combinations (4.14), and
explains the plot keys: in algebraic mode a tapped shift and a function
key open the plot windows, but in RPN mode the shift is used for
storing into variables and unit conversions, so the shift must be held
down (4.10). Observed in algebraic mode: tapped ← F1 opens Y= and → ▶
types `XSERV()` into the command line instead of starting a server.

For the application keys this is the 49G's equivalent of the 48GX's
left-shifted command menus ([[hardware/hp48gx-keyboard]]): the tapped
key starts the application, the held key gives its command menu. Of
the keys swept only the four in the table above do it (← MODE,
→ CAT, → 7, → 9); the other application keys (← APPS FILES, ← 1 to ← 9,
→ 1 to → 9) gave the same with a tapped or a held shift.

## Other unprinted functions

The four cursor keys have no shift labels at all.

- **→ ▼ shows the current menu's labels in full**, one per line
  (observed; FAQ 4.14 "review key").
- **→ ▲** opens the interactive stack, like ▲ and HIST (observed).
- **← ▼** edits level 1 like ▼; FAQ 4.14 calls it the command-line
  editor (observed: the same editor as ▼ for a real number).
- **← ▶** is SWAP like ▶; ← ▲ and ← ◀ do nothing on the stack (observed).
- **→ F1** acts as F1; → F2 to → F6 did nothing with the stack menu
  (observed).
- **In a command line** ← ◀ / ← ▶ and → ◀ / → ▶ move the cursor to the
  start and end of the line, and so do → ▲ / → ▼ on a one-line command
  line; ← DEL deletes the character at the cursor (observed).
- **Alpha shifts** give lowercase and Greek letters and symbols, mostly
  not printed; FAQ 4.14 lists a few more with a held right shift
  (capital omega, inverted ! and ?, the degree sign).

## Printed labels that do something else, and keys with no cursor

- **Cursor keys with no command line**: ◀ shows the graphics screen, ▲
  the interactive stack, ▼ edits level 1, ▶ SWAP; ⬅ is DROP and ENTER
  DUP in RPN mode (observed; FAQ 4.14, 2.6).
- **→ ÷ "ARG"** on a real number asks "Complex mode on?" first
  (observed).
- **→ HIST "UNDO"** restores the stack before the last command line;
  after `1 2 3` from an empty stack it clears the stack (observed).
- **→ ON** is OFF.

## ON-key combinations

ON is held, the second key pressed and released, then ON released.
Observed (saturnus, ROM 2.10, 2026-10-25), from the RPN stack holding
3, 2, 1:

| Keys | Manual / FAQ | Observed (saturnus, ROM 2.10) |
| --- | --- | --- |
| ON+F1+F6 (A+F) | Memory reset (UM p. D-5) | "Try To Recover Memory?" with YES and NO |
| ON+F3 (C) | System halt: stack and history cleared, HOME (UM p. D-4) | Stack cleared, variables kept |
| ON+F4 (D) | Diagnostics; a following key picks a test (FAQ 5.8) | "Tests" menu: 1 Hard, 2 Cpu, 3 Kbd1, 4 Kbd2, 5 FastROM, 6 FastRAM, 7 FullROM, 8 FullRAM, 9 FROM Format, E RS232, Q Reboot |
| ON+F5 (E) | not listed | A "RAM Tests" screen; not followed further |
| ON+F6 (F) | not listed | Display test ("DISP"), looping |
| ON++, ON+− | Contrast (UM p. 2-3) | Contrast 14 to 15, and 14 to 13 |
| ON+▲ | not listed | Serial port opened, nothing visible |
| ON+F2 (B), ON+√x, ON+SIN, ON+9 | not listed | Taken by the ON handler: an open command line stays |
| ON+SPC | not listed | Plain ON (the 48's "coma" combination does nothing here) |
| ON+any other key | not listed | Plain ON (CANCEL) |
| ⬅ held during a warm start | Skips the libraries' configuration (UM p. D-5; FAQ 6.1) | not tried |

The 49G's matrix differs from the 48's, so the softkeys F1-F6 take the
roles of the 48's A-F in these chords.

## Open

- Nothing here was checked on a real 49G.
- ON+▲ opened the serial port; whether it sends a screen dump was not
  checked.
- ON+F5 and ON+F6 run test sequences that were not followed through.
- Held-shift behaviour on ROM 1.19-6 and 2.15, and with flag −117 set,
  was not swept; the FAQ dates some combinations to ROMs after 1.10 and
  1.18 (4.14).
