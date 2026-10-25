---
title: "HP 42S keyboard: functions not printed on the keys, and the EXIT-key combinations"
type: rom-behaviour
models: [42s]
status: draft
sources: ["[[sources/hp42s-om]]", "[[sources/emu42-pioneer-dump]]", "[[sources/garnier-hp42s-new-facts]]", "[[sources/hosoda-hp42s]]"]
tags: [keyboard, rom, self-test]
---

# HP 42S keyboard: unprinted functions and EXIT combinations

What the 42S keys do beyond their printed labels, and what EXIT (the
key on the ON line) does held together with another key. The model, the
key matrix and the first chord table are on [[hardware/hp42s]]; the
matrix rules on [[hardware/keyboard]]. The 48G/GX counterpart is
[[hardware/hp48gx-keyboard]].

The 42S prints a shifted function above every key but the shift key
itself, so almost nothing is hidden behind an empty label: what is not
printed are the held-key behaviours and the EXIT chords.

## How the facts were obtained

- **Manual**: the HP-42S Owner's Manual, by book page
  ([[sources/hp42s-om]]).
- **Observed (saturnus, 42S ROM, 2026-10-25)**: a black-box key sweep on
  the clean-room saturnus emulator. The ROM file used (SHA-256
  841e79a9f0878882f6dd9343d428f1065e3ea12c1da4c11980061db4615b0baf) is
  not byte-identical to the revision C image on [[hardware/hp42s]], but
  every screen of a full self-test cycle and of the sweep's starting
  states came out the same with both, so it is taken to be revision C in
  another byte order. 370 combinations: each of the 37 keys unshifted,
  after shift, with shift held, with EXIT held, and held 1.5 s, from a
  stack holding 1 and 5; unshifted, after shift and with EXIT held from
  an open number entry; unshifted and after shift with the TOP.FCN menu
  showing. About 25 follow-up sequences tried the multi-key chords with
  different timings. Changes were detected by the display and the
  annunciators (the 42S has no RPL memory to read). Not checked on a real
  42S.

Not covered: program-entry mode, ALPHA mode beyond its menu, the
CATALOG and the other menus' own keys, the matrix editor, printing.

## Held keys

- **Function preview and NULL.** Holding a key that runs a function
  shows its name; held about a second, NULL replaces the name and the
  function is cancelled when the key is let go (src: [[sources/hp42s-om]]
  p. 76). Observed: after a 1.5 s press every function key left the
  stack unchanged, while digits, E and ·, which only type, still typed
  (saturnus, 42S ROM, 2026-10-25). Details of the timing:
  [[hardware/hp42s]] "Held keys".
- **SHOW (shift ·)** shows X in full precision for as long as the key is
  held (src: [[sources/hp42s-om]] p. 36); observed: the display returns
  when the key is let go.
- **Available memory**: holding the memory key in the CATALOG menu shows
  the free bytes until it is let go (p. 40).

## Printed labels that do something else

- **⬅ with no number being keyed in** runs CLX: X becomes 0 and stack
  lift is disabled (src: [[sources/hp42s-om]] p. 48). Observed: the
  stack 1, 5 became 1, 0 (saturnus, 42S ROM, 2026-10-25).
- **▲ and ▼** scroll a menu that has more than one row (the ▲▼
  annunciator shows) (p. 23); observed: shift 9 (MATRIX) then ▲ shows
  the second row (STOIJ RCLIJ STOEL RCLEL PUTM GETM). With no such menu
  and no program they changed nothing in the sweep.
- **Shift ▲ and shift ▼ (BST, SST)** ended a number being keyed in
  (observed); with no program they changed nothing visible.
- **XEQ then ENTER** turns the XEQ prompt into an Alpha prompt for a
  name, with the ALPHA menu (observed, saturnus, 42S ROM, 2026-10-25).

## EXIT-key combinations

EXIT is held down and the second key pressed before EXIT is released.

| Keys | Manual | Observed (saturnus, 42S ROM, 2026-10-25) |
| --- | --- | --- |
| EXIT + + or − | Contrast darker, lighter, without disturbing anything else (p. 20) | Contrast 22 to 23, and 22 to 21 |
| EXIT + √x (third key) | Machine reset: "Machine Reset", memory kept (p. 266); also stops the self-tests (p. 261) | "Machine Reset" over the unchanged X |
| EXIT + Σ+ + XEQ | Clear all memory and flags: hold EXIT, hold Σ+, press XEQ, release Σ+, release EXIT; "Memory Clear" (p. 267-268) | "Memory Clear" with X = 0, but only when EXIT and Σ+ went down in the same instant; with EXIT pressed first, as the manual describes, nothing happened at any gap tried (30 to 300 ms) |
| EXIT + LOG (fourth key) | A factory self-test; stop it with EXIT + √x (p. 261, footnote) | A test mode: a display pattern; then ⬅ opens the memory browser (address and nibbles) and Σ+ shows a speed figure ("SPD 0999968") |
| EXIT + LN (fifth key) | The self-test: beeps, patterns, "OK-42S-E" or FAIL and a number, repeating (p. 261) | BEEP, then the test steps; "OK-42S-E" and "COPYRIGHT HP 1988" each cycle |
| EXIT + shift | not listed | The calculator turns off (as shift EXIT, OFF) |
| EXIT + 1/x | not listed | Nothing; [[hardware/hp42s]] lists it as the memory clear (see Contradictions) |
| EXIT + any other key | not listed | With a number being keyed in, the entry ends as with EXIT alone; from the stack nothing |

The test mode and its memory browser, and the ROM revision shown there,
are described in [[sources/emu42-pioneer-dump]] and
[[sources/garnier-hp42s-new-facts]]; [[hardware/hp42s]] also lists
EXIT + + + XEQ as the deep sleep ([[sources/hosoda-hp42s]] note 15),
which in the sweep only raised the contrast (+ auto-repeated to 31).

## Contradictions

- [[hardware/hp42s]] "Key chords" gives EXIT + 1/x as the memory clear
  (42S ROM in saturnus). This sweep, with the same chord timing that made
  EXIT + √x, LOG and LN work, saw nothing; the manual's memory clear is
  EXIT + Σ+ + XEQ (p. 267-268), the same keys as the first and sixth menu
  keys of the 27S, 17B and 17BII ([[hardware/hp27s-17b-17bii-keyboard]]).
  Not resolved; a real 42S would settle it
  ([[questions/hp42s-memory-clear-chord]]).

## Open

- Nothing here was checked on a real 42S.
- Whether the memory-clear chord needs EXIT and Σ+ together on a real
  calculator, or the emulator's key timing makes the sequential press
  fail.
- Program-entry mode, ALPHA typing, CATALOG and the editors were not
  swept.
