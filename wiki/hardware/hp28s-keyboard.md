---
title: "HP 28S keyboard: functions not printed on the keys, and the ON-key combinations"
type: rom-behaviour
models: [28s]
status: draft
sources: ["[[sources/hp28s-om]]"]
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
catalogs, FORM, VISIT and EDIT, halted programs, USER mode.

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

## Open

- Nothing here was checked on a real 28S.
- Shift ▲ and shift ▼ in a multi-line command line were not tried.
- What the sweep's states do not reach (see above) was not swept.
