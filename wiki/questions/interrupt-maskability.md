---
title: "Which interrupt sources does INTOFF mask?"
type: question
status: open
tags: [saturn, interrupts]
---

# Which interrupt sources does INTOFF mask?

- Duchesne: maskable = keys other than ON; non-maskable = ON, timers 1 and 2
  reaching zero with their interrupt bit set, card insertion/removal (src:
  [[sources/duchesne-interrupts-en]] 2.1).
- Ervin: INTOFF blocks only keyboard interrupts; other devices still
  interrupt (src: [[sources/keyboard-ervin]] 4.1.2).
- Mastracci lists UART, keyboard, timers, low battery and IR as "maskable"
  and only ON and VLBI as non-maskable (src:
  [[sources/mastracci-saturn-guide]] 2.3).
- Tutorial (Giesselink): "a maskable interrupt source like the keyboard,
  timer or serial port can be disabled, but non-maskable sources like the ON
  key always occur" (src: [[sources/saturn-tutorial]] p. 98).

Reading: INTOFF gates only the keyboard scan; the timers, UART and IR are
gated by their own enable bits in I/O RAM, which is what Mastracci and the
tutorial call "maskable". Everything is gated by the in-service flag. Confirm
with [[sources/voyage-48gx]].
