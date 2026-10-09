---
title: "UART, serial port and IR"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/hdwreg-taplin]]", "[[sources/keyboard-ervin]]", "[[sources/buf49-flipse]]", "[[sources/serial49-sansonovski]]", "[[sources/saturn-tutorial]]", "[[sources/duchesne-interrupts-en]]", "[[sources/voyage-48gx]]", "[[sources/io-guide]]", "[[sources/hp48-faq]]"]
tags: [saturn, uart, serial, ir]
---

# UART, serial port and IR

One UART drives either the wired serial port or the IR LED; single-byte
receive and transmit holding registers (src: [[sources/mastracci-saturn-guide]]
4.5). This is the hardware hptx talks to and the emulator must model for
Kermit and XModem; see [[protocols/kermit-hp]].

## Registers

| Addr | Bits | Meaning |
| --- | --- | --- |
| #10D | 0-2 | baud rate code (table below) |
| #10D | 3 | UART clock (R/O) |
| #110 | 0 | interrupt when a receive starts |
| #110 | 1 | interrupt when the receive buffer is full |
| #110 | 2 | interrupt when the transmit buffer is empty |
| #110 | 3 | wired serial port enabled |
| #111 | 0 | character present in receive buffer |
| #111 | 1 | receiving a character |
| #111 | 2 | receive error |
| #112 | 0 | transmit buffer holds an unsent character |
| #112 | 1 | transmitting |
| #112 | 2 | LPB |
| #112 | 3 | break received |
| #113 | all | write anything: clear receive error (W/O) |
| #114-#115 | all | received byte, low nibble first (R/O) |
| #116-#117 | all | byte to send, low nibble first (W/O) |

(src: [[sources/mastracci-saturn-guide]] 4.5)

### Baud rate codes (#10D bits 0-2)

| Code | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Baud | 1200 | 1920 | 2400 | 3840 | 4800 | 7680 | 9600 | 15360 |

(src: [[sources/mastracci-saturn-guide]] 4.5). Voyage lists only four
speeds, 1200, 2400, 4800 and 9600, set by the three low bits of #10D (src:
[[sources/voyage-48gx]] p. 195). Taplin independently gives
0 = 1200, 2 = 2400, 4 = 4800, 6 = 9600, which confirms the listed order for
the even codes (src: [[sources/hdwreg-taplin]]). The odd codes are not
offered by the ROM.

Voyage confirms Mastracci's layout of #110, #111 and #112 bit for bit (src:
[[sources/voyage-48gx]] p. 196-197), and adds:

- #111 bit 0 is set by the receiver and cleared by reading the byte
  at #114-#115; bit 2 (error) is cleared by writing anything to #113 (p. 197-198).
- #112 bit 0 is set when a byte is written to #116-#117 and cleared when the
  byte has actually been sent; write only when it is 0 (p. 197-198).
- #110 bit 3 is "serial port active" (p. 196).

## Receive sequence

1. Start bit arrives: #111 bit 1 set; interrupt if #110 bit 0.
2. Byte complete: #111 bit 1 cleared, bit 0 set; interrupt if #110 bit 1.
3. Handler reads #114-#115, which clears #111 bit 0 and frees the buffer.

(src: [[sources/mastracci-saturn-guide]] 4.5 has the handler clear bit 0;
[[sources/voyage-48gx]] p. 198 says the read itself clears it.)

## Transmit sequence

1. Write to #116-#117 sets #112 bit 0.
2. UART moves the byte to the shifter: bit 0 cleared, bit 1 set.
3. Done: bit 1 cleared; interrupt if #110 bit 2; handler writes the next
   byte.

(src: [[sources/mastracci-saturn-guide]] 4.5)

## Line behaviour (HP I/O guide)

HP's own description, written for the 48SX (src: [[sources/io-guide]]):

- Frame: 1 start bit, 8 data bits LSB first, at least 1 stop bit. The HP 48
  transmits slightly more than 2 stop bits: 1 start + 8 data + 2 stop plus a
  3/16-bit internal delay, 11.375 bit times per byte, so at most baud/11.375
  bytes/s (844 at 9600) (2.2, 2.4.3).
