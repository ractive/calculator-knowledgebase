---
title: "Emu48: hardware behaviour it encodes"
type: emulator
emulator: Emu48
status: draft
tags: [emu48, emulator, hardware]
sources:
  - "[[sources/emu48-manual]]"
  - "[[sources/emu48-changes]]"
  - "[[sources/kml20]]"
  - "[[sources/io-guide]]"
---

# Emu48

Christoph Gießelink's GPL emulator of the HP38G, 39G, 40G, 48SX, 48GX and
49G (src: [[sources/emu48-manual]] 1). Facts only: what the hardware does
according to Emu48's documentation and change log. No code was read
(clean-room rule in CLAUDE.md). Citations to the change log use service-pack numbers because
CHANGES.TXT has no dates ([[sources/emu48-changes]]).

## What it emulates

- Calculators based on the Clarke (48SX) and the Yorke chip (all others)
  (src: [[sources/emu48-manual]] 1).
- KML `Hardware "Yorke"`; Model letters: `S` 48S/SX, `G` 48G/G+/GX, `A` 38G,
  `6` 38G with 64 KB RAM, `E` 39G or 40G (Class 39 or 40), `X` 49G (src:
  [[sources/kml20]] Global section).
- ROM images packed (even address in the low nibble of a byte) or unpacked
  (one nibble per byte) (src: [[sources/emu48-manual]] 3).
- ROM dumps made by upload programs contain the I/O register window mapped
  over the ROM; the Convert tool zeroes that area, otherwise the self-test
  reports an IROM failure (3). The 49G ROM image is built from a `.flash`
  update file and has an empty user port 2 (3.1).
- Ports: an optional 128 KB RAM card in port 1 with a write-protect switch;
  a port 2 file of up to 4 MB shared by S/SX and G/GX models; an SX sees
  only the first 128 KB of a larger card; a 4 MB card always gives "Invalid
  Card Data" on a GX, a firmware bug, and port 33 is unreachable (8.6.2).
  The 49G instead has an internal 128 KB RAM "card" (src:
  [[sources/emu48-changes]] SP14).

## Serial, IR and printer model

- Wire and IR are each mapped to a host COM port; the IR port works only at
  2400 baud (src: [[sources/emu48-manual]] 8.6.3.3).
- The HP 82240 IR printer is a separate path: bytes go over UDP to a printer
  simulator (8.6.3.2; [[sources/emu48-changes]] SP51).
