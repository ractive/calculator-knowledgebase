---
title: "Lewis CPU clock and the RATE register"
type: question
status: open
tags: [lewis, clock, timing, cpu]
models: [42s]
---

# Lewis CPU clock and the RATE register

Known: a 32.768 kHz crystal (src: [[sources/hosoda-hp42s]] note 1), the
8192 Hz timer (src: [[sources/garnier-hp42s-new-facts]] 2.4), CPU speed
"depending on the RATE control register" (src: [[sources/emu42-manual]]
8.6.1) at #40300 (src: [[sources/garnier-hp42s-new-facts]] 2.3); Hosoda
measured currents with #40300 = 7 and = F and got four times the speed with
F and a doubled crystal (src: [[sources/hosoda-hp42s]]). Emu42 sets the CPU
frequency as a multiple of 16384 Hz (src: [[sources/emu42-manual]] 8.6.3).

Open:

- The CPU frequency at the reset value of RATE. The 42S ROM never writes
  the register #40300 in boot, keys or the self-test (saturnus trace), so the reset value
  sets normal speed. "About 1 MHz" is the common figure (unverified).
  Inferred, not checked: f = (RATE + 1) x 131072 Hz would give 1.05 MHz at
  7 and 2.1 MHz at F.
- The cycle counts of the Lewis CPU core (saturnus uses the 48SX's SASM
  table). A real-calculator benchmark is needed to calibrate, as done for the
  48 models ([[questions/instruction-speed-vs-hardware]]).
- What the self-test's "SPD" number measures; saturnus at 1 MHz shows
  08847 to 08974. A photo of a real unit's SPD value would calibrate the
  clock directly.