- Mark (1) is a negative voltage, space (0) positive. With the port closed
  the TX level shifter is off and TX is shorted to ground (idle, 0 V); opening
  the port puts TX at mark (2.2, 2.4.3).
- Parity none, odd, even, mark or space; parity checking can be disabled
  while still sending parity. Parity is checked in software when bytes are
  taken from the input buffer, not by the UART (2, 2.4.1).
- Receiver: timing at 16x the baud rate from the leading edge of the start
  bit; a start bit must stay at space for half a bit time or the frame is
  aborted; data sampled mid-bit. After the stop bit the byte goes to RBR and
  RBF is set; if RBF was already set, RER (overrun) is set; a 0 stop bit also
  sets RER (framing) (2.4.1).
- A break reads as a null byte with RER and RBF set; the start filter keeps a
  long break from producing more bytes (2.4.2).
- Both directions are double-buffered (holding register plus shift
  register); receive and transmit share one baud generator and one interrupt
  (2).
- The ROM moves received bytes into a 255-byte input buffer under
  interrupts while the port is open (1, 2.4.1).
- Bit-width tolerance 2.5% on both TX and RX; TX swing at least +-3.0 V
  (3.5 V typical) into 3 kOhm and 500 pF; RX accepts +1..+15 V and -15..+0.3
  V, input impedance 5-7 kOhm (2.3).
- Gaps between incoming bytes of between 4 frame times and 4 frame times
  plus 5 ms can cause overruns, and timer interrupts (ticking clock, alarms)
  or key presses stretch the 5 ms; a sender should send back-to-back or
  pause clearly (5.1). This is the hptx pacing rule.
- When the HP48 is in self-test (ON-D, ON-E) it copies the test output to
  the serial port at 9600 8N1 regardless of IOPAR (src: [[sources/hp48-faq]]
  4.7).

## Wired port

Four contacts in the middle of the top edge, right to left: shield, TX, RX,
GND. In HP's cables these go to DB25 pins 1, 3, 2, 7 and to the DB9 shell and
pins 2, 3, 5 (src: [[sources/mastracci-saturn-guide]] 4.6).

## IR

IR is half-duplex because reflections off the window feed back (src:
[[sources/mastracci-saturn-guide]] 4.7).

HP's specification (src: [[sources/io-guide]] 3):

- 2400 baud only (2340-2460). Same framing as serial, but a 0 bit is a
  single IR pulse of 52 us (46.8-57.2 us transmitted, 40-80 us accepted) and a
  1 bit or idle is darkness.
- The receiver sets a latch (IRE) on any pulse; the receive clock samples
  and clears it mid-bit, so any pulse long enough to set IRE starts a frame.
  Framing and overrun handling as for wire, 1/16-bit resolution (3.4.2).
- Receive interrupts are disabled while transmitting to ignore reflections
  (3.4.2). While IR is in use the wired TX is held at mark and RX ignored;
  SBRK sends a train of IR pulses instead (3.4.1).
- 940 nm, maximum distance 2 in, beam +-20-30 degrees (3.3). The FAQ notes
  the transmit range is several feet; only the receiver is short-range (src:
  [[sources/hp48-faq]] 6.7).
- Noise pulses act as start bits, so garbage bytes (often #FF) appear between
  transmissions (5.1). After transmitting, wait half a bit time past the stop
  bit, then clear the receive buffer and errors (5.2).

| Addr | Bit | Meaning |
| --- | --- | --- |
| #11A | 0 | IR interrupt occurred |
| #11A | 1 | IR interrupts enabled |
| #11A | 2 | direct LED control disabled (UART drives IR) |
| #11A | 3 | latched IR sample / IR being received |
| #11C | 0 | IR buffer full |
| #11C | 1 | IR buffer empty |
| #11C | 2 | IR empty-buffer interrupts allowed |
| #11C | 3 | IR LED enable |
| #11D | 0 | LED buffer (bits 1-3 read zero) |

(src: [[sources/mastracci-saturn-guide]] 4.7)

On the 39G/40G ROM, #11A bit 3 also tells the models apart: read as 1, the
ROM shows the 40G's CAS label (observed in saturnus 2026-10-05; that a 40G
without an IR receiver reads the line high is inferred). See
[[questions/hp39g-40g-model-detection]].

