---
title: "HST bit order: is bit 1 SR or SB?"
type: question
status: answered
tags: [saturn, cpu]
---

# HST bit order: is bit 1 SR or SB?

- Mastracci: XM, SR, SB, MP from LSB to MSB (src:
  [[sources/mastracci-saturn-guide]] 2.2).
- Tutorial: XM bit 0, SB bit 1, SR bit 2, MP bit 3; clear-by-mask opcodes
  XM=0 821, SB=0 822, SR=0 824, MP=0 828 (src: [[sources/saturn-tutorial]]
  p. 43, 96).

The opcode masks are concrete and assembler-tested, so the tutorial order is
the working assumption. Confirm against the HP SASM manual
([[sources/sasm-reference]]) or [[sources/voyage-48gx]].

## Answer

Bit 0 XM, bit 1 SB, bit 2 SR, bit 3 MP. HP's SASM manual says so (src:
[[sources/sasm-reference]] 2.6), as do Nickel's opcode list and the tutorial.
Mastracci 2.2 has SB and SR swapped.
