---
title: "Hosoda, HP-42S memory upgrade and overclock"
type: source
authors: ["Takayuki Hosoda"]
year: 2007
raw: "raw/hp42s/hosoda-hp42s-memory-overclock.txt"
status: digested
tags: [42s, lewis, clock, ram]
---

# Hosoda, HP-42S memory upgrade and overclock

Hardware notes from someone who opened eleven Pioneer calculators
(2007-2020). Measurements on real units; the most reliable source for the
board. Cite by section or note number.

- Memory upgrade: the stock RAM is an 8 KB S-MOS SRM2264 SRAM beside the
  processor; replaced by a 32 KB SRAM after changing jumpers, the revision C
  ROM reports 31533 bytes free.
- note 1: the clock oscillator runs a 32.768 kHz crystal (measured with a
  frequency counter at a buffered test point).
- Crystal replacement: a 65.536 kHz crystal doubles the speed; "setting the
  speed register at 0x40300 to 0xF by using the built-in debugger" gives four
  times the original speed. Supply currents are listed for `*(0x40300)` = 7
  and = F.
- Self-test EXIT+LN results per supply voltage: "ROM O.K.", "DRAM FAIL",
  "URAM O.K." lines; low battery indicator at 3.0 V.
- note 15: EXIT, + and XEQ together put it into a deep sleep.
- The partial schematic names the processor 1LR2.

Facts used on [[hardware/hp42s]] and [[hardware/lewis]].
