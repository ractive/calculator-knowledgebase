---
title: "Mastracci, Guide to the Saturn Processor (With HP48 Applications) Rev. 1.0b"
type: source
authors: ["Matthew Mastracci"]
year: 1998
raw: "raw/saturn-hardware/saturnd/saturn.txt"
status: digested
tags: [saturn, cpu, io-ram]
models: [48sx, 48gx]
---

# Mastracci, Guide to the Saturn Processor

Compiled reference to the Saturn CPU and the HP48 S/SX/G/GX peripherals, rev.
1.0b of 1998-02-20 (1.4). Plain text, about 2000 lines; cite by section number
since there are no pages. The `.doc` in the same directory is the same file.

## Coverage

| Section | Content | Use |
| --- | --- | --- |
| 1.5 | Saturn chip versions per calculator (1LF2, 1LK7, 1LT8) and companion ICs (Lewis, Clarke, Yorke) | model pages |
| 2.1 | Chip specifications: clock, ROM/RAM sizes per model | model pages |
| 2.2 | Register set, HST bits | [[hardware/saturn-cpu]] |
| 2.3 | Interrupt entry, ST bits 12-15, interrupt sources | [[hardware/interrupts]] |
| 2.4 | Daisy-chain CONFIG/UNCNFG/RESET/C=ID, module IDs, priority | [[hardware/memory-controller]] |
| 2.6 | CRC register and formula | [[hardware/crc]] |
| 3.1 | Field selectors | [[hardware/saturn-cpu]] |
| 3.2, 3.3 | Marked "[incomplete]"; only PC=(A), PC=(C) | - |
| 3.4 | Chip-interface opcodes with encodings | [[hardware/saturn-cpu]] |
| 4.1-4.11 | I/O RAM registers #100-#13F: display, card ports, bank switching, UART, IR, speaker, keyboard, timers, power | [[hardware/io-ram]] and subsystem pages |
| 5.x | PCB pinouts (Yorke pins, ports, 74HC174 bank latch, LCD drivers, keyboard connector), from Teuwen v0.03 | [[hardware/hp48gx]] |
| 6.x | Example code: greyscale, beep, direct key read, interrupt-handler takeover. 6.5-6.7 are empty | partly |

## Reliability

Secondary compilation from Gariepy, Nickel, Brittenson, Ervin, Teuwen, Duchesne,
x48 source and newsgroup posts (1.3). Marked "pre-release". Several slips are
visible:

- 4.9 says the ON key returns "in bit 15 of OUT"; the key code in 6.3 shows it
  is bit 15 of the value read with C=IN, so IN is meant.
- 4.8 says the speaker uses "the upper two bits" of OUT but only lists #8xx
  (bit 11).
- 4.2 lists #128/#129 twice (read: line count, write: screen height); this is
  intentional, the register is dual-purpose.
- 2.2 says RSTK is an "eight-level FIFO"; a return stack is LIFO.
- 2.2 lists the HST bits as XM, SR, SB, MP; HP's SASM manual has SB as bit 1
  and SR as bit 2 ([[questions/hst-bit-order]]).
- 2.1 gives the 48SX 128 KB of RAM; the tutorial's memory map and Horn's port
  sizes imply 32 KB ([[hardware/hp48sx]]).
- 2.6 calls the CRC "8-bit" at #104-#105, but Gariepy says 16-bit and the
  formula itself keeps 16 bits; see [[hardware/crc]].
- 4.9 table swaps the two shift keys relative to Ervin, Teuwen and the
  tutorial; see [[questions/keyboard-matrix-shift-keys]].
- 2.4 says sizes are multiples of #100; Giesselink (tutorial ch. 66) says the
  controllers ignore A11-A0, so the unit is #1000.

Use it as an index and cross-check every register against
[[sources/voyage-48gx]] and [[sources/hdwreg-taplin]].
