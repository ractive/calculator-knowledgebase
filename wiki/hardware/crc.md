---
title: "Hardware CRC register"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/checksum-crc]]"]
tags: [saturn, crc, io-ram]
---

# Hardware CRC

- The CRC register in I/O RAM at #104 is updated on every nibble the CPU
  reads from memory (src: [[sources/mastracci-saturn-guide]] 2.6).
- To checksum a block: disable interrupts, write 0 to the register, read the
  block, read the register back. The interrupt handler's own reads corrupt it
  (2.6).
- Update rule per nibble n:
  `crc = (crc >> 4) ^ (((crc ^ n) & 0xF) * 0x1081)` (2.6). This is the
  CRC-16/CCITT polynomial processed 4 bits at a time, LSB first (inferred).
- The register is 16 bits (four nibbles from #104) (src:
  [[sources/checksum-crc]] Gariepy). Mastracci's "8-bit" at #104-#105 (2.6,
  4.11) is wrong; the update rule itself keeps 16 bits.
- BYTES computes its checksum this way: zero #104, read the object (src:
  [[sources/checksum-crc]] Gariepy). The hardware takes the data off the bus,
  so a copy loop checksums as it moves.

Emu48 places the register at #104-#107 and updates it on every memory read,
including the reads done by PC=(A)/PC=(C) and a read of the TIMER2 MSB
at #13F (src: [[emulators/emu48]] CHANGES SP15, SP19). Voyage says reads of the
I/O RAM do not disturb the CRC (src: [[sources/voyage-48gx]] p. 194);
the #13F case is an exception to check.

## Use outside the CPU

Libraries carry the same CRC. It does not cover the 5-nibble library prolog;
it starts at the length field after the prolog (src: [[sources/checksum-crc]]
Kaffine). For object transfer formats see [[protocols/hp-object-format]].

## Contradictions

- Width: "8-bit" and two nibbles (#104-#105) in Mastracci 2.6 / 4.11 versus
  16 bits in Gariepy and in Mastracci's own formula. Resolved in favour of 16
  bits: [[questions/crc-register-width]] (answered).
