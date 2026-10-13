---
title: Does @ractive's 42S ROM image pass the ROM's own CRC test?
type: question
status: answered
tags: [lewis, crc, rom]
models: [42s, 27s, 17b, 17bii, 28s]
sources: ["[[sources/hpmuseum-forum]]", "[[sources/emu42-pioneer-dump]]", "[[sources/hosoda-hp42s]]"]
---

# Does @ractive's 42S ROM image pass the ROM's own CRC test?

**Answer (2026-10-13): yes.** The images are good; the Lewis CRC is not
the Clarke's. It is the remainder of the bit stream read, each nibble
most significant bit first, divided by x^16 + x^14 + x^13 + x^11 + 1, starting
from 0. With that rule every Lewis image at hand passes both passes of its
self-test's ROM step with #FFFF. The rule is on [[hardware/lewis]] "CRC";
how it was found is below.

## The ROM step

The 42S self-test (EXIT + LN) has a ROM step at #01C44 (revision C). It
zeroes the hardware CRC at #40304 and reads #0001C-#1FFFB through it as data
(three blocks of two interleaved pointers #5550 nibbles apart, 16 nibbles
per read). The routine then reads the CRC back and sets carry only
when it is #FFFF. On success a second pass reads the whole ROM
(#00000-#1FFFF) in address order, 16 nibbles per read, and must give #FFFF
too (42S ROM, routine at #01CB3). The 27S, 17B and 17BII run the same
routine (CLR + fifth menu key). Real units print "ROM O.K."
(src: [[sources/hosoda-hp42s]]).

The 19BII and 28S ROMs (two Lewis chips, CRC at #FFF04) run the same
routine four times: both passes on #00000-#1FFFF, then both on
#20000-#3FFFF. The 28S gives each of the four its own failure code
(28S and 19BII ROMs, disassembled with saturnus). The 48SX ROM contains the same
routine on #104; it uses it to checksum a plugged-in card, not the ROM
(48SX ROM at #044C9).

## What failed

With the 48's CRC rule ([[hardware/crc]]) saturnus gets #1BE8 on
@ractive's revision C 42S image (SHA-256 f4c5f9f0...08f3), #5DFB on the 27S,
#AB1D on the 17B and #1326 on the 17BII; the step prints the value ("ROM
01BE8") and the summary reads FAIL. A second 42S image (SHA-256
841e79a9...0baf) is the same ROM in dump order ([[hardware/saturn-models]]
"ROM image forms"). These variants all failed on all four ROMs:

- every 16-bit polynomial, shifted either way, from 0 or #FFFF, in the
  read order above
- counting instruction fetches into the CRC
- one stream only, or the two streams swapped
- nibbles fed one at a time

## How the rule was found

A test that does not depend on the start value or a final XOR: all images
of a family are made to give the same constant, so the right generator
gives every image the same value even when its start value is wrong. Over
every 16-bit feedback mask, with the nibbles of each 16-nibble word in six
orders and their bits either way, one generator gave the four Pioneer
ROMs' second pass (whole ROM) the same value: nibbles in address order,
each fed most significant bit first, through the polynomial above. A
chance hit across four images is about 1 in 2^48. Of the register forms
tried (the CRC form, a Fibonacci shift register, and the form in which each
input bit enters at the bottom and the bit shifted out of the top folds
in the polynomial), only the last gives #FFFF from 0, on both passes. That
last form computes the plain remainder of the message polynomial modulo
the polynomial. Computed with it:

| ROM | Pass 1 | Pass 2 |
| --- | --- | --- |
| 42S revision C | #FFFF | #FFFF |
| 27S revision B | #FFFF | #FFFF |
| 17B main ROM | #FFFF | #FFFF |
| 17BII main ROM | #FFFF | #FFFF |
| 19BII #00000 and #20000 halves | #FFFF | #FFFF |
| 28S #00000 and #20000 halves | #FFFF | #FFFF |

The 17B's and 17BII's 32 KB language ROMs give #FFFF read whole in address
order. The 28S image only passes after reordering it from dump order
(256 units of 512 bytes, last first); in the order it was handed over it
fails, so that image is in dump order like the Pioneer ones.

The polynomial is primitive (x has order 65535 modulo it). Each 64 KB
Lewis ROM ends with its self-test pass text followed by eight nibbles that
look random, #1FFF8-#1FFFF. Pass 1 stops at #1FFFB and pass 2 reads to
#1FFFF, so these are probably two check words, one per pass (inferred;
any four nibbles inside a pass's range could serve).

## Outside confirmation

Gießelink's LEWISCRC (V1.10) prints, per Lewis chip, "Pass 0" and "Pass 1"
both #FFFF on good 27S, 42S, 17B, 17BII, 19BII and 28S images, plus an
"External ROM CRC" of #FFFF on the 17B and 17BII (src:
[[sources/hpmuseum-forum]] F6). That is the same two-pass structure and the
same target as the ROM step, and our rule reproduces it on the images
here. Running LEWISCRC on @ractive's images (as given, in dump order, and in
address order) would confirm the images directly, but it is no longer
needed to decide between a bad dump and a different generator.

## Not covered

The Sacajawea (1LR3) ROMs (14B, 22S, 32S, 32SII) also run a two-pass
CRC check (LEWISCRC: "Sacajawea CRC Pass 0/1 = FFFF", F6), at #24304 on
the 32SII. With this rule their whole images do not give #FFFF, linearly
or after reordering from dump order. Their read order and comparison differ
(the 32SII reads five nibbles back and compares differently), so they were
not checked further.

## The 28S in saturnus (2026-10-13)

The 28S's self-test (ON + the ◀ menu key) runs the same ROM step on its
master chip's CRC (#FFF04): #0001C-#1FFFB in three blocks of two
interleaved halves #5550 nibbles apart, 16-nibble reads, wanting #FFFF
(code at #0169B and #01770 of revision 2BB). With the 48's rule the
owner's image got #185A and the pass ended in "8 FAIL" (ST bit 11); with
the rule above it ends in "OK-28S" (see [[hardware/hp28s]] "Self-test";
saturnus iteration 43a).
