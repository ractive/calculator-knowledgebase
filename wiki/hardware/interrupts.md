---
title: "Interrupt system"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/keyboard-ervin]]", "[[sources/saturn-tutorial]]", "[[sources/duchesne-interrupts-en]]", "[[sources/voyage-48gx]]", "[[sources/hp48-faq]]"]
tags: [saturn, interrupts]
---

# Interrupt system

## CPU behaviour

- On an interrupt the CPU disables further interrupts, pushes PC on RSTK and
  jumps to #0000F. An internal flag blocks further interrupts until RTI;
  interrupts arriving meanwhile set an internal pending flag that makes the
  handler run again before returning (src: [[sources/mastracci-saturn-guide]]
  2.3).
- RTI (0F) returns and re-enables interrupt detection; RSI (80810) resets the
  interrupt-detection logic (3.4).
- INTOFF (808F) / INTON (8080) disable/enable maskable interrupts, which on
  the HP48 means the automatic 1 ms keyboard scan (2.3, 4.9). The ON key is
  wired straight to the CPU and is scanned regardless (4.9).
- MP in HST is set whenever *NINTX is pulled low, whether or not an
  interrupt is taken (2.2).

## Sources

Maskable (src: [[sources/mastracci-saturn-guide]] 2.3):

- UART character received; UART transmit holding register empty
  ([[hardware/uart]])
- keyboard key down / repeat ([[hardware/keyboard]])
- timer expiry ([[hardware/timers]])
- low battery
- IR emission or receipt

Non-maskable: ON key, very-low-battery interrupt (VLBI).

Duchesne draws the line differently: only keys other than ON are maskable
(by INTOFF); ON, timer expiry with the interrupt bit set, and card
insertion/removal are non-maskable (src: [[sources/duchesne-interrupts-en]]
2.1). See [[questions/interrupt-maskability]].

## ROM handler conventions (ST bits)

| ST bit | Meaning |
| --- | --- |
| 12 | DeepSleep should stay awake (forced wake-up request) |
| 13 | an interrupt has occurred (latched; clear, then test) |
| 14 | interrupt pending; set first thing by the handler, cleared when none left |
| 15 | interrupts enabled; if clear the handler returns at once without RTI |

(src: [[sources/mastracci-saturn-guide]] 2.3)

- With ST bit 15 clear the handler returns without RTI, so the CPU still
  believes it is in the handler and ignores interrupts until RTI or RESET.
  The ON key, ON-C and ON-A-F then do nothing; only the reset button works,
  and the calculator cannot reach deep sleep (2.3, 4.9).
- The handler expects OUT to be shadowed in RAM, because OUT cannot be read
  back (2.3).
- The handler uses I/O RAM #11E as scratch (4.11) and corrupts the CRC
  register at #104 ([[hardware/crc]], 2.6).

## Details from Ervin (48SX)

- With ST bit 15 clear the handler "disables further interrupts and returns",
  using RTN rather than RTI, and sets ST bit 14 to record the unserviced
  request (src: [[sources/keyboard-ervin]] 4.1.1).
- Re-enabling correctly: set ST15; if ST14 is set, clear it and execute RSI;
  then RTI, which lets the pending interrupt be taken (src:
  [[sources/keyboard-ervin]] Appendix B, ENABLE_INTR). RSI is described there
  as resetting "the keyboard interrupt state machine".
- INTOFF stops only keyboard interrupts; ON still interrupts, as do the other
  sources (src: [[sources/keyboard-ervin]] 4.1.2).
- The ticking-clock display and due alarms are serviced whenever interrupts
  run, and these paths execute INTON (src: [[sources/keyboard-ervin]] 4.1.3).
- The low-battery path lets the handler put the machine into deep sleep to
  save RAM (src: [[sources/keyboard-ervin]] 4.1.1).
