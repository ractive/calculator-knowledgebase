---
title: "How does the Lewis decode memory: fixed map or configurable windows?"
type: question
status: open
tags: [lewis, memory-controller, 42s, memory]
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
