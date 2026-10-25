---
title: "HP 38G, 39G and 40G keyboards: functions not printed on the keys, and the ON-key combinations"
type: rom-behaviour
models: [38g, 39g, 40g]
status: draft
sources: ["[[sources/hp38g-ug]]", "[[sources/hp39g40g-ug]]", "[[sources/sebastian-selftests]]"]
tags: [keyboard, rom, self-test]
---

# HP 38G, 39G and 40G keyboards: unprinted functions and ON combinations

What the keys of the aplet calculators do beyond their printed labels, and
what ON does held together with another key. The models and their
keyboard matrices are on [[hardware/hp38g]] and [[hardware/hp39g-40g]];
the 48GX equivalent of this page is [[hardware/hp48gx-keyboard]].

## How the facts were obtained

- **Manuals**: the HP 38G User's Guide ([[sources/hp38g-ug]]) and the HP
  39G/40G User's Guide ([[sources/hp39g40g-ug]]), by printed page.
- **Observed (saturnus, 2026-10-25)**: brute-force sweeps on the
  clean-room saturnus emulator running the real ROMs: the 38G ROM A1.67
  (`38G_A167.ROM`, 705 key combinations) and the 39G/40G ROM of 2000-11-19
  (`rom.39g`, 765 combinations on each model). Starting states: HOME with
  an empty edit line, HOME with `12` on the edit line, HOME with one
  history entry (1+2 = 3), and the Aplet Library. Each key was tried
  unshifted and after SHIFT in every state; from the empty HOME also after
  ALPHA, after ALPHA then SHIFT, with SHIFT, ALPHA or ON held down, and as
  a 1.5 s press. Changes were detected from the display image and the
  annunciators, and named by looking at the screens. About 25 follow-up
  sequences per ROM checked the ON chords and the shifted cursor keys.
  This is the ROM on an emulator, not checked on a real calculator.

Not covered: the aplet views other than the setup screens, input forms
and choose boxes beyond the Aplet Library, the MATH menu, the program and
note editors, the matrix and list editors, the 40G's CAS.

## Unprinted functions

- **SHIFT PLOT, SHIFT SYMB, SHIFT NUM** open the setup views. The keys
  carry no shifted label of their own: one SETUP bracket spans the three
  keys, and the guides write the functions SETUP-PLOT, SETUP-SYMB and
  SETUP-NUM (src: [[sources/hp38g-ug]] p. 1-13; [[sources/hp39g40g-ug]]
  p. 8-3). Observed in the Function aplet: on the 38G all three open
  (FUNCTION PLOT SETUP, FUNCTION SYMBOLIC SETUP with the angle measure,
  FUNCTION NUMERIC SETUP); on the 39G and 40G SHIFT PLOT and SHIFT NUM
  open, SHIFT SYMB does nothing, as the Function aplet there has no
  symbolic setup (saturnus, 2026-10-25). The 39G guide uses SETUP-SYMB
  for the Statistics aplet's fit (p. 8-3).
- **SHIFT ▲ and SHIFT ▼ in a list** go to its beginning and its end (src:
  [[sources/hp38g-ug]] p. 1-11; [[sources/hp39g40g-ug]] p. 1-8).
  Observed: in HOME, SHIFT ▲ highlights the oldest history entry and
  SHIFT ▼ then returns to the edit line (saturnus, 38G A1.67 and 39G,
  2026-10-25); in the 38G's Aplet Library SHIFT ▼ highlights the last
  aplet (observed).
- **SHIFT ◀ and SHIFT ▶ on the edit line** move the cursor to its start
  and its end (observed, saturnus, 38G and 39G, 2026-10-25). SHIFT ◀ in
  the history does nothing.
- **SHIFT on a menu key** does nothing (observed).
- **Holding ALPHA** types letters for as long as it is held (src:
  [[sources/hp38g-ug]] p. 1-2; [[sources/hp39g40g-ug]] p. 1-6). Observed:
  held ALPHA gives the same letters as ALPHA pressed first; ALPHA then
  SHIFT then a letter key gives the lowercase letter, as SHIFT ALPHA does
  (saturnus, 2026-10-25).
- **Long press** repeats the typing keys (observed). Holding SHIFT while
  pressing a key does the same as pressing it first (observed).
- **The 40G's sixth HOME menu key** is CAS, shown on the screen only; on
  the 39G that key is blank in HOME. Everywhere else the 39G and 40G gave
  the same result for every combination (observed, saturnus, 2026-10-25).

## Printed labels that do something else

- **ON is also CANCEL.** The 38G guide names the key ON/CANCEL: with the
  calculator on, it cancels the current operation (src:
  [[sources/hp38g-ug]] p. 1-1); the 39G guide calls it CANCEL when it
  clears the edit line (src: [[sources/hp39g40g-ug]] p. 1-2). The key
  itself is printed ON, with OFF above it.

## ON-key combinations

ON is held, the other keys pressed, then ON released. Menu keys are
numbered 1 to 6 from the left. Both guides document only the resets
(src: [[sources/hp38g-ug]] p. 9-8; [[sources/hp39g40g-ug]] R-4, R-5);
the rest was found by the sweep.

38G (observed, saturnus, ROM A1.67, 2026-10-25). The keys sit at the
48's matrix positions ([[hardware/hp38g]] "Keyboard"), and every chord
acts as the same position does on the 48GX ([[hardware/hp48gx-keyboard]]),
which supports that inference:

