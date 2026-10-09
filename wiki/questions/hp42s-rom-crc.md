---
title: Does @ractive's 42S ROM image pass the ROM's own CRC test?
type: question
status: open
tags: [42s, lewis, crc, rom]
---

# Does @ractive's 42S ROM image pass the ROM's own CRC test?

The 42S self-test (EXIT + LN) has a ROM step at #01C44 (revision C). It
zeroes the hardware CRC at #40304, reads #0001C-#1FFFB through it as data
(three passes of two interleaved pointers #5550 nibbles apart, 16 nibbles
per read) and wants #FFFF; on success a second pass reads the whole ROM
(#00000-#1FFFF) the same way. See [[hardware/lewis]] "CRC".

With the 48's CRC rule (see [[hardware/crc]]) saturnus gets #1BE8 on the
@ractive's revision C image (SHA-256 f4c5f9f0...08f3), the step prints "ROM
01BE8" and the summary FAIL; an independent Python computation of the same
read order gives #1BE8 too. No other CRC-16 variant tried (polynomials #1021,
#8005, #8408, #A001 and others, reflected or not, init 0 or #FFFF, nibbles
plain, inverted or bit-reversed) yields #FFFF, and counting instruction
fetches into the CRC gives #3D71. No interrupt fires during the step.

Two explanations: the dump has wrong bits (one bad byte suffices), or
the Lewis CRC differs from the Clarke's. Real units print "ROM O.K."
(src: [[sources/hosoda-hp42s]]).

## To settle

- Run `LEWISCRC` on the image (Windows tool in the Emu42 package; see
  [[sources/emu42-pioneer-dump]]). If it reports the CRC good, the Lewis CRC
  rule differs; if bad, the image is damaged.
- Or dump @ractive's calculator again (the IR route to the 48SX) and compare
  the two images byte by byte.
