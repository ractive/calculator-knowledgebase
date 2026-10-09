---
title: "HP SASM Reference (Saturn assembler manual, HTML)"
type: source
authors: ["Hewlett-Packard", "Adrian Drury"]
year: 1998
raw: "raw/saturn-hardware/sasmhtml/sasm.html"
status: skimmed
tags: [saturn, cpu, instruction-set, assembler]
---

# HP SASM Reference (Saturn assembler manual, HTML)

HP's Saturn assembler manual from Goodies Disk 4, HTML conversion. Only
consulted so far for the register description (section 2.6, Hardware Status
table, around line 195-215 of the HTML) to settle the HST bit order. Not
otherwise read.

- HST: bit 3 MP (module pulled, *NINTX low), bit 2 SR (service request, set by
  SREQ?), bit 1 SB (sticky bit, non-zero bit shifted off the right), bit 0 XM
  (set by the 00 opcode RTNSXM) (section 2.6).
- Nickel's opcode list `hp48-sdk-1993/SATURN.TXT` agrees: XM=0 821, SB=0 822,
  SR=0 824, MP=0 828.

Authoritative (HP's own). Worth a full read for instruction semantics when the
emulator CPU work starts; see [[hardware/saturn-cpu]].