Voyage gives the same bits with more detail (src: [[sources/voyage-48gx]]
p. 198-199):

- #11A bit 2 selects the IR mode. Set: IR runs as an RS-232 style link
  through the UART buffers at #114-#117. Clear: "Red Eye" mode, where software
  drives the LED directly (the HP 82240 printer protocol, unverified);
  clearing it also puts
  the UART back on the wire.
- #11A bit 3 shows IR light being received; bit 0 latches an IR interrupt;
  bit 1 enables IR interrupts.
- #11C: bit 0 IR buffer (#11D) full, bit 1 busy transmitting, bit 2 enable
  "buffer empty" interrupts, bit 3 LED on. In Red Eye mode the LED can be
  driven through #11C bit 3 or by writing pulses to #11D bit 0.

On the 49G, #11C bit 3 (called LCR, LED control register) is reused as the
flash write enable together with NCE3 (src: [[sources/saturn-tutorial]]
p. 164); see [[hardware/hp49g]].

## ROM handler behaviour

- While a character is being transmitted, the interrupt handler lights the
  I/O annunciator and busy-waits for the character to finish, using a delay
  from a ROM table indexed by baud rate (src:
  [[sources/duchesne-interrupts-en]] p. 12). So an emulated UART that finishes
  "instantly" is fine, but one that never clears the transmitting bit hangs
  the handler.
- IR: if the "output buffer empty" and "input buffer full" interrupts are
  enabled, the handler reports these events by clearing the bits; Duchesne
  describes detecting IR reception by writing a character and waiting for
  the bit to clear (p. 12).

## Interaction with the keyboard interrupt

The ROM's serial interrupt service routines execute INTON, so a program that
uses INTOFF and serial I/O must keep re-issuing INTOFF (src:
[[sources/keyboard-ervin]] 4.1.2).

## HP49G TX hardware bug

Early HP49G units (serial ID below 94xxxxxx) drive TX with a weak level that
ramps instead of switching: about -3.6 V to +4 V into 3.3 kOhm. Many older PC
serial ports misread it (src: [[sources/buf49-flipse]] p. 1-2;
[[sources/serial49-sansonovski]] p. 1). Fixes are an internal patch or an
external buffer powered from the PC's RTS, DTR and TX, which needs the host
program to assert RTS and DTR (src: [[sources/buf49-flipse]] p. 1). Not an
emulator concern; a hptx concern.

## Contradictions

Taplin's 48SX register list disagrees with Mastracci on two registers (src:
[[sources/hdwreg-taplin]]; [[sources/mastracci-saturn-guide]] 4.5):

| Reg | Bit | Mastracci | Taplin |
| --- | --- | --- | --- |
| #110 | 0 | rx-start interrupt enable | transmit interrupt enable |
| #110 | 1 | rx-full interrupt enable | transmit interrupt flag |
| #110 | 2 | tx-empty interrupt enable | receive interrupt enable |
| #110 | 3 | wired serial enabled | receive interrupt flag |
| #112 | 0 | transmit buffer full | transmit ready |
| #112 | 1 | transmitting | receive ready |

Taplin also lists the baud rate as an 11-bit register across #10D-#10F,
whereas Mastracci puts card-detect bits in #10E-#10F. Voyage sides with
Mastracci on all three registers (src: [[sources/voyage-48gx]] p. 195-197),
so Taplin's early list is wrong here. Answered:
[[questions/uart-register-bit-layout]].

## Emu48 findings

Emu48's register names: BAU #10D (UCK bit), IOC #110 (SON), RCS #111,
TCS #112 (TBF, TBZ, LPB loop-back, BRK), RBR #114, TBR #116, SRQ1 #118 (USRQ),
IRC #11A (EIRU selects IR vs wire). Clearing SON resets the UART registers;
reading RBR clears RBF; the G-series XSEND times out if TBF clears too
quickly relative to CPU speed (src: [[emulators/emu48]] UART facts).

## Facts settled while building saturnus (2026-10-05)

