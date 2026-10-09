---
title: "Duchesne, HP48 S, SX, G, GX Interrupts: Tricks to Know (English ed., rev. 07c)"
type: source
authors: ["Régis Duchesne", "Marcos Navarro"]
year: 1994
raw: "raw/saturn-hardware/Interrupts48-R_DUCHESNE-EN-Rev07c.pdf"
status: digested
tags: [hp48, interrupts, rom, timers]
---

# Duchesne, HP48 S, SX, G, GX Interrupts: Tricks to Know (English ed., rev. 07c)

Navarro's 2024 English translation (34 pages, text PDF) of Duchesne's 1994
French paper, the original being [[sources/hpregint-fr]]. Cite by **PDF**
page number (printed page numbers are 3 lower). Knowledge "dated 25/5/1994" (p. 1).

## Content

- Ch. I (p. 4): concepts.
- Ch. II (p. 5-15): interrupt types (2.1, p. 5) and a commented disassembly of the
  ROM interrupt handler at #0000F for S and G (2.2). The listing itself is
  ROM code and is **not** reproduced in this wiki; only the behaviour
  described in the comments (p. 10-15) is.
- Ch. III (p. 16-): replacing the handler, via a RAM card configured
  at #00000 (needs an empty-name backup object) or via internal RAM using a
  return-oriented chain of ROM fragments.

## Facts used

Recorded on [[hardware/interrupts]], [[hardware/timers]], [[hardware/uart]],
[[hardware/keyboard]], [[hardware/memory-controller]]:

- Maskable = keyboard keys other than ON (INTOFF/INTON). Non-maskable = ON,
  timers 1 and 2 reaching zero with their interrupt bit set, and card
  insertion/removal (p. 5, 2.1).
- The S handler halts with "configuration anomaly" if C=ID is non-zero (a
  module left unconfigured); the G handler does not check (p. 10).
- The handler saves CPU state to a fixed RAM block (#7045C on S, #805DB on
  G), restores OUT from the OUT shadow (#704C3 on S, #80642 on G), and checks
  in order: TIMER2 running, battery, RAM check word #A5C3F, serial transmit,
  IR, card switches, clock/alarms, TIMER1/keys, annunciators, ON
  combinations, keyboard (p. 10-14).

## Reliability

High: derived from reading the ROM. Two slips: 2.2 says flag 15 "set" where
the code and every other source mean "clear" (the French original has the
same slip); the claim that timer interrupts are non-maskable conflicts with
the tutorial's wording (see [[questions/interrupt-maskability]]).
