---
title: "How is the HP49G flash programmed?"
type: question
status: open
tags: [49g, flash]
---

# How is the HP49G flash programmed?

Neither Smith nor Sousa describes flash writes; Sousa refuses to and warns
against "toggling the IR line and trying different controllers" (src:
[[sources/memory49-sousa]]). An emulator needs the Intel 28F160S5 command set
plus whatever enables the write line. Check [[emulators/emu48]] CHANGES and
the chip datasheet ([[sources/intel-28f160s5]]).

## Progress

Giesselink: configure NCE3 as 128 KB at #40000 and set bit 3 of #11C (LCR,
"LED control register"); the flash bank selected by the upper latch bits is
then writable through NCE3, using the flash chip's own command set (src:
[[sources/saturn-tutorial]] p. 164-165). Still open: the Intel 28F160S5
command set and status polling, which an emulator must implement for ROM
updates and port 2 writes. Source would be the Intel datasheet or
[[emulators/emu48]].

## Chip command set (2026-10-05)

The Intel datasheet is now in the library: [[sources/intel-28f160s5]]
(one-byte commands, status register polling, identifier and CFI query
data, 32 erase blocks of 64 KB). Still open for the 49G itself:

- How a byte-wide chip sees the Saturn's nibble writes. The saturnus
  emulator assumes the even-address nibble is held and the odd-address
  nibble of the same byte completes one byte cycle (low nibble first), so
  `DAT1=C B` at an even address writes one command or data byte
  (unverified). A ROM trace of the OS flash routines would settle it.
- Whether #11C bit 3 gates WE#, VPP or both, and how the boot sector is
  protected (lock-bit with WP# low would fit the datasheet's Table 13;
  unverified).

## What the ROM does (2026-10-05)

Observed on the saturnus emulator with ROM 2.15 (emulator behaviour, not a
hardware measurement), storing an object in port 2 (`42 STO :2:A`):

- The ROM opens #11C bit 3, then programs the chip with **write to
  buffer**: #50 clear status, #E8 at the block address (and reads XSR), the byte
  count, the data bytes, #D0 confirm; it then reads status (#70) and
  returns to read array (#FF). Three such buffers wrote 24 bytes into bank
  8 (port 2). It never used #40 byte program in this run.
- Every command and data byte arrived as two nibble writes at an even and
  the following odd address, low nibble first; under the emulator's
  assumption (even nibble held, odd nibble completes the byte) the ROM's
  writes form exactly the bytes the datasheet expects, and `RCL(:2:A)`
  reads 42 back, matching saturnng. This supports the byte-assembly
  assumption but does not prove how the real glue logic works.