Read from the 48SX ROM J itself (disassembly with saturnus's disassembler,
plus a trace of every data access to #10D-#11D while booting, pressing keys
and running a Kermit server session in saturnus). These are facts about
what the ROM does, not hardware measurements.

- The handler's serial routine (#003C8) reads IOC and returns if SON (bit
  3) is clear; it clears IOC bit 2 (tx-empty enable) every time and returns
  if bits 0-1 (receive enables) are both clear. It then reads RCS, masks
  off bit 3, and returns if the rest is 0. Otherwise it executes INTOFF,
  lights the I/O annunciator (#10C bit 1) and polls RCS for RBF or RER
  (subroutine #0283D). It reads RBR as a byte (#004AE) and writes #113 when
  RER was set. It ends with INTON.
- Between bytes the routine waits with a TIMER2 timeout taken from a table
  at #0048B indexed by the #10D baud code: 300, 188, 150, 94, 75, 47, 38,
  23 ticks (1/8192 s) for codes 0-7. At each even code that is about 3.9
  frame times of 11.375 bits, which matches the I/O guide's overrun window of
  gaps between 4 frame times and 4 frame times plus 5 ms (src:
  [[sources/io-guide]] 5.1). So the baud-indexed wait is in the receive
  path. Duchesne describes a baud-indexed busy-wait while transmitting (src:
  [[sources/duchesne-interrupts-en]] p. 12); ROM J's send routine at #31416
  instead polls TCS bit 1 with a fixed loop count (#2400), then a short
  fixed delay.
- Before writing TBR (as a byte, low nibble first, #30FAA) the ROM checks
  TCS bit 0 (TBF) at #310CA and treats a set bit as busy.
- ROM J writes #8 to TCS at #317A9, so TCS bit 3 is a writable control bit
  ("send break" in Emu48's naming, [[emulators/emu48]] SP26), not only the
  "break received" status of Mastracci's table.
- Port open at #31638 writes #B to IOC: SON plus both receive interrupt
  enables, no tx-empty interrupt.
- ROM J never read #118 in the traced paths (boot, keys, Kermit server
  receiving a file); it finds serial work by polling IOC and RCS. The USRQ
  bit position in #118 stays undocumented.
- ROM J masks RCS bit 3 off after every read (#003F5, #02845); what the bit
  holds is undocumented.
- An emulated UART built from this page (one holding register and shifter
  per direction, 11.375-bit frames both ways, RBF after the stop bit,
  interrupts on the rising edge of the enabled conditions, not masked by
  INTOFF) is enough for ROM J's Kermit server to receive a file at 9600
  baud (saturnus, emulator only).

## Facts settled while building saturnus (2026-10-06)

- ROM J can take a serial interrupt it does not service: when the start
  bit's interrupt arrives while ST bit 15 is clear, the handler sets ST bit
  14 and returns with RTN, leaving the CPU in service (see
  [[hardware/interrupts]] "ROM handler conventions"). The ROM catches up
  later through AllowIntr (#010E8-#01113: ST bit 15 set, INTON, RSI, RTI).
  Seen in saturnus with the Kermit server waiting for a command, when a
  packet's first byte arrived 1-3 ms after the server's NAK.
- For the byte to be read at all, the UART's request must still count at
  that RSI/RTI: the receiver keeps RBZ, then RBF, set until RBR is read,
  and the request has to re-enter the handler while it is held. An
  emulator that treats the UART interrupt purely as an edge loses it here.
  RBR is then never read, every later byte overruns (RCS reads #5: RBF and
  RER), and the server stops answering for good. Re-entering at RTI while
  the request is held, as Emu48 documents for ON, NINT and NINT2
  ([[emulators/emu48]] SP8/SP9/SP19), makes ROM J read the bytes. At worst
  the server NAKs a packet whose first byte overran during the window.
  That the UART request behaves like NINT at RTI is inferred, not
  documented.

## Open

- LPB (#112 bit 2) is loop-back (answered: [[questions/uart-lpb-bit]]).
- Mastracci gives the UART as "up to 9600 baud" (4.5) yet lists 15360 as a
  code; the ROM only exposes 1200-9600.
