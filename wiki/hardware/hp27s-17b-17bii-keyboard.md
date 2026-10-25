---
title: "HP 27S, 17B and 17BII keyboards: functions not printed on the keys, and the CLR-key combinations"
type: rom-behaviour
models: [27s, 17b, 17bii]
status: draft
sources: ["[[sources/hp27s-om]]", "[[sources/hp17bii-om]]", "[[sources/hp17b-om]]"]
tags: [keyboard, rom, self-test]
---

# HP 27S, 17B and 17BII keyboards: unprinted functions and CLR combinations

What the keys of the three Pioneer menu machines do beyond their printed
labels, and what the CLR key (the bottom-left key, on the ON line; ON
when the calculator is off) does held together with another key. The
models, their key labels and the chord tables from the manuals are on
[[hardware/hp27s]] and [[hardware/hp17bii]] (which covers the 17B too);
the 42S, on the same board, is [[hardware/hp42s-keyboard]]; the 48G/GX
counterpart is [[hardware/hp48gx-keyboard]].

## How the facts were obtained

- **Manuals**: the HP-27S Owner's Manual ([[sources/hp27s-om]]), the
  HP 17BII Owner's Manual ([[sources/hp17bii-om]]) and the HP-17B
  Owner's Manual ([[sources/hp17b-om]]), by book page.
- **Observed (saturnus, 2026-10-25)**: a black-box key sweep on the
  clean-room saturnus emulator, with the 27S ROM (revision B) and the
  international 17BII and 17B ROMs whose SHA-256 are on
  [[hardware/hp27s]] and [[hardware/hp17bii]] "ROM images seen"
  (English chosen at the language prompt). 296 combinations per model:
  each of the 37 keys unshifted, after shift, with shift held, with CLR
  held, and held 1.5 s, from MAIN with the message cleared; unshifted,
  after shift and with CLR held from an open number entry ("12"). Ten
  follow-up sequences per model tried the multi-key chords. Changes were
  detected by the display and the annunciators. Not checked on real
  calculators.

Not covered: the applications' own menus (TVM, CFLO, SUM, SOLVE, TIME,
STAT and the rest), ALG versus RPN mode on the 17BII, the equation and
list editors, typing letters, printing.

## The menu keys with no menu (27S)

The 27S prints a shifted function above each of its six menu keys (√x,
x², eˣ, LN, yˣ, 1/x). When no menu labels are showing, the menu keys run
those functions without the shift (src: [[sources/hp27s-om]] p. 22).
Observed: from MAIN the third menu key turned 0 into 1 (eˣ), as shift
and the same key did; from an open entry of 12 each menu key gave the
same result unshifted and shifted (saturnus, 27S ROM, 2026-10-25).
These keys' caps carry only a printed ^ pointing at the display.

The 17B and 17BII print nothing above their menu keys. Shift then a
menu key acts like the menu key alone (observed, both models).

## Keys with no shifted label

Observed (saturnus, 2026-10-25):

- **⬅, ▲, ▼** (no shifted label on any of the three): shift then the key
  acts as the key alone. ⬅ deletes the last digit typed; ▲ and ▼ roll
  the history stack, and with a number being typed they end the entry.
  Holding ▲ or ▼ makes the history stack wrap round (src:
  [[sources/hp17bii-om]] p. 40; [[sources/hp17b-om]] p. 40).
- **STO, the digits 1 to 9 (17B and 17BII), and on the 17B also RCL, (
  and )**: shift then the key does nothing; the shift is dropped. The
  17BII prints RND over RCL and R↓, x≷y over ( and ) for its RPN mode.

## Held keys

- **SHOW** (shift ·) shows the number in full precision for as long as
  the key is held (src: [[sources/hp27s-om]] p. 34;
  [[sources/hp17bii-om]] p. 31; [[sources/hp17b-om]] p. 31).
- In the Solver, holding the unknown's menu key until the numbers stop
  changing shows the last two estimates (src: [[sources/hp27s-om]]
  p. 115); in CFLO, holding INPUT keeps FLOW(0) on the display (src:
  [[sources/hp17bii-om]] p. 84; [[sources/hp17b-om]] p. 84).
- A 1.5 s press of a digit or an operator did nothing more than a tap:
  no auto-repeat and no NULL as on the 42S (observed).

## CLR-key combinations

CLR is held down and the other key or keys pressed before CLR is
released. Same matrix positions on all three models, and on the 42S's
EXIT chords ([[hardware/hp42s-keyboard]]).

| Keys | Manuals | Observed (saturnus, 2026-10-25, all three models) |
| --- | --- | --- |
| CLR + + or − | Contrast (27S p. 15; 17BII and 17B p. 16) | Contrast 22 to 23, and 22 to 21 |
| CLR + third menu key | "MACHINE RESET", memory kept (27S p. 195; 17BII and 17B p. 218) | "MACHINE RESET" |
| CLR + first + sixth menu key | Erases continuous memory; "MEMORY LOST" on release (27S p. 196; 17BII and 17B p. 218) | "MEMORY LOST" when all three went down together; with CLR first and the menu keys 300 ms apart, CLR alone acted (the entry was cleared) |
| CLR + fourth menu key | A factory test; stop with CLR + third menu key (27S p. 198, footnote) | The same display pattern as the 42S's test mode |
| CLR + fifth menu key | The continuous self-test (27S p. 198; 17BII and 17B p. 220) | BEEP, then the test steps; "COPYRIGHT HP 1987" each cycle |
| CLR + shift | not listed | The calculator turns off (as shift CLR, OFF) |
| CLR + second menu key | not listed | Taken by the chord handler: an open entry stays, CLR does not act |
| CLR + any other key | not listed | Acts as CLR alone (clears the calculator line) |

## Open

- Nothing here was checked on real calculators.
- Whether the memory-clear chord works with CLR pressed first on a real
  unit (the manuals say "press and hold" the three keys).
- The factory test behind CLR + fourth menu key was not explored past
  its first screen.
- ALG versus RPN mode, the applications' menus and the editors were not
  swept.
