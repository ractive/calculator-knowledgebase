---
title: "HP 48G / 48GX"
type: hardware
models: [48gx]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/teuwen-gx-hardware]]", "[[sources/bank-horn]]", "[[sources/saturn-tutorial]]"]
tags: [hp48, 48gx, model]
---

# HP 48G / 48GX

| Item | Value |
| --- | --- |
| Released | 1993-06-01 (src: [[sources/mastracci-saturn-guide]] 1.5) |
| CPU | Saturn 1LT8 in the Yorke IC (NEC D3004GD, HP 00048-80063) (1.5; [[sources/teuwen-gx-hardware]] 11) |
| Clock | "~4 MHz, varies with temperature" (2.1); SPD pin high = 4 MHz, low = 2.4 MHz ([[sources/teuwen-gx-hardware]] 3, 12) |
| Crystal | 32 kHz timekeeping crystal ([[sources/teuwen-gx-hardware]] 3) |
| ROM | 512 KB (2.1) |
| RAM | 48G 32 KB (expandable to 128 KB), 48GX 128 KB (2.1) |
| Card ports | 2; port 1 up to 128 KB, port 2 up to 4 MB in 128 KB banks (2.1, 4.3) |

The Yorke has an 8-bit external data bus and a 19-bit external address bus
(src: [[sources/mastracci-saturn-guide]] 2.5); the ROM and RAM chips are
byte-wide, addressed by A0-A16 plus AR17, AR18 for the ROM (src:
[[sources/teuwen-gx-hardware]] 6).

## Memory map and controllers

- Controllers: CE1 = bank-select latch (usually at #7F000), CE2 = port 1, NCE3
  = port 2 (src: [[sources/mastracci-saturn-guide]] 2.4, 4.4).
- I/O RAM at #00100; base nibble #11F reads 8 on the G/GX (4.11). RAM is
  at #80000: 32 KB to #8FFFF on the G, 128 KB to #BFFFF on the GX (src:
  [[sources/saturn-tutorial]] p. 156-157).
- Default maps per card configuration are on [[hardware/memory-controller]].
  Notably, empty slots are configured as 2 KB windows at #7E000, the bank
  switcher at #7F000 covers 2 KB of ROM, and on a GX without cards the ROM
  shows through at #C0000-#FFFFF (p. 156-157).
- Slot 1 card (CE2) and slot 2 card (NCE3) are both configured at #C0000;
  slot 1 wins by priority (p. 158).
- 4 MB cards in slot 2 always give "Invalid Card Data" because of a bank
  latch off-by-one (p. 159-160; [[hardware/memory-controller]]).
- Upper ROM and port 2 are multiplexed on Yorke pin 85 and selected by DA19
  (#129 bit 3); never both at once (4.4). DA19 = 1 is upper ROM, DA19 = 0
  port 2 with the lower ROM mirrored at #80000 (src: [[emulators/emu48]]
  CHANGES SP9; [[questions/da19-polarity]]). See
  [[hardware/memory-controller]].

## System RAM

HOME at #80711, current directory at #8071B, saved D1 at #806F8, flags
at #80843 (system) and #80853 (user), found in saturnus:
[[hardware/hp48-system-ram]].

## Board

- 74HC174 bank latch and 74HC00 glue only on the GX (src:
  [[sources/teuwen-gx-hardware]] 4, 5).
- Four solder jumpers allow the same PCB (00048-80050) to be built as an SX:
  CE1-CE2.2, NOE-NOE2, SPD-Vcc (fitted, 4 MHz), SPD-GND (2.4 MHz) (12).
- IR transmit is a single LED from +4.5 V to the TXir pin (14.1).
- More than about 120 mA through the CPU lights all annunciators regardless
  of state (18.1).

## Differences from the SX that software sees

- Port 2 banks appear as ports 2-33 (src: [[sources/bank-horn]]).
- Display, timers, UART and keyboard registers are the same layout as the SX
  as far as Mastracci describes them (4.x is written for both).

## Facts settled while building saturnus (2026-10-05)

Observed by running ROM R (`gxrom-r`, 524,288 bytes packed, SHA-256
`de3a5a07b0f00640f4ba3599ea4092e9473113aad75c04bd03d3e37c059b5b33`) on the
clean-room saturnus emulator and comparing screens with saturnng 6.1.1
(`MODEL=48gx`). This is emulator against emulator, not hardware.

- With the DA19 polarity from [[questions/da19-polarity]] (1 = upper ROM)
  and the bank latch modelled as on [[hardware/memory-controller]], ROM R
  cold boots to "Try To Recover Memory?" on the top line, YES on the first
  menu key and NO on the sixth (F). NO then gives "Memory Clear"
  over the empty stack. The boot, arithmetic, menu, alpha, OFF/ON and card
  screens match saturnng pixel for pixel.
- After that boot the OS has written 8 to #11F, as Mastracci 4.11 says
  for the G/GX ([[hardware/io-ram]] "Base nibble"). The contrast register
  holds 14, Emu48's reset value for the 48GX ([[emulators/emu48]]
  Display).
- ROM R switches port 2 banks with byte reads (two nibbles) at #7F040 +
  2n. Each one is preceded by a byte read at #7F000 (BEN = 0).
  Writes to the latch window were not seen on the PVARS paths.
- With a latch that stores exactly the nibble address bits A1-A6 of each
  read, and a 4 MB card in slot 2, `33 PVARS` gives "PVARS Error: Invalid
  Card Data", while `2 PVARS` gives an empty list and 131,027 bytes free.
  saturnng shows the same screens. The "4 MB card always gives Invalid
  Card Data" behaviour ([[emulators/emu48]] 8.6.2; tutorial p. 159-160)
  therefore appears without modelling Giesselink's three-nibble skew. The
  firmware asks for a latch value with BEN = 0 for the 33rd port. Which
  physical 128 KB of the card holds a given port was not checked.
- The card in slot 1 sits behind CE2 and is reported by #10F bits 1 and
  3. With a 128 KB card there, `1 PVARS` gives an empty list and 131,027
  bytes free on both emulators. So Mastracci's #10F port names are right
  for the GX ([[hardware/card-ports]]).
- The Kermit server started by typing ALPHA ALPHA S E R V E R ENTER (the
  same keys as on the SX) passes hptx's end-to-end suite over TCP.
- Power-on contrast: 14 (range 9-24), observed on ROM R after a cold start
  (observed in saturnus 2026-10-07; saturnus renders it at about 90 % darkness).

## Speed (2026-10-05)

The real 48GX runs User RPL about 1.7x as fast as the 48SX, not 2x. On
the summation benchmark (n = 1000) it takes 55 s against the SX's 95.5 s,
while the two ROMs execute nearly the same instruction mix (src:
[[sources/hpmuseum-summation-benchmark]]; profile in
[[questions/instruction-speed-vs-hardware]]). The 48 FAQ says the G/GX
throughput is about 40% above the S/SX "due to various overheads" (src:
[[sources/hp48-faq]] 3.2). HP gives the new CPU "twice the speed of its
predecessor ... a 4-MHz bus rate" (src: [[sources/hpj-48gx]] PDF p. 4).
The G-series cycle counts in [[hardware/saturn-cpu]] "Timing" explain
most of the gap.

System flag meanings: [[hardware/system-flags-48gx]].
