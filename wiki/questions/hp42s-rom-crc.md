---
title: Does @ractive's 42S ROM image pass the ROM's own CRC test?
type: question
status: open
tags: [lewis, crc, rom]
models: [42s]
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

## More images (2026-10-13)

Four more Lewis images from @ractive, run in saturnus (iteration 42):

- A second 42S image (SHA-256 841e79a9...0baf) is the same ROM as the
  revision C image above, stored in dump order (see
  [[hardware/saturn-models]] "ROM image forms"). Put in address order it
  is identical byte for byte, so it adds no evidence.
- The 27S (revision B), the 17B (main ROM revision B, language ROM B) and
  the 17BII (main ROM revision B, language ROM A) run the same ROM step:
  their self-test (CLR + fifth menu key) reads #0001C-#1FFFB in three
  blocks of two interleaved halves #5550 nibbles apart, 16 nibbles per
  read, and wants #FFFF. With the 48's CRC rule they give the values
  of #5DFB (27S), #AB1D (17B) and #1326 (17BII); the step prints the
  value ("ROM 05DFB") and the pass ends in FAIL. A Python run of the same read order gives the
  same values.
- No 16-bit CRC with any polynomial (#0001-#FFFF), shifted either way,
  starting from 0 or #FFFF, gives #FFFF on all four ROMs in that read
  order (exhaustive search). Neither does counting instruction fetches
  into the CRC, reading only one of the two streams, swapping them, or
  feeding nibbles one at a time instead of in 16-nibble words.

Four independent dumps of four different ROMs failing the same way makes
damaged dumps unlikely. The Lewis CRC generator, or what the chip feeds
into it during a 16-nibble read, most likely differs from the Clarke's in
a way none of the variants above covers. Open; `LEWISCRC` on any one of
these images would tell whether the images are good.