| Keys | 38G guide | Observed | Same position on the 48 |
| --- | --- | --- | --- |
| ON + menu key 3 | reset | HOME, history cleared | ON+C |
| ON + menu keys 1 and 6 | erase all memory | "Memory Clear" box | ON+A+F |
| the same, keys 1 and 6 released, menu key 3 pressed, then ON released | cancels the erase | no Memory Clear; history cleared as by the reset | |
| ON + menu key 4 | not listed | display test pattern (vertical lines), stays after ON | ON+D, interactive self-test |
| ON + menu key 5 | not listed | horizontal stripe patterns, repeating | ON+E, looping self-test |
| ON + 1 | not listed | serial port switched on, nothing visible | ON+1, screen dump |
| ON + , | not listed | display off; the next ON brings HOME back with the history cleared | ON+SPC, "coma" |
| ON + +, ON + − | not listed | contrast 14 to 15, and 14 to 13 | ON++, ON+− |
| ON + any other key | not listed | nothing visible | |

39G and 40G (observed, saturnus, `rom.39g`, 2026-10-25; the 40G gave the
same results):

| Keys | 39G/40G guide | Observed |
| --- | --- | --- |
| ON + menu key 3 | reset (R-4) | HOME, history cleared |
| ON + menu keys 1 and 6 | erase all memory (R-5) | "Memory Clear" box |
| the same, keys 1 and 6 released, then menu key 3 | cancels the erase (R-5) | no Memory Clear; history cleared as by the reset |
| ON + menu key 4 | not listed | a "Tests" menu: 1 Hard, 2 Cpu, 3 Kbd1, 4 Kbd2, 5 FullRAM, 6 RS232, 7 IR, ON Reboot; 3 starts "Kbd1"; ON reboots to HOME with the history cleared |
| ON + menu key 5 | not listed | "RAM Tests" (RAM0 and RAM1 "No open pins"), then display patterns, repeating |
| ON + menu key 6 | not listed | display patterns, then "Kbd2", which shows a code for each key pressed |
| ON + 1 | not listed | serial port switched on, nothing visible |
| ON + +, ON + − | contrast up, down (p. 1-2) | contrast 12 to 13, and 12 to 11 |
| ON + any other key | not listed | nothing visible; ON + (−), at the 49G's SPC position, does not turn the display off |

## Leaving the tests

Neither guide documents the tests. Sebastian's page says the 38G's
tests work as on the 48, left with ON + menu key 3 (the 48's ON+C);
the 39G's Tests menu is left with ON, which reboots, or ON + menu key 3,
and its continuous and quick tests with ON + menu key 3. It warns that
the tests may clear memory (src: [[sources/sebastian-selftests]]). The
48 FAQ gives ON+C, a warm start, as the way out of the 48's interactive
test (src: [[sources/hp48-faq]] 4.7).

Observed (saturnus, 2026-10-25), each from HOME with 77 stored in A:

**38G** (ROM A1.67):

| Test | Leaves it | Other keys |
| --- | --- | --- |
| ON + menu key 4: test mode, three vertical lines | ON + menu key 3: HOME, history cleared, A still 77 | ON alone does nothing. Menu keys 1 to 6 run single tests as on the 48: 1 speed ("SPD"), 2 display patterns (ENTER steps them), 3 "IROM OK", 4 "IRAM OK", 5 "KBD1", 6 "KBD2"; a wrong key in KBD1 or KBD2 shows a code. Each returns to test mode |
| ON + menu key 5: continuous test | ON + menu key 3, as above | Patterns, IRAM, "OK-38", speed, IROM, repeating. ON did nothing; other keys let it run on, then it showed "KBD 00013" and stayed in test mode |

**39G and 40G** (`rom.39g`; the 40G differs only in its CAS label and
in naming itself HP40G):

| Test | Leaves it | Other keys |
| --- | --- | --- |
| ON + menu key 4: the Tests menu | ON ("Reboot") or ON + menu key 3: HOME, history cleared, A still 77 | 1 Hard and 2 Cpu showed nothing in saturnus. 3 Kbd1 and 4 Kbd2 wait for the keys in order and show a hex code for a wrong key, ON included, so ON does not leave them; Kbd2 done in Sebastian's order returns to the menu; ON + menu key 3 leaves either. 5 FullRAM runs about 5 s and returns to the menu; A was still 77. 6 RS232 and 7 IR ask to open, then close the loop, "And Press any key"; without a loop plug they report an error, and keys step them back to the menu. Menu key 1, not listed, opens a hex memory dump |
| ON + menu key 5: continuous test | ON + menu key 3, held: HOME, history cleared, A still 77 | RAM tests ("No open pins", "RAM0 OK"), display patterns, speed, repeating; ON and other keys did not stop it |
| ON + menu key 6: quick test | ON + menu key 3, held, as above | Patterns, then Kbd2; done in order it goes on to the RS232 and IR steps, keys stepping them; ON counts as a wrong key in Kbd2 |

How long menu key 3 is held matters in the continuous and quick tests:
held 2 s it left at every one of eight starting times tried (1 to 20 s
into the test); held 0.4 s it was sometimes taken as a key press (the
test showed "HP39/40 Error" and went on), and where it did leave, the
history held two lines of garbage characters; held 1 s it failed once in
eight. A was kept in every case.

In saturnus each continuous pass ends with "HP39/40 Error", where
Sebastian reports "HP 39/40 OK", and the ERAM step shows "Write on ERAM
ERROR, Calculator is HP39G": probably an emulator gap, not checked.

## Open

- Nothing here was checked on a real 38G, 39G or 40G.
- Whether "Write on ERAM ERROR" and "HP39/40 Error" in the 39G tests
  are saturnus faults, and why Hard and Cpu show nothing.
- Whether ON + 1 sends the screen anywhere (serial or IR) was not
  checked; the test screens were not explored item by item.
- The states listed under "Not covered" were not swept.
