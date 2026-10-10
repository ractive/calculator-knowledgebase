---
title: "Ervin, HP48SX Keyboard Input: a guide for the ML programmer v1.0"
type: source
authors: ["Joe Ervin"]
year: 1992
raw: "raw/saturn-hardware/hp48-hw-notes/input/keybrd_input.txt"
status: digested
tags: [keyboard, interrupts]
models: [48sx]
---

# Ervin, HP48SX Keyboard Input: a guide for the ML programmer v1.0

Careful 1991/92 description of the 48SX keyboard hardware and the ROM's
keyboard service. Cite by section (2, 3.1-3.4, 4.1). Appendix A (Brittenson's
key-buffer code) and Appendix B (custom scanner from the game Vaders) are
example code.

## Key facts

- 1 ms hardware scan of the whole keyboard, no software involved; interrupt
  only if a key is down. No interrupt on key release (2).
- ROM handler schedules a 1/16 s timer interrupt while keys are held, to poll
  for releases (2). It debounces by sampling the whole keyboard every 2 ms
  until 5 identical samples (>10 ms per keystroke) and loops synchronised to
  TIMER1; holding a key costs about 75% of the CPU (3.3).
- OUT bits 8..0 drive rows 8..0; IN bits 5..0 are columns; ON is IN bit 15
  and "in a column of its own" (Figure 1, 3.1, 3.2).
- RAM structures on the 48SX: KeyBuf at #704EA (34 nibbles: get pointer, put
  pointer, 16 one-byte key codes), KeyState at #704DD (13 nibbles, one bit
  per key), ORshadow at #704C3 (3 nibbles), KBdisable at #704DC, annunciator
  shadow at #706C3 (3.4).
- Key codes: 1 = A ... #19 ENTER ... #1F 7 ... #31 +; alpha #80, left
  shift #40, right shift #C0 ORed into the next key; ON has no code (3.4.1).
- ST bit 15 clear: handler returns with RTN, not RTI, sets ST bit 14; the CPU
  stays in "interrupt" state until RTI. Re-enable with: set ST15, if ST14
  then clear it and RSI, then RTI (4.1.1, Appendix B ENABLE_INTR).
- INTOFF blocks only keyboard interrupts; ON always interrupts. The serial
  interrupt service routines execute INTON (4.1.2).
- TIMER2 is 32-bit and its rollover interrupt can be ignored for 72 hours
  (4.1.1), which with 8192 Hz is 2^31 ticks (inferred: 2^31/8192 s = 72.8 h).
- Light sleep wait: write 8 to #10E, RSI, SHUTDN, restore #10E to #C
  (Appendix A).

## Reliability

High for the 48SX; consistent with [[sources/mastracci-saturn-guide]] except
the shift keys, where Ervin's Figure 1 labels #004/IN#20 "yel" and #002/IN#20
"blu". The RAM addresses are 48SX-ROM specific.
