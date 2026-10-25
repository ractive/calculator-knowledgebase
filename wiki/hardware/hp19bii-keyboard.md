---
title: "HP 19BII keyboard: functions not printed on the keys, and the ON-key combinations"
type: rom-behaviour
models: [19bii]
status: draft
sources: ["[[sources/hp19bii-om]]", "[[sources/sebastian-selftests]]"]
tags: [keyboard, rom, self-test]
---

# HP 19BII keyboard: unprinted functions and ON combinations

What the 19BII keys do beyond their printed labels, and what ON does
held together with another key. The model, both keyboard halves with
their matrix codes and shifted labels, and the self-test are on
[[hardware/hp19bii]]; the Pioneer menu machines are on
[[hardware/hp27s-17b-17bii-keyboard]].

## How the facts were obtained

- **Manual**: the HP-19B Business Consultant II Owner's Manual, by book
  page ([[sources/hp19bii-om]]).
- **Observed (saturnus, 19BII ROM, 2026-10-25)**: a black-box key sweep
  on the clean-room saturnus emulator with the 19BII ROM on
  [[hardware/hp19bii]] (SHA-256
  25151cab88df3d419cc677c8b59744286298433fe042073c80885b00121fa83d),
  English chosen at the language prompt (the sixth menu key, labelled
  ENG; [[hardware/hp19bii]] "Cold start and language"). 576
  combinations: each of the 72 keys unshifted, after shift, with shift
  held, with ON held, and held 1.5 s, from MAIN with the message cleared;
  unshifted, after shift and with ON held from an open number entry
  ("12"). Follow-up sequences tried the multi-key chords and the keyboard
  test. Changes were detected by the display and the annunciators. Not
  checked on a real 19BII.

Not covered: the applications' menus, the equation and list editors
beyond their first screen, ALG versus RPN, printing to a printer.

## Unprinted functions

- **ON turns the calculator off as well as on.** Pressing ON while it is
  on turns it off (src: [[sources/hp19bii-om]] p. 20); the key has no
  shifted OFF label. Observed: ON alone and shift ON both turned the
  display off (saturnus, 19BII ROM, 2026-10-25).
- **A letter key at MAIN** (with the alpha annunciator off) shows "PRESS
  [EXIT], OR TYPE A MESSAGE & PRESS [PRNT]" and takes the letter: the
  start of a message for the infrared printer; EXIT drops it (src:
  [[sources/hp19bii-om]] p. 27). Observed for every letter key, ?, $, #,
  : and SPACE.
- **Shift on a letter key** types the shifted character printed above it
  (Ä, Ã, Ç, ;, °, { and so on) and starts the same message line
  (observed).
- **Shift on the menu keys** acts as the menu key alone; shift on STO,
  RCL, INS, DEL, ▲, ▼ and the digits did nothing from MAIN (observed).
- **Held keys.** SHOW (shift ·) shows the full number while held (p. 48);
  holding ▲ or ▼ wraps the history stack (p. 44); in a histogram,
  holding INPUT shows more about the cell under the pointer (p. 164).
  [[hardware/hp19bii]] notes that MEM's held display (p. 37) does not
  show in saturnus.

## ON-key combinations

ON is held down and the second key pressed before ON is released.

| Keys | Manual | Observed (saturnus, 19BII ROM, 2026-10-25) |
| --- | --- | --- |
| ON + + or − | Contrast darker, lighter (p. 20) | Contrast 22 to 23, and 22 to 21 |
| ON + PRNT | Prints the display, menu labels included, when both keys are released (p. 204) | Transmit annunciator lit |
| ON + third menu key | "MACHINE RESET" (p. 276) | "MACHINE RESET" over MAIN |
| ON + first + sixth menu key | Erase continuous memory: hold ON, hold the first, press the sixth; "MEMORY LOST" (p. 276) | "MEMORY LOST", both with the three keys pressed together and pressed one after another |
| ON + fourth menu key | The factory test ([[hardware/hp19bii]] "Self-test") | A display pattern of vertical lines |
| ON + fifth menu key | The self-test (p. 279) | Horizontal lines, then the test steps |
| ON + EXIT | not listed | "KEYBOARD TEST": a factory keyboard test; it took some keys and answered another with "1 FAIL", then showed "COPYRIGHT HP 1986, 1987" and returned to MAIN. The expected key order was not found |
| ON + second menu key | not listed | Nothing; the calculator stays on |
| ON + any other key | not listed | The calculator turns off (ON acts alone) |

## Leaving the tests

The manual: ON + third menu key halts the self-test with "MACHINE
RESET"; any other key halts it with a FAIL message caused by the wrong
key (p. 279). The footnote on the same page stops the factory test (ON
+ fourth menu key) the same way.

Observed (saturnus, 19BII ROM, 2026-10-25), each from MAIN with 77
stored in register 1:

| Test | Leaves it | Other keys |
| --- | --- | --- |
| Self-test, ON + fifth menu key | ON + third menu key: "MACHINE RESET" over 0.00; RCL 1 gives 77 | Patterns and "OK-19B II", repeating. A key, ON included, is noticed at some steps only: "1 FAIL"; the next key shows "COPYRIGHT HP 1986, 1987", the one after returns to MAIN with 0.00 and is not acted on. Register 1 was kept |
| Factory test, ON + fourth menu key | ON + third menu key, at any step, as above | Vertical lines; each key but ON advances one step (ON did nothing): display patterns, then "KEYBOARD TEST", the test ON + EXIT starts |
| Keyboard test, ON + EXIT | ON + third menu key, as above | Some keys are taken; ON or a wrong key gives "1 FAIL", then as for the self-test |

No test lost memory; every exit leaves 0.00 on the calculator line.

## Open

- Nothing here was checked on a real 19BII.
- The keyboard test's expected key order, and whether it is the test the
  manual's fourth-menu-key note means for the Pioneers.
- The applications' menus and editors were not swept.
