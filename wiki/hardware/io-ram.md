---
title: "I/O RAM (hardware registers) map"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/voyage-48gx]]", "[[sources/saturn-tutorial]]", "[[sources/hdwreg-taplin]]"]
tags: [saturn, io-ram, registers]
---

# I/O RAM

The HDW module: 64 nibbles of memory-mapped hardware registers, configured by
the ROM at #00100, so the registers occupy #00100-#0013F (src:
[[sources/mastracci-saturn-guide]] 4.1). Some nibbles are read-only (R/O) or
write-only (W/O); many share a nibble between unrelated flags, so writes must
preserve the other bits (4.1).

Offsets below are absolute nibble addresses with HDW at #100. Details live on
the subsystem pages.

## Register map

| Addr | Bits | Function | Page |
| --- | --- | --- | --- |
| #100 | 0-2 / 3 | display horizontal (left) pixel offset / display enable | [[hardware/display]] |
| #101 | all | contrast, low 4 bits of 5 | [[hardware/display]] |
| #102 | 0 / 1-3 | contrast MSB / display test | [[hardware/display]] |
| #103 | all | display test; bit 3 no-refresh mode (dangerous) | [[hardware/display]] |
| #104-#107 | all | CRC, 16 bits (Mastracci shows only #104-#105) | [[hardware/crc]] |
| #108 | 0-3 | battery status: very low internal, low internal, low port 1 card, low port 2 card | below |
| #109 | 0-3 | battery test control; bit 3 starts a test | below |
| #10A | all | chip mode (R/O) | - |
| #10B | 0-3 | annunciators: left shift, right shift, alpha, alert | [[hardware/display]] |
| #10C | 0-3 | annunciators: busy, I/O, XTRA, annunciator enable | [[hardware/display]] |
| #10D | 0-2 / 3 | UART baud rate / UART clock (R/O) | [[hardware/uart]] |
| #10E | 0-3 | software interrupt, set module pulled, run card detect, enable card detect | [[hardware/card-ports]] |
| #10F | 0-3 | card present port 2, port 1; write allowed port 2, port 1 | [[hardware/card-ports]] |
| #110 | 0-3 | UART interrupt enables: rx start, rx full, tx empty; bit 3 wire serial enabled | [[hardware/uart]] |
| #111 | 0-2 | UART receive control/status | [[hardware/uart]] |
| #112 | 0-3 | UART transmit control/status, LPB, break | [[hardware/uart]] |
| #113 | all | write: clear receive error (W/O) | [[hardware/uart]] |
| #114-#115 | all | receive buffer byte (R/O) | [[hardware/uart]] |
| #116-#117 | all | transmit buffer byte (W/O) | [[hardware/uart]] |
| #118-#119 | all | interrupt type, read by the handler; #119 bit 3 = keyboard (Voyage). Mastracci: "service request" | [[hardware/interrupts]] |
| #11A | 0-3 | IR control; bit 3 also tells the 39G from the 40G | [[hardware/uart]] |
| #11B | all | "base nibble offset" (Mastracci); blank in Voyage | below |
| #11C | 0-3 | IR status / LED enable | [[hardware/uart]] |
| #11D | 0 | LED buffer | [[hardware/uart]] |
| #11E | all | scratch nibble used by the ROM interrupt handler | [[hardware/interrupts]] |
| #11F | all | RAM base nibble kept by the OS: 7 on S/SX, 8 on G/GX, #C while RAM is moved to #C0000; #F on the 38G (RAM at #F0000; observed in saturnus 2026-10-05, ROM A1.67) | below |
| #120-#124 | all | display start address (W/O) | [[hardware/display]] |
| #125-#127 | all | display line offset (W/O) | [[hardware/display]] |
| #128-#129 | | read: current LCD row, M32, DA19; write: line count before menu, M32, DA19 | [[hardware/display]] |
| #12A-#12D | | unused / undocumented | - |
| #12E | 0-3 | timer 1 control | [[hardware/timers]] |
| #12F | 0-3 | timer 2 control | [[hardware/timers]] |
| #130-#134 | all | menu (lower screen) start address (W/O) | [[hardware/display]] |
| #135-#136 | | unused / undocumented | - |
| #137 | all | timer 1 value (4 bits) | [[hardware/timers]] |
| #138-#13F | all | timer 2 value (32 bits) | [[hardware/timers]] |

(src: [[sources/mastracci-saturn-guide]] 4.2-4.11; [[sources/voyage-48gx]]
p. 192, 200 for the overview tables)

## Battery registers

- Voyage: to test the batteries write #C to #109 (bit 3 starts the test, bit
  2 stays set), then read #108: bit 3 = port 2 card battery low, bit 2 = port 1
  card battery low, bit 1 = internal batteries low, bit 0 = internal
  batteries very low. The ROM reads #108 six times in a row and treats any 1
  as low. Afterwards write #4 to #109 (src: [[sources/voyage-48gx]]
  p. 194-195).
- Mastracci names the same bits differently: #108 = VLBI occurred, LowBat0,
  LowBat1, LowBat2; #109 = RST, GRST, enable VLBI, enable LBI (src:
  [[sources/mastracci-saturn-guide]] 4.11). Both fit if bit 2 of #109 is the
  very-low-battery enable and bit 3 the low-battery test; the names
  for #108 bits 2-3 differ (card batteries vs LowBat1/2).
- An emulator can return 0 from #108 (all batteries good).

## Base nibble

#11F is described as "Base nibble (IRAM@), 7 for S/SX, 8 for G/GX" and #11B
as "base nibble offset" (src: [[sources/mastracci-saturn-guide]] 4.11).
Voyage explains it: #11F is plain read/write storage in which the OS records
the high nibble of the internal RAM base (8 normally, #C while RAM is moved
to #C0000 to reach hidden ROM). Writing it does not move RAM; routines that must
work in both states, like the display driver, read it (src:
[[sources/voyage-48gx]] p. 199). #11E is likewise scratch storage for the
interrupt handler (p. 199).

## Gaps

Still undocumented: #10A (Mastracci: "chip mode",
R/O), #11B, #12A-#12D, #135-#136. Voyage leaves them blank (src: [[sources/voyage-48gx]] p. 192,
200). An emulator should store and return writes to them.

## Contradictions

- #118-#119: "service request" (Mastracci 4.11) versus "interrupt type"
  with #119 bit 3 = keyboard (Voyage p. 198).
- #108/#109 bit names, see Battery registers.
- UART #110/#112: Taplin against Mastracci and Voyage, see
  [[hardware/uart]].
