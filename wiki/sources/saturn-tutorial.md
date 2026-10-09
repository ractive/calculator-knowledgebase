---
title: "Fernandes and Rechlin, Introduction to Saturn Assembly Language, 3rd ed."
type: source
authors: ["Gilbert Fernandes", "Eric Rechlin"]
year: 2005
raw: "raw/saturn-hardware/Saturn_tutorial.pdf"
status: digested
tags: [saturn, cpu, instruction-set, memory, display, keyboard, hp48, hp49g]
---

# Fernandes and Rechlin, Introduction to Saturn Assembly Language, 3rd ed.

Edition 3, 2005-07-16. 189-page text PDF; cite **printed book page numbers**
(the PDF has about 8 front-matter pages more). Chapters 66-68 (memory
management, HP 49 memory, graphics) and 50 (interrupts) were written by
Christoph Giesselink, the Emu48 author; they are the most reliable hardware
material in the book.

## Read

| Ch. | Pages | Content | Fed into |
| --- | --- | --- | --- |
| 21-30 | 33-44 | registers, fields, nibble order, P, RSTK, ST/HST/carry, IN/OUT, clock, cycle counts | [[hardware/saturn-cpu]] |
| 32-54 | 47-102 | full instruction set with opcodes, carry effects and approximate cycle counts, incl. Saturn+ (ARM) extensions | [[hardware/saturn-cpu]] |
| 50 | 98-99 | interrupt-in-service flag, DisableIntr/AllowIntr | [[hardware/interrupts]] |
| 51 | 99-100 | RESET, SREQ?, CONFIG, UNCNFG, C=ID, SHUTDN wake conditions | [[hardware/memory-controller]], [[hardware/interrupts]] |
| 60 | 136-140 | keyboard OUT/IN tables for HP48 and HP49 | [[hardware/keyboard]] |
| 66 | 150-160 | HP48 memory controllers, daisy chain, priority, C=ID codes, default maps per model, GX bank switcher and its quirks | [[hardware/memory-controller]], model pages |
| 67 | 161-165 | HP49 flash/RAM banks, controllers, flash write enable | [[hardware/hp49g]] |
| 68 | 165-171 | display controller: refresh, main/menu areas, margins, register bits, RAM ghost registers | [[hardware/display]] |

Skipped: Part I-II (number bases, tools), Part V objects (55-58, read later
for [[protocols/hp-object-format]] if needed), Part VI programming examples
except ch. 60. The book has no UART, timer or IR chapter.

## Reliability

Good for the instruction set (cycle counts are approximations from the Meta
Kernel docs, p. 44). Chapters by Giesselink are first-hand and experimental.
Small internal inconsistencies: LINENIBS at #123 (p. 170) versus #125 (p. 167);
"vertical offset" for the BITOFFSET left margin (p. 169); "if bit 15 is null,
[ON] was pressed" (p. 138) versus the code that loops while it is 0 (p. 139).

Disagreements with other sources, recorded on the subject pages:

- HST bit order XM, SB, SR, MP (p. 43, 96) versus Mastracci's XM, SR, SB, MP:
  [[questions/hst-bit-order]].
- 48SX RAM 32 KB at #70000 (p. 156) versus Mastracci's "128 KB (SX)".
- CONFIG granularity #1000 (p. 151) versus Mastracci's #100.
- 49G bank-switch latch bit assignment versus [[sources/memory49-sousa]]:
  [[questions/hp49g-bank-latch-bits]].