- The IR control register bit EIRU (in #11A) switches the UART between IR
  and wire; changing it re-initialises the port (SP22, SP65).
- Host side uses 2 stop bits, "closer to the original 2-3/16 stop bits"
  (SP33; compare [[sources/io-guide]] 2.4.3).

## UART facts (register names as Emu48 uses them)

| Reg | Name | Bits Emu48 implements |
| --- | --- | --- |
| #10D | BAU | baud code; UCK (UART clock) bit, always set when the UART is off (SP14, SP42) |
| #110 | IOC | SON (serial on) and interrupt enables (SP21) |
| #111 | RCS | receive status incl. RX and RER; RER on framing error and on a received BREAK (SP21, SP26, SP28) |
| #112 | TCS | TBF (transmit buffer full), TBZ, LPB (loop back), BRK (send break) (SP15, SP26, SP28) |
| #114-#115 | RBR | received byte; reading it clears RBF (SP65) |
| #116-#117 | TBR | byte to send; reading returns the last written byte (SP21) |
| #118 | SRQ1 | USRQ (serial service request), TSRQ (timer service request) (SP15, SP21) |
| #119 | SRQ2 | KDN (key down), NINT2, NINT (SP10, SP15, SP19) |

- Clearing SON in IOC clears IOC, RCS, TCS, RBR and TBR; writes to RCS, TCS
  and TBR only work while SON is set (SP21, SP42).
- USRQ is only set on a serial interrupt condition while the UART is enabled,
  and is re-evaluated after reading RBR or writing IOC or TBR; writing IOC
  can raise a pending serial interrupt (SP15, SP21, SP26).
- **LPB is loop-back**: with it set, transmitted bytes come back into the
  receiver (SP15, SP22, SP28). Answers [[questions/uart-lpb-bit]].
- The HP48G-series XSEND command polls TBF with a software timeout tuned to
  real CPU speed; Emu48 slows the emulated CPU on TBF reads so the timeout
  does not overflow (SP66, SP67). An emulator that runs much faster than
  real time must do something similar.

## Timers

- When a TIMER2 interrupt is pending, reading TIMER2 returns #FFFFFFFF
  (SP43). The timer2 event is tied to a change of the counter's most
  significant bit (SP8), i.e. the interrupt fires when TIMER2 counts down
  through zero into #FFFFFFFF. Answers
  [[questions/timer-expiry-semantics]] for TIMER2.
- TIMER1 decrements after a full period; reloading it with the value it
  already has does not restart the period (SP4, SP12). TIMER1 only runs while
  TIMER2 runs (SP39).
- TIMER2CTRL's RUN bit also governs the annunciators: they are off while
  TIMER2 is stopped (SP19). Setting TIMER1CTRL must not clear its XTRA bit
  (SP9). Control bits are re-evaluated after every timer read or write
  (SP8, SP9).
- The ROM measures CPU speed against TIMER2 at warm and cold start and
  stores it in =CSPEED, which scales beep frequency and duration; Emu48
  detects that read pattern (SP51; [[sources/emu48-manual]] 8.6.3.1).

## Keyboard and interrupts

- Keyboard interrupt sources: ON (non-maskable), a key found by the 1 ms
  scan when enabled (maskable), and a key found after an OUT or after RSI
  when an IN bit is set (SP4).
- The keyboard interrupt is triggered by the rising edge of the OR of
  IN[8:0], not by each line; the ON interrupt (IN bit 15) is level
  sensitive (SP16).
- A stopped timer does not stop A=IN/C=IN from reading the keyboard, but it
  stops the 1 ms poll and its interrupt (SP31). KDN in SRQ2 is updated by
  the 1 ms poll and by A=IN/C=IN (SP10, SP31).
- INTON and INTOFF never generate an interrupt themselves (SP4), but INTON
  takes a key interrupt that is already pending (SP10). RSI raises or marks
  pending an interrupt if an IN bit is high (SP4, SP10).
- RTI re-enters the handler at once if ON is pressed, if NINT or NINT2 is
  low, or if a timer interrupt is pending (SP8, SP9, SP19).
- A released ON key must clear IN bit 15 even when keyboard reading is off
  (SP43).
- A=IN and C=IN only work at even addresses (Emu48 reproduces this "Saturn
  bug"), except when executed from the I/O register window, where they also
  work at odd addresses (SP1, SP36). Settles
  [[questions/c-equals-in-even-address]].

## SHUTDN

- SHUTDN does not stop the CPU if an interrupt request or a timer wake
  condition is already present, or if ON is held; IN must be refreshed
  before the check (SP8, SP18, SP43, SP50).
- Wake-up sources: keyboard, serial, timers (SP14).
- SHUTDN also resets the GX bank-switch flip-flop, as does CPU reset (SP23).

## Memory controller

- DA19 (#129 bit 3) on a G-series ROM: **DA19 = 0 disables upper ROM**; the
  lower 256 KB of ROM is then mirrored at #80000 (AR18 = 0), and port 2 is
  selected only when DA19 = 0 and BEN = 1 (SP9). DA19 = 1 gives A19 to the
  ROM. Clarke and Yorke behave the same; what matters is the ROM size (SP16,
  SP19). Answers [[questions/da19-polarity]].
- The I/O window is 64 nibbles and starts on a 64-nibble boundary; C=ID uses
  the saved I/O base (SP8).
- Every window begins and ends on a boundary of its mapping size (SP5).
- Unmapped addresses read as an "open data bus": fixed values for even and
  odd addresses (SP11, SP16).
- The GX bank switcher latches on any read in its whole configured window,
  but not when RAM or CE2 (higher priority) owns the same address; an
  unconfigured bank-switch window is inactive; the S/SX have no bank
  switcher (SP9, SP11).
- Port 2 is mapped 128 KB at a time (SP27); 32 KB cards are valid (SP25).
- Addresses wrap at #FFFFF (SP31); instructions may straddle a 2 KB mapping
  page, the longest opcode being 21 nibbles (SP42, SP50); code can execute
  from the I/O window (SP9).
- CE1/CE2 access and unconfigure priority were corrected once for SX (port 1
  vs port 2) and GX (port 1 vs bank select) (SP5); the entry does not state
  which wins, so [[questions/bus-priority-ce1-ce2]] stays open.
- 49G: NCE3 can map the flash (SP16); toggling the LED bit in LCR (#11C)
  re-maps memory (flash write enable) (SP16); bank latch line A6 selects the
  upper half of the flash (SP15); flash is an Intel 28F160 command-set model
  with query table and block-lock status bits (SP17, SP28, SP47, SP51).
  ROMs smaller than 2 MB are mirrored (SP19). (With the bit order the ROMs
  need, A4 is the top bit of the high-window bank. Emu48's A6 may number
  the latch differently; see [[questions/hp49g-bank-latch-bits]].)

## I/O register details

- CRC register is #104-#107; reading the TIMER2 MSB (#13F) updates it, and
  PC=(A) / PC=(C) update it because they read memory (SP15, SP19).
- LPD (#108): LB0 and VLBI bits; LPE (#109): RST, ELBI, EVLBI bits (SP31,
  SP43).
- LPE's RST bit is set by power-on reset and by the NRES reset pin and is
  cleared when LPE is read (SP31). Reads of LPE and RBR have side effects,
  peeks do not (SP43).
- CARDCTL (#10E): SMP, SWINT, ECDT bits; CARDSTAT (#10F) reads 0 when card
  detection is disabled; a card change sets MP and pulls NINT low (SP16,
  SP19). Partial answer to [[questions/register-10e-role]].
- LCR (#11C): LED and ELBE bits; LBR (#11D): LBO bit (SP51).

## Display

- The line counter counts down 63 ... 0 (SP7, SP38); when the display is
  switched on it starts from the LINECOUNT value (SP30).
- Nothing is drawn while DON (#100 bit 3) is clear (SP31).
- Writes to the display address, line offset, line count and menu address
  registers take effect nibble by nibble (SP7). Negative line offsets are
  valid (SP7). A LINECOUNT of zero needs special handling (SP22).
- Contrast: 5 bits, 0-31 in hardware; reset value and keyboard range per
  model (src: [[sources/kml20]] LCD):

| Model | Reset | Keyboard min | Keyboard max |
| --- | --- | --- | --- |
| 48SX | 11 | 3 | 19 |
| 48GX | 14 | 9 | 24 |
| 49G | 14 | 9 | 24 |
| 38G | 14 | 9 | 24 |
| 39G, 40G | 12 | 9 | 24 |

The reset values match what the ROMs themselves write at a cold start:
48SX J 11; 48GX R, 38G A and 49G 14; 39G/40G 12 (observed in saturnus
2026-10-07).

## CPU

- `r=r+CON` / `r=r-CON` always operate in hexadecimal, regardless of
  SETDEC (SP10), and with a single-nibble field selector they overrun into
  the rest of the register (SP1). See [[questions/dec-mode-constant-bug]].
- The rotate instructions also update SB (SP35).
- BUSCB, BUSCC and BUSCD are no-ops on these calculators (SP8).
- Cycle counts differ between the S/SX and the G series (SP1); several
  counts were corrected over time (SP15, SP31, SP34).
- The object at #02BAA is DOACPTR on the G series but DOEXT1 on the S series
  (SP48).

## Objects

- Load Object accepts files starting `HPHP48-x` (48) or `HPHP49-x` (49G), x
  any alphanumeric character; anything else is loaded as a string (src:
  [[sources/emu48-manual]] 9.1).
- The DDE clipboard format "CF_HPOBJ" is a 4-byte length (LSB first) followed
  by the HP object (13).

## Beeper

The beeper is driven by OUT bit changes and emulated from them (SP55); there
is no longer a ROM patch for sound (SP63; [[sources/emu48-manual]] 8.6.3.1).
