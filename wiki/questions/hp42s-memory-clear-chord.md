---
title: Which chord clears the 42S memory, EXIT + 1/x or EXIT + Σ+ + XEQ?
type: question
status: open
tags: [keyboard, rom, lewis]
models: [42s]
sources: ["[[sources/hp42s-om]]"]
---

# Which chord clears the 42S memory?

[[hardware/hp42s]] "Key chords" lists EXIT + 1/x as the memory clear
("Memory Clear", a cold start), found by running the 42S ROM in
saturnus. The Owner's Manual gives EXIT + Σ+ + XEQ instead (src:
[[sources/hp42s-om]] p. 267-268), the first and sixth top-row keys, the
same positions as the memory clear of the 27S, 17B and 17BII
([[hardware/hp27s-17b-17bii-keyboard]]).

A key sweep in saturnus on 2026-10-25 saw nothing for EXIT + 1/x, with
the timing that made EXIT + √x, LOG and LN work; EXIT + Σ+ + XEQ worked
only when EXIT and Σ+ went down at the same instant
([[hardware/hp42s-keyboard]]). Both runs are on an emulator. A real 42S
would settle which chord the ROM takes, and how strict its timing is.
