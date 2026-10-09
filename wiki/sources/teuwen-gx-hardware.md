---
title: "Teuwen, Guide to the HP48G/GX Hardware v0.05"
type: source
authors: ["Philippe Teuwen"]
year: 1997
raw: "raw/saturn-hardware/hp48-hw-notes/hphard/hphard.txt"
status: digested
tags: [hp48, 48gx, pcb, pinout]
---

# Teuwen, Guide to the HP48G/GX Hardware v0.05

Board-level reverse engineering of the 48G/GX (v0.05, 1997-09-15). Sections
2-18 (cite by section): pin glossary, 74HC174 bank latch, 74HC00, RAM/ROM
pinouts, ports, SED1181 LCD drivers, keyboard connector and matrix, Yorke pin
list, PCB jumpers, IR receive/transmit and RS232 circuits, power. GIF/JPG
schematics alongside.

[[sources/mastracci-saturn-guide]] chapter 5 is an adaptation of v0.03 of this
document, so the two are **not** independent witnesses; v0.05 adds nothing
material that Mastracci lacks except the Vbb pin names at Yorke 146-147.

## Facts beyond Mastracci's register view

- 74HC174 latch: clock = CE1; D inputs A0-A5, outputs A17 (from A0), A18
  (A1), A19 (A2), A20 (A3), A21 (A4), BEN (A5) (4). So the bank number is
  byte-address bits 0-4 and BEN bit 5 of the address read in the CE1 window.
- CE2.2 (port 2 chip enable) = BEN AND NOT AR18; NOE2 = NOT NWE (5).
- ROM 512 KB on A0-A16 plus AR17, AR18 (6).
- Jumper pads let the GX board be built as an SX; SPD tied high = 4 MHz
  (fitted), tied low = 2.4 MHz (12).
- IR transmit on the G/GX is a single LED from +4.5 V to TXir; the S/SX uses a
  transistor driver from HP's I/O guide (14.1, 14.2).
- 32 kHz crystal on Xtal1/Xtal2 (3).
