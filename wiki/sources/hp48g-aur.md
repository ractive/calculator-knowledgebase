---
title: "HP 48G Series Advanced User's Reference Manual, 4th edition"
type: source
authors: ["Hewlett-Packard"]
year: 1994
raw: "raw/manuals/hp48gaur.pdf"
status: skimmed
tags: [hp48, manual, iopar, kermit, commands]
---

# HP 48G Series Advanced User's Reference Manual, 4th edition

764-page scan without a text layer. Read: contents (PDF 5-24) and Appendix D
"Reserved Variables" pages D-3 to D-7 (**PDF 703-707**), which hold IOPAR.
Not read: the command reference entries for the I/O commands (chapter 3:
CLOSEIO 3-51, FINISH 3-117, KERRM 3-159, KGET 3-160, SRECV 3-318, TRANSIO
3-352, XMIT 3-381, XRECV 3-384, XSEND 3-386; SERVER, PKT, OPENIO, BAUD,
PARITY, CKSM are also in chapter 3) and Appendix C system flags (around PDF
695-702). Chapter 1 starts at PDF 25.

## IOPAR (D-5, D-6)

IOPAR in HOME is created on the first transfer or OPENIO and updated when I/O
settings change; all parameters are integers. Fields, in order: baud (1200,
2400, 4800, 9600; default 9600); parity (0 none, 1 odd, 2 even, 3 mark, 4
space; negative = parity used on transmit only; default 0); receive pacing
(non-zero enables XON/XOFF on receive, not used for Kermit; default 0);
transmit pacing (same, honours XOFF/XON; default 0); checksum CKSM (block
check requested when initiating SEND: 1, 2 or 3; default 3); translation code
TRANSIO (0-3; default 1).

Also from D-3: an alarm repeat interval is given in ticks of 1/8192 s.

Facts used on [[protocols/iopar]].

Later read: Appendix C system flags (p. C-1 to C-6 = PDF 695-700, the same
table as the User's Guide appendix D) and "Using Flags" (p. 1-42 to 1-44 =
PDF 66-68: system flags -1 to -64, user flags 1 to 64, RCLF bit order), for
[[hardware/system-flags-48gx]].
