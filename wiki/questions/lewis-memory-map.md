---
title: "How does the Lewis decode memory: fixed map or configurable windows?"
type: question
status: answered
tags: [lewis, memory-controller, memory]
models: [42s, 28s]
---

# How does the Lewis decode memory: fixed map or configurable windows?

On the 42S the ROM uses #40000-#403FF and #50000 from its first
instructions and never executes CONFIG, UNCNFG or C=ID (see
[[hardware/lewis]]). The 28S, also Lewis-based, has RAM at #C0000 and its
display at #FF840 (src: [[sources/hp28s-procnotes]]), and Emu42 talks of
separate "Register and Display/Timer MMU configuration" (src:
[[sources/emu42-problems]]).

Open:

- Are the 42S addresses the chip's reset defaults, or set by board straps?
  What do CONFIG and C=ID do on a Lewis?
- What answers outside ROM, the block at #40000 and RAM (#20000 is a
  possible second ROM, [[sources/garnier-hp42s-new-facts]] 3.3)? saturnus
  reads 0 there (inferred).
- Does 8 KB RAM really mirror through #5FFFF? The ROM only works with a
  mirror in saturnus, which suggests the RAM chip select decodes 32 KB and
  the SRAM ignores the top address lines (inferred).
- Does the display/register block mirror above #403FF?

## Answer from the 28S ROM (2026-10-13)

The 28S's ROM (revision 2BB, run on a logging bus in saturnus; see
[[hardware/hp28s]] "Memory map") behaves as the 42S's: after RESET at
its reset entry it addresses its blocks at once and never executes
CONFIG, UNCNFG or C=ID, through cold boot, arithmetic, menus, printing,
System Halt, Memory Reset and the self-test, and it reads or writes
nothing outside ROM #00000-#3FFFF, RAM #C0000-#DFFFF, the slave's
block #FF800-#FFBFF and the master's #FFC00-#FFFFF. Each chip's block has the
42S's internal layout (display RAM from offset 0, registers from #300)
at another base.

So for emulation the question is answered: **each ROM runs on a fixed
map**, the 42S's (one chip at #40000, RAM at #50000) or the 28S's (slave
at #FF800, master at #FFC00, RAM at #C0000), and no window registers need
modelling. Emu42's note that "Register and Display/Timer MMU
configuration isn't separated" (src: [[sources/emu42-problems]]) fits
chips whose windows are set before the CPU runs (inferred).

Still open, not needed to run either ROM:

- Where the bases come from: board straps, or chip reset defaults that
  differ per mask? What CONFIG and C=ID would do on a Lewis.
- Whether the blocks or the RAM mirror beyond what the ROMs use (the
  28S's RAM is 32 KB in a 64 KB window, so twice; the 42S's 8 KB needs
  its mirror, above).
