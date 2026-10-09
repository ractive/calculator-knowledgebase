---
title: "HP 48S / 48SX"
type: model
models: [48sx]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/keyboard-ervin]]", "[[sources/screen-brittenson]]", "[[sources/bank-horn]]", "[[sources/hdwreg-taplin]]", "[[sources/teuwen-gx-hardware]]", "[[sources/saturn-tutorial]]"]
tags: [hp48, 48sx, model]
---

# HP 48S / 48SX

| Item | Value |
| --- | --- |
| Released | 48SX 1991-03-16, 48S 1991-04-02 (src: [[sources/mastracci-saturn-guide]] 1.5) |
| CPU | Saturn 1LT8 inside the Clarke IC (1.5, 2.1) |
| Clock | 2 MHz (2.1) |
| ROM | 256 KB (2.1) |
| RAM | 32 KB at #70000 (src: [[sources/saturn-tutorial]] p. 156); expandable through the card slots. Mastracci 2.1 says 128 KB for the SX, which conflicts |
| Card ports | 2, up to 128 KB each (2.1) |
| Display refresh | 64 Hz (4.2) |

## Memory map

Default configuration (src: [[sources/saturn-tutorial]] p. 156):

| Range | Contents |
| --- | --- |
| #00000-#000FF | ROM |
| #00100-#0013F | I/O RAM (HDW), covering ROM |
| #00140-#6FFFF | ROM |
| #70000-#7FFFF | 32 KB RAM (NCE2), covering the top 32 KB of ROM |
| #80000-#BFFFF | card in slot 1 (CE1) |
| #C0000-#FFFFF | card in slot 2 (CE2); unused NCE3 configured as 2 KB at #D0000, covered |

- Controllers: CE1 = port 1, CE2 = port 2, NCE3 unused (src:
  [[sources/mastracci-saturn-guide]] 2.4; [[sources/saturn-tutorial]] p. 151).
- System pointers (HOME, current directory, saved D1, flags at #706C5
  and #706D5) and the directory layout: [[hardware/hp48-system-ram]].
- ROM variables confirm RAM at #70000: #704C3-#706C3 (src:
  [[sources/keyboard-ervin]] 3.4), #70551-#70565 (src:
  [[sources/screen-brittenson]]), display ghosts #7050E-#7051B (src:
  [[sources/saturn-tutorial]] p. 166). I/O RAM #11F reads 7 (src:
  [[sources/mastracci-saturn-guide]] 4.11).
- The Clarke gates writes through CE1/CE2 with each card's write-protect
  input (src: [[sources/saturn-tutorial]] p. 160).

## Differences from the G/GX

- Port numbering: port 0 up to 288 KB, ports 1-2 single banks; bank-switched
  third-party cards are switched by user code (src: [[sources/bank-horn]]).
- IR transmitter driven through a transistor stage (src:
  [[sources/teuwen-gx-hardware]] 14.2).

## Contradictions

- RAM size: Mastracci 2.1 says 128 KB on the SX; the tutorial's default map
  has 32 KB at #70000 and Horn's port 0 maximum of 288 KB (32 + 2 x 128) fits
  32 KB built in (src: [[sources/saturn-tutorial]] p. 156;
  [[sources/bank-horn]]). Follow 32 KB.
- ROM range: the top 32 KB of the 256 KB ROM (#70000-#7FFFF) is covered by RAM
  (src: [[sources/saturn-tutorial]] p. 156); answered in
  [[questions/hp48sx-rom-extent]].

## Observed while booting ROM J in saturnus (2026-10-05)

From running the ROM in the clean-room saturnus emulator, not from a
document:

- Cold boot with zeroed RAM configures exactly the default map above: HDW
  at #00100; NCE2 size #F0000 (32 KB) at #70000; CE1 and CE2 size #C0000,
  at #80000 and #C0000; NCE3 size #FF000 at #D0000.
- With timers modelled as "expiry when the counter's MSB goes set, control
  bit 3 = MSB set and (INT or WAKE)" ([[questions/timer-expiry-semantics]]),
  INTOFF masking only the keyboard scan ([[questions/interrupt-maskability]])
  and the CRC fed by data reads only ([[hardware/crc]]), the ROM reaches
  "Try To Recover Memory?" after about 40 M cycles at 2 MHz (before
  saturnus's timing calibration of iteration 7), idles in SHUTDN
  at #0497C with TIMER2 running and WAKE set, and after NO (menu key F)
  shows "Memory Clear" over an empty stack. This supports, but does not
  prove, those three readings.
- No undefined opcode is executed on this path.
- Power-on contrast: 11 (range 3-19), observed on ROM J after a cold start
  (observed in saturnus 2026-10-07; saturnus renders it at about 90 % darkness).

System flag meanings: [[hardware/system-flags-48sx]].
