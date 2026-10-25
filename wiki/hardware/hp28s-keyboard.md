---
title: "HP 28S keyboard: functions not printed on the keys, and the ON-key combinations"
type: rom-behaviour
models: [28s]
status: draft
sources: ["[[sources/hp28s-om]]", "[[sources/sebastian-selftests]]"]
tags: [keyboard, rom, self-test]
---

# HP 28S keyboard: unprinted functions and ON combinations

What the 28S keys do beyond their printed labels, and what ON does held
together with another key. The model, its two keyboards, their matrix
codes and the documented ON chords are on [[hardware/hp28s]]; the 48GX
equivalent of this page is [[hardware/hp48gx-keyboard]].

## How the facts were obtained

- **Manual**: the HP-28S Owner's Manual, by printed page
  ([[sources/hp28s-om]]).
- **Observed (saturnus, ROM 2BB, 2026-10-25)**: a brute-force sweep on the
  clean-room saturnus emulator running the real 28S ROM, revision 2BB
  (SHA-256 on [[hardware/hp28s]] "ROM image"). 720 key combinations from
  two starting states: the stack holding 1, 2, 3 with no menu showing, and
  an open command line `45 67` with the cursor at its end. Each key was
  tried unshifted, after the shift key, with the shift key held down, after
  α, with ON held down (stack only) and as a 1.5 s press. The 28S memory is
  not read by saturnus' probe: changes were detected from the display image
  and the annunciators only, and named by looking at the screens. 21
  follow-up sequences checked Menu Lock, the cursor menu and the ON chords.
  This is the ROM on an emulator, not checked on a real calculator.

Not covered: menus other than TRIG, the PLOT and SOLV environments,
catalogs, FORM, VISIT and EDIT, halted programs, USER mode. Which menu
and page shows can be read from RAM: [[hardware/hp28s]] "Menu in RAM".

## Unprinted functions

- **Shifted menu keys in a command line.** With a command line and no
  menu labels showing, the six menu keys are the cursor menu: INS, DEL,
  ▲, ▼, ◀ and ▶, printed in white above the keys. Their shifted functions
  have no label: shift INS deletes everything left of the cursor, shift
  DEL deletes the character at the cursor and everything to its right,
  shift ▲ and shift ▼ go to the top and bottom row, shift ◀ and shift ▶ to
  the left and right end (src: [[sources/hp28s-om]] p. 166-168, item 16
  on p. 30). Observed: all four tried (delete left, delete right, left
  end, right end) behave so; ✥ from the TRIG menu brings the cursor menu
  back (saturnus, ROM 2BB, 2026-10-25).
- **The menu keys with no command line** and no menu showing do nothing,
  shifted or not (observed, saturnus, ROM 2BB, 2026-10-25).
- **Long press.** The unshifted cursor menu keys except INS repeat while
  held (src: [[sources/hp28s-om]] p. 167). Observed: the typing keys
  (letters, digits, delimiters, EEX) repeat too (saturnus, ROM 2BB,
  2026-10-25).
- **Holding the shift key** while pressing a key does the same as tapping
  it first (observed, saturnus, ROM 2BB, 2026-10-25).

## Printed labels that do something unexpected, and empty slots

- **Shift α, printed MENUS, is Menu Lock**, a toggle, not a list of menus.
  While it is on, the shifted and unshifted positions of the letter keys A
  to R swap: A selects the ARRAY menu and shift A types A. Shift α again
  turns it off (src: [[sources/hp28s-om]] p. 169, 192). The manual's
  troubleshooting answer for "the keys from A to R don't work" is that
  Menu Lock was selected by accident (p. 284). Observed: shift α then A
  shows the ARRAY menu (→ARRY ARRY→ PUT GET PUTI GETI), shift α shift A
  types A, and no annunciator shows that Menu Lock is on (saturnus, ROM
  2BB, 2026-10-25).
- **Shift P** has no label and did nothing, from the stack, from a command
  line and from the TRIG menu (observed, saturnus, ROM 2BB, 2026-10-25).
- **⬅ and ✥ with no command line** do nothing (observed); the 28S has a
  separate DROP key, unlike the 48 where ⬅ drops.
- **Shift SPACE (NEWLINE)** does nothing without a command line (observed).

## ON-key combinations

ON is held, the other key pressed, then ON released. The documented chords
and their manual pages are on [[hardware/hp28s]] "ON chords"; all of them
were tried (observed, saturnus, ROM 2BB, 2026-10-25; menu keys named by
their white cursor labels):

| Keys | Manual | Observed (saturnus, ROM 2BB) |
| --- | --- | --- |
| ON+L | print the display (p. 216) | transmit annunciator lit |
| ON++, ON+− | contrast darker, lighter (p. 21, 216) | contrast 22 to 23, and 22 to 21 |
| ON+▲ | System Halt (p. 217) | stack cleared |
| ON+INS+▶ | Memory Reset (p. 20, 217) | "Memory Lost" over an empty stack |
| ON+INS+▶, then DEL before ON is released | cancelled (p. 215) | stack kept |
| ON+▲, then DEL before ON is released | cancelled (p. 215) | stack kept |
| ON+◀ | repeating self-test (p. 218) | test patterns on the display, every annunciator lit |
| ON+NEXT | keyboard test (p. 219) | "KEYBOARD TEST" |
| ON+▼ | factory test waiting for keys (p. 291) | vertical line pattern, busy annunciator |
| ON+DEL alone | cancel a pending operation (p. 215) | nothing visible |
| ON+any other key | not listed | nothing visible |

## Leaving the tests

The manual: both tests perform a System Halt (p. 218), so the stack is
cleared and variables are kept (p. 217). The repeating test (ON+◀) is
left with System Halt, ON+▲; a key pressed during it gives a failure
message that means nothing (p. 218, 290-291). The keyboard test
(ON+NEXT) ends with OK-28S or a FAIL message, then ON (p. 219, step 4).
The factory test (ON+▼) waits for keys and is quit with System Halt
(p. 291). Sebastian's page gives ON+▲ for all three and says the
factory test steps on any key until KEYBOARD TEST (src:
[[sources/sebastian-selftests]]).

Observed (saturnus, ROM 2BB, 2026-10-25), each from the stack holding
77 with a variable X = 5:

| Test | Leaves it | Other keys |
| --- | --- | --- |
| Repeating, ON+◀ | ON+▲: the stack, empty; X still 5 | ON alone did nothing within 6 s. Another key (1, ENTER) stopped it with "1 FAIL"; the next key, ON included, returned to the empty stack and was not acted on. Left alone, a pass took about 30 s and ended in OK-28S, then the test started again |
| Keyboard, ON+NEXT | ON+▲, as above | ON or a key out of order gives "1 FAIL"; the next key returns to the empty stack and is swallowed |
| Factory, ON+▼ | ON+▲, as above | Every key, ON included, advances one step: eight display patterns, then KEYBOARD TEST, from where it acts as the keyboard test |

No test cleared variables; every exit left the stack empty, as System
Halt does. In the keyboard test saturnus accepted A to J in order and
answered K with "1 FAIL" at every speed tried, although K types K
outside the test, so the full order was not completed.

## Open

- Nothing here was checked on a real 28S.
- Why the keyboard test rejects K in saturnus (an emulator fault, or an
  order other than A to F, G to L).
- Shift ▲ and shift ▼ in a multi-line command line were not tried.
- What the sweep's states do not reach (see above) was not swept.
