---
title: "Lewis register roles not settled by the 42S ROM"
type: question
status: open
tags: [lewis, registers, 42s]
---

# Lewis register roles not settled by the 42S ROM

See [[hardware/lewis]] "Registers".

- #4030E: Garnier guesses "beep?" (src: [[sources/garnier-hp42s-new-facts]]
  2.3); the ROM sets and clears its bits 1 and 2 around SHUTDN and ORs 6 into
  it, like the 48's TIMER1 control; saturnus treats #4030E/#4030F as the timer
  controls and #403F7 as TIMER1. The beeper is driven by OUT bits 10 and 11.
- LPD #40308: which bit is which of GRAM, VLBI, LBI (src:
  [[sources/emu42-changes]]); the ROM debounces bits 0 and 1. saturnus reads
  0 (batteries good).
- LPE #40309, INPORT #4030C, LEDOUT #4030D, DSPTEST #40302, RAMTST #4030B:
  names from [[sources/emu42-problems]], bit positions unknown.
- The annunciator words are 5 nibbles of #FFFFF; which bit drives the
  segment is unknown (saturnus looks at the first nibble).
