---
title: "HP 48G Series User's Guide, 8th edition"
type: source
authors: ["Hewlett-Packard"]
year: 1993
raw: "raw/manuals/hp48gug.pdf"
status: skimmed
tags: [manual, transfer, kermit, xmodem, iopar]
models: [48gx]
---

# HP 48G Series User's Guide, 8th edition

612-page scan without a text layer. Only chapter 27 "Transmitting and
Printing Data" (book p. 27-1 to 27-16 = **PDF p. 369-384**) and Appendix D
system flags -27 to -44 (p. D-3, D-4 = PDF 469-470) were read, as page
images. Contents at PDF 7-14; chapter 1 starts at PDF 15. Status skimmed
because the rest of the guide is user-level and was not read.

## Facts from chapter 27

- HP48-to-HP48 over IR: receiver chooses "Get from HP 48", sender "Send to HP
  48..."; ports within 2 inches (27-1).
- TRANSFER form: PORT (Wire or IR), TYPE (Kermit or XModem), NAME, FMT (ASC or
  BIN, Kermit only), XLAT (translation, Kermit only), CHK (1-3, Kermit only),
  BAUD, PARITY (Kermit only), OVRW (overwrite) (27-8, 27-9). Defaults shown:
  Wire, Kermit, ASC, XLAT Newl, CHK 3, 9600, parity None.
- Kermit to and from a PC, with the PC as server (SEND, KGET), or the PC
  sending to RECV; the HP ends server mode with SRVR FINIS (27-9 to 27-11).
- File names received: characters illegal in a name make the HP abort the
  transfer and send an error to the computer; names matching built-ins get
  `.1`; names matching an existing variable get `.1` unless flag -36 is set
  (27-11).
- ARCHIVE to `:IO:name` backs up HOME over Kermit, always binary; a ticking
  clock in the display may corrupt the backup data (27-12).
- When the HP 48 is the server it "only responds to GET (KGET), SEND, REMOTE
  DIR, REMOTE HOST, FINISH, and LOGOUT"; PKT sends one packet of a given type
  and returns the reply as a string; an error packet is shown and kept for
  KERRM (27-13).
- "The XMODEM protocol built into the HP 48 doesn't perform any CRC
  checking", but works with a CRC-capable host after the host falls back to
  checksum (27-14).
- IOPAR menu: IR/W port, BAUD 1200-9600, PARIT 0 none, 1 odd, 2 even, 3 mark,
  4 space, negative = transmit only without receive checking; TRAN 0-3
  (27-15); translation table for options 1-3 (27-16).

## Flags (Appendix D)

-33 I/O device: clear = serial, set = IR. -34 printing device: clear = IR
printer, set = serial if -33 clear. -35 I/O data format: clear = ASCII, set =
binary. -36 receive overwrite. -39 I/O messages suppressed (p. D-3, D-4).

The whole appendix (p. D-1 to D-6 = PDF 467-472) was later read for
[[hardware/system-flags-48gx]].

Facts used on [[protocols/iopar]], [[protocols/kermit-hp]], [[protocols/server-commands]], [[protocols/xmodem-hp]].
