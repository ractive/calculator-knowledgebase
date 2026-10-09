---
title: "Card ports"
type: hardware
models: [48sx, 48gx]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/bank-horn]]", "[[sources/teuwen-gx-hardware]]"]
tags: [saturn, card-ports, memory]
---

# Card ports (48SX, 48GX)

- Two ports on both the SX and the GX, modified Seiko/Epson 40-pin card
  interface. GX port 2 supports 32 banks of 128 KB (4 MB) by bank switching
  (src: [[sources/mastracci-saturn-guide]] 4.3). Bank switching itself is on
  [[hardware/memory-controller]].

## Registers

| Addr | Bit | Meaning |
| --- | --- | --- |
| #10E | 0 | software interrupt |
| #10E | 1 | set module pulled |
| #10E | 2 | run card detect |
| #10E | 3 | enable card detect |
| #10F | 0 | card present in port 2 |
| #10F | 1 | card present in port 1 |
| #10F | 2 | write allowed on port 2 |
| #10F | 3 | write allowed on port 1 |

(src: [[sources/mastracci-saturn-guide]] 4.3; note the port 2 / port 1 order)

On the 48SX this port numbering does not hold: ROM J pairs bits 1 and 3
with the CE2 window and calls that card port 2, and bits 0 and 2 with CE1
(port 1). The bits follow the chip select, so the names are right for the
GX, where CE2 is port 1. See "Checked against saturnng" below.

## Card detect

Pin 37 (CDT) tells the calculator the card type: open = empty; low = ROM
(writes disabled); high and passes the RAM size test = RAM; high and fails =
unknown device, invisible to user code but accessible from ML (src:
[[sources/mastracci-saturn-guide]] 4.3).

## Pinout

Pin 1 VDD, 2 VBB (card battery check), 3-19 A0-A16, 20 NWE, 21 CE, 22 NOE,
23-30 D0-D7, 31 A17 and 32 NA18 (port 2), 33-36 XSCL, LP, LD0, LD1 on port 1
or A19, A20, A21, BEN on port 2, 37 CDT, 38-39 NC, 40 GND (src:
[[sources/mastracci-saturn-guide]] 4.3, 5.6). Cards are byte-wide.

## Ports as the OS sees them

- Port 0 is main RAM, sized to its contents: up to 256 KB on the GX and
  288 KB on the SX (src: [[sources/bank-horn]]).
- Port 1 is the card in slot 1, 32 or 128 KB, identical on SX and GX; the
  overhead-projector interface only works there (src: [[sources/bank-horn]]).
- GX slot 2: a card of 128 KB or more is split into 128 KB banks, each shown
  as a separate port, port 2 to port 33 for a 4 MB card. A 32 KB card is just
  port 2. Slot 2 cannot be MERGEd (src: [[sources/bank-horn]]).
- SX: third-party 256 KB and 512 KB cards are bank-switched by user programs;
  only the active bank is visible (src: [[sources/bank-horn]]).

## GX port 2 chip enable

Port 2's chip enable CE2.2 is BEN AND NOT AR18, built from a 74HC00 (src:
[[sources/teuwen-gx-hardware]] 5), which is why upper ROM and port 2 are
exclusive ([[hardware/memory-controller]]).

## Checked against saturnng (2026-10-05)

Black-box comparison of saturnng 6.1.1 (48SX, ROM J) with the clean-room
saturnus emulator, with empty slots unless noted; emulator against emulator, not
hardware.

- With #10F reading 0 (no card present, no write enable) saturnus follows
  saturnng's cold boot instruction for instruction, including the CE1, CE2
  and NCE3 CONFIG/UNCNFG sequence, and both show the same screens after
  "Try To Recover Memory?" NO, `6 ENTER 7 * ENTER`, a menu walk and an
  OFF/ON cycle.
- The ROM never reads the empty slot windows on those paths, so the value
  an empty slot returns is not observable this way; see
  [[hardware/memory-controller]] "Checked against saturnng" for the
  experiment that would settle it.
- The saturnng container inserts a 128 KB RAM card by default (file
  `port1`); run it with `CARDS=0` for an empty-slot comparison. The 48SX
  ROM finds that card behind CE2 and reports it as port 2: `2 PVARS` gives
  `{ }` and the free bytes, `1 PVARS` gives "Port Not Available".
- How ROM J finds RAM cards (its own code, #09A18-#09A63 and #01DA1;
  saturnng's instruction log agrees): it reads #10F; if bit 1 (present)
  and bit 3 (write) are set it RAM-tests the window at #C0000 (CE2), then
  it shifts the nibble left one bit and tests the window at #80000 (CE1)
  the same way for bits 0 and 2. The test at #01DA1 saves the nibbles at
  base+#3FFFB, writes #FFFFF and #00000 and reads each back, then compares
  base+#0FFFB with base+#3FFFB to tell a 32 KB from a 128 KB card (the
  result is the text "128K"). A card whose window fails the write/read-back
  is ignored.
- A RAM card present at power-on is not merged into user memory: `MEM`
  stays at 30269 bytes after Memory Clear with or without a 128 KB card,
  in both emulators. It is an independent port.

## Checked on the 48GX (2026-10-05)

saturnus, emulating the 48GX with ROM R, was compared with saturnng
(emulator against emulator). With #10F bits 1 and 3 assigned to the card
behind CE2 (port 1) and bits 0 and 2 to the banked NCE3 card (port 2),
the ROM finds a 128 KB card in port 1 and a 4 MB card in port 2. `1
PVARS` and `2 PVARS` match saturnng, as does the "Invalid Card Data" for
`33 PVARS`. The pairing "follows the chip select" therefore holds for the
GX too ([[hardware/hp48gx]] "Facts settled while building saturnus").
