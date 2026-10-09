---
title: "Garnier, HP-42S: New Facts (2002)"
type: source
authors: ["Jean-François Garnier"]
year: 2002
raw: "raw/hp42s/garnier-hp42s-new-facts.txt"
status: digested
tags: [42s, lewis, display, registers]
---

# Garnier, HP-42S: New Facts (2002)

An HP Museum articles-forum post by J-F Garnier, written while he built a
private 42S emulator; facts from tracing the revision C ROM. The live page
was behind a bot check (HTTP 403) on 2026-10-05; the copy is the Wayback
Machine snapshot of 2025-10-11. The most direct source on the 42S hardware
found. Cite by section number.

- 1: ROM dump through the memory browser (EXIT-LOG, then ←; `.` shows the
  version A, B or C top left; EXIT-√x leaves), sent over IR to an HP 48
  running INPRT, #1000 nibbles at a time to #1FFFF.
- 2.1 Display: 131 columns; each column is 4 consecutive nibbles, 16 pixels,
  top to bottom, LSB the top pixel. Two interleaved areas: #40000 column
  0, #40004 column 66, #40008 column 1, ..., #40204 column 130, #40208 column 65.
- 2.2 Annunciators: one 5-nibble word each, 00000 off or FFFFF on: #40218
  ▲▼, #40220 shift, #40228 print, #40230 ((.)), #40238 battery, then G
  at #40240, RAD at #40248; the word at #40210 "seems to control all announciators
  globally. Should be 00000".
- 2.3 I/O registers at #40300-#4030F, "close to the HP-28S": 00 speed, 01
  contrast, 03 display?, 04-07 a 4-nibble hardware CRC, 08 battery, 0C irin?,
  0D irout?, 0E beep? (his question marks).
- 2.4 Clock: #403F8-#403FF, 8 nibbles, counting down at 8192 ticks per
  second; #403F7 "also related to the clock".
- 2.5 Keyboard: a 16-byte key buffer at #50086 (RAM, firmware).
- 3.3 XFCN: if the 5-nibble word at #20000 is #5AC3F (also the RAM test word
  at #5001A), XFCN calls #20005: a hook for an optional second ROM there,
  as on the 17BII.
- 3.4 The COMP bug involves the XM status flag, which nothing resets.

Facts used on [[hardware/hp42s]] and [[hardware/lewis]].
