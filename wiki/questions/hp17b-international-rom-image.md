---
title: What form does a 96 KB international 17B/17BII ROM image take?
type: question
status: open
tags: [lewis, rom, pioneer]
models: [17b, 17bii]
sources:
  - "[[sources/emu42-pioneer-dump]]"
  - "[[sources/emu42-changes]]"
  - "[[sources/garnier-hp42s-new-facts]]"
---

# What form does a 96 KB international 17B/17BII ROM image take?

International 17B and 17BII units have 96 KB of ROM instead of 64 KB:
Gießelink's dump utility reads 768 blocks of 128 bytes from them, and
their memory scanner shows two revision characters, the first the main
ROM's (src: [[sources/emu42-pioneer-dump]] PIONEER). Emu42 v1.24 sizes
the in-chip and outside ROMs by model and prepares the larger Pioneers
with a 32 KB extra ROM for these versions (src: [[sources/emu42-changes]]
v1.24). The 42S ROM's hook for a second ROM at #20000 (src:
[[sources/garnier-hp42s-new-facts]] 3.3) is where the extra 32 KB would
fit (inferred, [[hardware/saturn-models]]).

Open:

- Is the dumped image one 96 KB file, #00000-#2FFFF in address order, or
  a 64 KB main ROM plus a separate 32 KB file?
- Packed bytes (two nibbles per byte, as the 64 KB 42S image) or one
  nibble per byte?
- Does the second ROM really answer at #20000-#2FFFF, and does the main
  ROM find it through the same check the 42S makes?
- Does the second revision character come from the second ROM?

How to settle it: a 96 KB image itself, or a description from someone who
has dumped an international unit. The dump procedure says the image size
follows from the block count, so a 96 KB file in address order is the
likely answer (unverified).