- Light sleep: write 8 to #10E, RSI, SHUTDN; on wake restore #10E to #C (src:
  [[sources/keyboard-ervin]] Appendix A). Mastracci names #10E bits 0-3
  software interrupt, set module pulled, run card detect, enable card detect
  (4.3), so the role of #10E as a wake "event mask" is open:
  [[questions/register-10e-role]].

## Interrupt-in-service flag (Giesselink)

- Single interrupt vector at #0000F. Entering it sets an internal
  "interrupt in service" flag; until RTI clears it no other interrupt is
  taken, maskable or not. Interrupts are not re-entrant (src:
  [[sources/saturn-tutorial]] p. 98).
- Maskable sources include the keyboard, timers and serial port; the ON key
  is non-maskable (p. 98).
- To disable everything, including ON, the ROM routine DisableIntr (#01115 on
  the 48, #26791 on the 49) clears ST bit 15, executes INTOFF and then
  deliberately causes an interrupt, so the CPU is left "in service" with the
  handler having returned via RTN (p. 98).
- AllowIntr (#010E5 on the 48, #26767 on the 49) sets ST bit 15, INTON, RSI if
  ST bit 14 is set, then RTI (p. 99).

## RSI and the ST flags (Voyage)

- RSI raises a new interrupt if any IN bit is active; if the CPU is already
  in service, that interrupt is taken after the next RTI (src:
  [[sources/voyage-48gx]] p. 132). This is how the ROM catches keys that were
  held while interrupts were off.
- ST bit 15 suppresses interrupt processing; bit 14 records an interrupt that
  could not be processed; bit 13 is set when an interrupt has occurred and
  been processed (p. 89).
- Interrupts take one RSTK level when ST bit 15 is clear and two otherwise,
  leaving programs 6 or 7 (p. 83).

## SHUTDN and wake-up

SHUTDN stops the CPU in low-power mode until (src:
[[sources/saturn-tutorial]] p. 100):

- a timer (1 or 2) expires with both its WKE bit and the MSB of its control
  nibble set, or
- the IN register becomes non-zero (a key recognised by the hardware scan),
  or
- the serial receive buffer raises an interrupt.

### SHUTDN with OUT = 0 (checked 2026-10-05)

The SASM manual says SHUTDN with OUT = 000 sets PC to zero on wake-up (cold
start). Observed with ROM J on the 48SX, emulator against emulator, not
hardware:

- Turning the calculator off (right shift, ON) ends in SHUTDN at #04377
  with OUT = #000 and the display off (saturnus register state).
- saturnng 6.1.1 (black box, `--debug-implementation` log) wakes from that
  SHUTDN on ON through the interrupt vector: #0000F, then RTNCC at #0001A
  back to #0437A, whose code restores OUT = #1FF. It does not jump to zero.
  saturnus does the same, and the screens after power-on match.
- The ROM has live code right after that SHUTDN, which suggests the OFF
  path expects execution to continue there (inference, unverified on
  hardware).

Voyage lists: a key whose OUT row is active, ON, or a timer reaching zero
(src: [[sources/voyage-48gx]] p. 130). The two lists agree on keys and timers;
only the tutorial mentions serial receive.

See [[hardware/timers]] for the WKE bit and [[questions/register-10e-role]]
for #10E's part in this.

## What the ROM handler does (Duchesne)

From Duchesne's reading of the S and G ROMs; the S and G handlers are the
same apart from RAM addresses (#70000 vs #80000) (src:
[[sources/duchesne-interrupts-en]] PDF p. 5, 10-15):

1. Set ST bit 14 ("inside the handler"), then branch on ST bit 15 without
   disturbing carry. With bit 15 clear it returns with RTN/RTNCC, leaving the
   CPU in service (p. 10).
2. **48S only:** execute C=ID; if any module is unconfigured, halt with
   warm-start log "configuration anomaly". The G handler skips this, so RPL
   code may unconfigure modules on a G (p. 10).
3. Save registers to a fixed RAM block (#7045C S / #805DB G), set P=0 and HEX
   mode (p. 11). The code jumps over #00100-#0013F because I/O RAM always sits
   there (p. 11).
4. If TIMER2 is stopped, halt with "Clock corrupted". Touch #10E (read before
   write); after a card removal, write #10E until MP clears, else "Module
   removed" (p. 12).
5. Very-low-battery test, possibly OFF and "Very low batteries" (p. 12).
6. Check the RAM word #A5C3F; mismatch gives "Corrupted RAM" (p. 12).
7. If transmitting on the serial port, light the I/O annunciator and
   busy-wait for the end of the character, the wait taken from a ROM table
   indexed by baud rate (p. 12).
8. IR: if "output buffer empty" and "input buffer full" interrupts are
   enabled, signal these by clearing the bits (p. 12).
9. Card write-protect switch changed: "Module removed" (p. 12).
10. Clock: add the TIMER2 delta since last write to the time offset (CRC
    checked, else "Corrupted clock offset"); check alarms; reload TIMER2 with
    1 s if the clock is displayed, else with min(1 h, time to next alarm)
    (p. 13).
11. If TIMER1 has not passed zero and a key is down, set TIMER1 to 6/16 s
    (p. 13).
12. Annunciators (low battery, shifts, alpha) (p. 13).
13. ON key: count ATTN presses; recognise ON with +, -, B, A F, 1, 4, C, D,
    E, SPC (p. 13).
14. Keyboard scan and key buffer (p. 14).
15. Restore registers, OUT from the RAM shadow (#704C3 S / #80642 G), HEX/DEC
    mode and SB (p. 10). Clear ST bit 14, then RTI (p. 14).

Consequences:

- With interrupts enabled the handler uses two RSTK levels (return address
  plus a saved C(A)); a program using more than six loses its oldest entry,
  which pops as #00000 and causes a warm restart (p. 14-15).
- Pressing a key causes an interrupt; holding it does not (except ON)
  (p. 19).
- The only way to replace the handler is to map RAM over #0000F: configure a
  RAM card or the internal RAM at #00000 (p. 16-).

## Warm-start log codes

The WSLOG command lists the last four warm starts with a cause code (src:
[[sources/hp48-faq]] 8.13): 0 log cleared (ON SPC ON); 1 low battery, deep
sleep; 2 IR hardware time-out; 3 run through address 0; 4 system time
corrupt; 5 deep-sleep wake-up (alarm?); 6 unused; 7 RAM CMOS test word
corrupted; 8 device configuration abnormal; 9 corrupt alarm list; A problem
with RAM move; B card module pulled; C hardware reset; D System RPL error
handler not found; E corrupt config table; F system RAM card pulled. Several
correspond to the handler checks above (codes 3, 4, 7, 8, B). Useful for
diagnosing an emulator that warm-starts.

## Emu48 findings

Keyboard interrupts fire on the rising edge of OR(IN[8:0]); ON is level
sensitive; RTI re-enters immediately if ON is held, NINT/NINT2 is low or a
timer interrupt is pending; INTON takes an already pending key interrupt;
SHUTDN is skipped when a wake condition is already present (src:
[[emulators/emu48]] Keyboard and interrupts, SHUTDN).

## Emulator requirements (derived)

- Implement the pending-interrupt latch: an interrupt raised while in the
  handler must re-enter after RTI.
- Keyboard interrupts must respect INTOFF; the ON key and timers must not
  (src: [[sources/duchesne-interrupts-en]] 2.1; [[sources/keyboard-ervin]]
  4.1.2). See [[questions/interrupt-maskability]].
- TIMER2 must run, or the ROM halts with "Clock corrupted" (src:
  [[sources/duchesne-interrupts-en]] p. 12).
- On a 48S/SX, every controller must report configured (C=ID = 0) whenever an
  interrupt arrives (p. 10).
- Card insertion/removal raises an interrupt and sets MP (p. 5; see
  [[questions/register-10e-role]]).
