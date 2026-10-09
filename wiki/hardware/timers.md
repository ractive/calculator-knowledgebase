---
title: "Timers"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/keyboard-ervin]]", "[[sources/saturn-tutorial]]", "[[sources/duchesne-interrupts-en]]", "[[sources/voyage-48gx]]"]
tags: [saturn, timers]
---

# Timers

Two hardware timers in I/O RAM.

| Timer | Width | Rate | Value at | Control at |
| --- | --- | --- | --- | --- |
| TIMER1 | 4 bits | 16 Hz | #137 | #12E |
| TIMER2 | 32 bits (8 nibbles, #138-#13F) | 8192 Hz | #138 | #12F |

(src: [[sources/mastracci-saturn-guide]] 4.10)

- Each timer decrements every tick; on reaching zero it performs the action
  selected in its control nibble (4.10).
- TIMER1 is used by the ROM handler to detect keys held down and released
  (4.10); after a key interrupt the handler schedules a 1/16 s timer
  interrupt to update the key buffer (4.9).

- The ROM keeps time from TIMER2; it can miss rollover interrupts for 72 hours
  without losing time (src: [[sources/keyboard-ervin]] 4.1.1). At 8192 Hz,
  2^31 ticks is 72.8 h, so the ROM appears to watch the top bit (inferred).
- The ROM keyboard service loop synchronises to TIMER1 ticks and exits when
  the next tick is less than about 17 ms away (src: [[sources/keyboard-ervin]]
  3.3). TIMER1 timing must therefore be accurate for key handling to feel
  right.

## Control nibbles

| Bit | #12E (TIMER1) | #12F (TIMER2) |
| --- | --- | --- |
| 0 | extra function (XTRA) | TRUN (run) |
| 1 | interrupt | interrupt |
| 2 | wake | wake |
| 3 | service request | service request |

(src: [[sources/mastracci-saturn-guide]] 4.10)

Voyage describes the bits by function (src: [[sources/voyage-48gx]] p. 203):

| Bit | #12E (TIMER1) | #12F (TIMER2) |
| --- | --- | --- |
| 0 | not described | timer running; the handler checks it and halts the system if clear; only "coma" mode stops TIMER2, and leaving coma reconfigures the machine |
| 1 | reaching zero raises an interrupt | same |
| 2 | reaching zero wakes the CPU from SHUTDN | same |
| 3 | timer needs service: set when the interrupt has happened, used by the handler | same |

- TIMER1 (#137) counts down in 1/16 s steps from #F to #0; the ROM uses it to
  detect key release so that two presses of the same key are seen (p. 204).
- TIMER2 (#138-#13F, 8 nibbles) counts down in 1/8192 s steps from #FFFFFFFF
  to #00000000; passing through zero raises the interrupt. The ROM loads it
  with #00001FFF (1 s) while the clock is shown, with the ticks to an alarm
  due within the hour, or else with 1 hour = #01C20000, reset to 1 hour on
  each key press in interactive mode (p. 204).

## ROM use (Duchesne)

- The handler halts the machine with "Clock corrupted" if TIMER2 is not
  running (src: [[sources/duchesne-interrupts-en]] p. 12).
- Timekeeping: the ROM keeps a time offset in RAM and adds the TIMER2 delta
  since it last wrote TIMER2; it then reloads TIMER2 with 1 s (clock shown)
  or the smaller of 1 h and the time to the next alarm. The code compensates
  for its own execution time with cycle-counted instructions (p. 13). An
  emulator's TIMER2 must therefore tick at 8192 Hz against emulated CPU time
  consistently, or the clock drifts.
- After a key-down, if TIMER1 has not passed zero, the handler sets TIMER1 to
  6/16 s (p. 13).
- Timer expiry with the interrupt bit set is a non-maskable interrupt (2.1, p. 5).

## Wake from SHUTDN

A timer wakes the CPU from SHUTDN when both the WKE bit and the MSB of its
control register are set (src: [[sources/saturn-tutorial]] p. 100). Under
Mastracci's naming the WKE bit is bit 2 and the MSB is bit 3 ("service
request"), so bit 3 probably doubles as the expiry flag (inferred; see
[[questions/timer-expiry-semantics]]).

## Emu48 findings

TIMER2 fires when it counts through zero into #FFFFFFFF and reads #FFFFFFFF
while that interrupt is pending (but see "TIMER2 read during service":
the read must not stay frozen until the CPU vectors); TIMER1 runs only
while TIMER2 runs;
reloading TIMER1 with its current value does not restart its period; the
annunciators are blanked while TIMER2 is stopped (src: [[emulators/emu48]]
Timers). See [[questions/timer-expiry-semantics]] (answered).

## Facts settled while building saturnus (2026-10-05)

- ROM J's clock (flag -40 shows it in the status line) gained no measurable
  time against the emulated TIMER2: 600 s shown in 600.0007 s of emulated
  time, -0.7 ms, below the 10 ms resolution of the measurement. This was
  in saturnus, where TIMER2 ticks at 8192 Hz of emulated time and
  instruction times are SASM's approximate cycle counts plus a flat 13%
  display stall. So the handler's self-compensation (src:
  [[sources/duchesne-interrupts-en]] p. 13) is insensitive to cycle errors
  of that size (emulator only, not hardware). The test still passes after
  saturnus's timing calibration (iteration 7: Meta Kernel counts on the
  Yorke models and a per-model factor, see
  [[questions/instruction-speed-vs-hardware]]) (saturnus decision log,
  iteration 7).
- With the clock shown, ROM J still turns the calculator off after ten
  minutes without a key press (observed in saturnus).

### TIMER2 read during service

- The interrupt handler of ROM J (48SX) and ROM R (48GX) has a wait at
  addresses #009A9-#009BD: `D0=(5) #00139`, `C=DAT0 S`, then `A=DAT0 S` and
  `?A=C S GOYES` back until TIMER2's nibble 1 changes, i.e. it
  synchronises to the next 16-tick boundary of the running counter. The
  49G ROM 2.10 holds the same instruction sequence (flash offset #40759).
  The handler runs it also when it was entered for a key while TIMER2
  expired, with #12F reading #F (expired, service requested) and the
  timer interrupt not yet taken (observed in saturnus, 2026-10-05).
- So a TIMER2 read keeps returning the counting value after an expiry the
  CPU has not vectored for: it reads #FFFFFFFF right after the wrap and
  then counts down. An emulator that holds the read at #FFFFFFFF until
  the next vectoring (one reading of the "pending" rule above) hangs the
  ROM in that loop with the clock shown (flag -40, TIMER2 reloaded every
  second): keys after the coincidence are lost and the clock stops.
  Inferred from the ROM's code and from @ractive's real 48SX, which does
  not hang with the clock shown; saturnng runs the same key sequence.

## Open

- Expiry is the counter's MSB going set (count through zero); the
  interrupt fires on the rising edge of MSB and INT:
  [[questions/timer-expiry-semantics]] (answered).
- Whether TIMER1 has a run bit; Mastracci and Voyage show a run bit only for
  TIMER2.
