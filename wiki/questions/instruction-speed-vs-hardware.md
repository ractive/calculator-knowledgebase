---
title: How fast do real 48SX/48GX/49G run compared with the cycle counts?
type: question
status: open
tags: [saturn, cpu, timing]
models: [48sx, 48gx, 49g]
---

# How fast do real 48SX/48GX/49G run compared with the cycle counts?

saturnus times instructions with the SASM manual's approximate cycle counts
(src: [[sources/sasm-reference]] 8) at a 2 MHz clock (src:
[[sources/saturn-tutorial]] p. 43) plus a flat 13% display-refresh stall
(src: [[sources/voyage-48gx]] p. 193). The ROM's own clock stays within 1 ms
over 600 s under that model ([[hardware/timers]]), because the handler
compensates with TIMER2. But the *speed* of code has no oracle yet:

- In saturnus, ROM J runs `TICKS 1 2000 START NEXT TICKS SWAP -` in 48880
  ticks = 5.97 s (2026-10-05, measured over the Kermit bridge).
- saturnng returns 145 ticks for the same program, which is implausibly
  fast; its TICKS clock is evidently not tied to its instruction timing, so
  it is no oracle for speed.
- Kermit still works at that speed: hptx's e2e suite passes 6/6 over the
  emulated wire, with per-packet turnaround of 100-360 ms on the calculator
  side.

## Experiment

On a real HP 48SX (or S), in HOME with the clock display off, run
`TICKS 1 2000 START NEXT TICKS SWAP -` a few times and note the result in
ticks (1/8192 s). Compare with 48880. A real figure settles whether the
SASM counts, the 2 MHz figure or the stall model is off, and by how much.
The same program on a 48GX gives the Yorke figure for later.

## Progress (2026-10-05): a published benchmark as oracle

The HP Museum thread "Summation based benchmark for calculators" (src:
[[sources/hpmuseum-summation-benchmark]]) times
`sum(x=1..n) cbrt(exp(sin(atan(x))))` on many calculators, including real
48SX, 48GX and 49G units. Replaying the same programs on saturnus
(through `saturnus-mcp`'s Kermit host command, unpaced, measuring with
TICKS) gives:

| Machine | Program | n | Real hardware | saturnus | saturnus / real |
| --- | --- | --- | --- | --- | --- |
| 48SX | `TICKS 'Σ(X=1,n,XROOT(3,EXP(SIN(ATAN(X)))))' EVAL SWAP TICKS SWAP -` | 1000 | 95.5 s (Bob Prosperi, post 135) | 80.3 s (10 x the n=100 run: 65779 ticks) | 0.84 |
| 48SX | `0 1 n FOR X X ATAN SIN EXP 3 INV ^ + NEXT` | 100 | (no real figure) | 64214 ticks = 7.84 s | - |
| 48GX | FOR/NEXT, UserRPL | 100 | 5.5 s (thread summary) | 29695 ticks = 3.62 s | 0.66 |
| 48GX | Σ sum function | 100 | 5.9 s (thread summary) | 30478 ticks = 3.72 s | 0.63 |
| 49G (ROM 2.10) | FOR/NEXT, radians | 100 | 5.5 s (post 195) | not measured here (the in-process Kermit host command timed out on the 49G; measured in iteration 7, see below) | - |

The sums match the real machines digit for digit (139.297187047 for
n=100; the thread lists 1395.3462877 for n=1000). So saturnus runs the
48SX about 16% too fast and the 48GX about 35% too fast. The direction
contradicts the earlier worry (the 2000-pass empty `START NEXT` loop at
5.97 s looked slow, but no real figure exists for it). Candidate causes:
the SASM cycle counts are approximate and low for the instruction mix of
the ROM's math code; the 13% flat display stall underestimates the refresh
cost; the Yorke clock is below 4 MHz or the G series has different cycle
counts (Emu48 SP1 says it does). A per-model calibration against these
figures is the practical next step; the question stays open until a
cause, not a fudge factor, explains both ratios.

## Narrowed (2026-10-05, saturnus iteration 7)

Measured again at n = 1000, which is the precise figure. At n = 100 the
48s spend about 5% extra on start-up, and the 49G's first `Σ` costs
about 2.2 s more on saturnus than the real n = 100 figure allows (cause
unknown). On the 49G the FOR/NEXT loop needs real literals
(`0. 1. 100. FOR ... 3. INV ^`): with exact integers the 49G computes
symbolically for minutes, which is what looked like a Kermit timeout.

| Machine | n = 1000 real | SASM only | Meta Kernel table on Yorke | + calibration |
| --- | --- | --- | --- | --- |
| 48SX, sum | 95.5 s | 75.4 s | (SASM) 75.4 s | 95.5 s |
| 48GX, sum / FOR | 55 / 54 s | 34.4 / 33.9 s | 41.2 / 40.5 s | 55.0 / 54.1 s |
| 49G ROM 2.10, sum / FOR | 47.8 / 51.0 s | 33.0 s / - | 40.2 / 41.8 s | 48.4 / 50.3 s |

What the evidence shows:

- **The G series needs its own cycle counts.** An instruction profile of
  the benchmark shows the 48SX (ROM J) and 48GX (ROM R) running almost the
  same mix: 13.1M vs 11.9M instructions, 137M vs 126M SASM cycles, mostly
  the BCD digit loops (shifts, subtracts, GONC). With one cycle table the
  GX would be 2.2x the SX, but it is 1.74x. The 48 FAQ says the same: the
  G/GX "throughput is approximately 40% faster", not 2x, "due to various
  overheads" (src: [[sources/hp48-faq]] 3.2). The Saturn tutorial's
  counts, taken from the Meta Kernel documentation for the 48G (src:
  [[sources/saturn-tutorial]] p. 44, ch. 33-52), are higher than SASM's:
  about 0.5 cycle per opcode nibble, with longer jumps and DAT reads.
  Timing the Yorke models with them brings the GX's error relative to the
  SX from 26% down to 5%. This agrees with "cycle counts differ between
  the S/SX and the G series" ([[emulators/emu48]] SP1).
- **The display stall is right.** The thread's assembly measurement on a
  GX (post 165: 39.7 s with the display on, 34.8 s off) is a 14%
  slowdown. That matches the 13% model (src: [[sources/voyage-48gx]]
  p. 193).
- **No source gives a measured clock.** The 48SX's CPU clock is
  multiplied from the 32 kHz crystal (src: [[sources/hpj-48sx]] p. 30).
  The G series has a "4-MHz bus rate" (src: [[sources/hpj-48gx]] PDF
  p. 4), "~4 MHz, varies with temperature" (src:
  [[sources/mastracci-saturn-guide]] 2.1).
- **What remains is a 19-34% slowdown that no source explains.** It is on
  both chips: 48SX 1.27, 48GX 1.34, 49G 1.19-1.22. saturnus applies it as
  a per-model calibration factor (48SX 1.267, 48GX 1.335, 49G 1.205).
  The 48GX and 49G share the Yorke but differ by 11%, so memory speed
  (mask ROM vs flash) is one candidate (unverified). Another is that
  both cycle tables omit a bus overhead of the byte-wide commercial
  memories, which the 48SX's IC needs "careful interfacing" for (src:
  [[sources/hpj-48sx]] p. 30; unverified).

Experiment that would settle it: on a real 48SX and 48GX, time a loop of
one instruction class (for example 10,000 `ASR W`, or 10,000 `A=DAT0 A`)
with the display off, against TIMER2. The per-instruction time against
SASM or the Meta Kernel count gives the clock and the table error
separately.
