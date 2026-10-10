---
title: "Intel 28F160S5/28F320S5 5 Volt FlashFile datasheet"
type: source
authors: ["Intel Corporation"]
year: 1998
raw: "raw/saturn-hardware/datasheets/intel-28f160s5-290609-004.pdf"
status: digested
tags: [flash, datasheet]
models: [49g]
---

# Intel 28F160S5/28F320S5 5 Volt FlashFile datasheet

"5 Volt FlashFile Memory 28F160S5 and 28F320S5 (x8/x16)", preliminary,
December 1998, order number 290609-004, formerly titled "Word-Wide
FlashFile Memory Family". Fetched 2026-10-05 from the Wayback Machine copy
of Intel's developer site:
<https://web.archive.org/web/20000819001922id_/http://developer.intel.com:80/design/flcomp/datashts/29060904.pdf>.
The 49G uses the 16 Mbit part, an Intel TE28F160-S5 (src:
[[sources/saturn-tutorial]] p. 161), so this is its primary reference.
Page numbers below are the datasheet's printed page numbers.

Reliability: manufacturer datasheet, high. It describes the bare chip; how
the 49G wires it (x8 mode, VPP, WP#, how nibble writes become byte cycles)
is not in it.

## Organisation

- 16 Mbit = 2 MB, 32 erase blocks of 64 KB each; in x8 (byte) mode the
  byte addresses run #000000-#1FFFFF (Figure 4, p. 10; Table 10, p. 22).
  The 49G's 128 KB "banks" are its own bank-switch unit, two erase blocks
  each, not a chip feature.
- BYTE# low selects x8 operation on DQ0-DQ7 (Table 1, p. 7).
- Power-up, or return from deep power-down (RP# low), puts the chip in
  read array mode with the status register at #80 (3.4, p. 11; 4.1, p. 16).

## Command set (Table 3, p. 14)

Every command is one bus write; the address is "any valid address" unless
noted.

| Command | First cycle | Second cycle |
| --- | --- | --- |
| Read Array | #FF | - |
| Read Identifier Codes | #90 | reads at identifier addresses |
| Read Query (CFI) | #98 | reads at query addresses |
| Read Status Register | #70 | reads return the status |
| Clear Status Register | #50 | - |
| Write to Buffer | #E8 at the block address | count N (N+1 bytes, max 32), the data writes, then #D0 |
| Byte/Word Program | #40 or #10 | data at the target address |
| Block Erase | #20 | #D0 at an address in the block |
| Block Erase / Program Suspend | #B0 | - |
| Block Erase / Program Resume | #D0 | - |
| STS pin configuration | #B8 | code #00-#03 |
| Set Block Lock-Bit | #60 | #01 at an address in the block |
| Clear Block Lock-Bits | #60 | #D0 |
| Full Chip Erase | #30 | #D0 |

Other codes are reserved (Table 3 note 13).

## What reads return

- Read array mode returns array data and stays until another command (4.1,
  p. 16). The chip ignores Read Array while the write state machine (WSM)
  is busy, unless it is suspended.
- After #70, and automatically after program, erase, lock-bit, full chip
  erase and resume commands, every read returns the status register until
  another read-mode command (4.4, p. 24; 4.6, p. 25; 4.9, p. 26).
- Identifier mode (#90): manufacturer code #B0 at word address 0, device
  code #D0 (16 Mbit) or #D4 (32 Mbit) at word 1, and at word 2 of each block
  a lock/status byte (DQ0 = block locked, DQ1 = last erase of the block
  failed). Byte address A0 is ignored in byte mode too, so in x8 mode each
  code appears at two byte addresses (Table 12 and its note 2, p. 24;
  Figure 5, p. 13).
- Query mode (#98): the CFI table; byte reads see each byte twice ("Q",
  "Q", "R", "R", "Y", "Y" from byte #20) (4.2, p. 16-17; Tables 4-11,
  p. 17-24). For the 28F160S5: "QRY" at word #10, device size 2^21 bytes
  at word #27, 32-byte write buffer at #2A, one erase region of 32 blocks of
  256 x 256 bytes at #2C-#30, Intel extended table "PRI" at #31 (Tables 8,
  10 and 11).
- After #E8 the reads return the extended status register: XSR.7 = 1 when a
  write buffer is free (4.4, p. 25; Table 16, p. 30).

## Status register (Table 15, p. 30)

| Bit | Meaning when set |
| --- | --- |
| 7 | WSM ready (0 = busy; bits 6-0 are invalid while busy) |
| 6 | block erase suspended |
| 5 | error in block erase or clear lock-bits |
| 4 | error in program or set lock-bit |
| 3 | VPP low, operation aborted |
| 2 | program suspended |
| 1 | block lock-bit or WP# protection detected, operation aborted |
| 0 | reserved, mask it |

- Bits 5, 4, 3 and 1 are set by the WSM and only cleared by Clear Status
  (#50), so a sequence of operations can be checked once at the end (4.5,
  p. 25).
- Bits 5 and 4 both set means an invalid command sequence, for example a
  wrong confirm byte after #20 (4.6, p. 25; Table 15 notes).

## Program and erase

- Program: the WSM only reports failures where a 0 did not program; bits
  can only go from 1 to 0 (4.9, p. 26). Erase sets every byte of the block
  to #FF (4.6, p. 25).
- Lock-bits gate program and erase only while WP# is low; with WP# high
  they are overridden. Setting or clearing lock-bits needs WP# high. A
  refused program sets bits 4 and 1, a refused erase bits 5 and 1 (Table
  13, p. 29; 4.6-4.9, 4.13-4.14, p. 25-28).
- Full chip erase erases all unlocked blocks, or all blocks with WP# high
  (4.7, p. 25; Table 13).
- Write to buffer that crosses an erase block boundary, or gets anything
  but #D0 as confirm, aborts with bits 5 and 4 set; after a program or erase
  error the chip refuses further write-to-buffer commands until cleared
  (4.8, p. 26).
- Suspend (#B0) during an erase allows reads and programs in other blocks;
  status bit 6 (erase) or 2 (program) reports the suspension; resume (#D0)
  continues (4.11-4.12, p. 27-28).
- VPP below the lockout level makes every program, erase or lock operation
  fail with bit 3 set (4.6-4.9).

Used on [[hardware/hp49g]] and [[questions/hp49g-flash-write]].
