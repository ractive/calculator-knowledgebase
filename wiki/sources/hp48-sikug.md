---
title: "HP 48 to DOS/Windows PCs Serial Interface Kit User's Guide, ed. 2"
type: source
authors: ["Hewlett-Packard", "Sparcom"]
year: 1994
raw: "raw/manuals/hp48-sikug-pc-en.pdf"
status: digested
tags: [hp48, manual, kermit, pc, iopar]
---

# HP 48 to DOS/Windows PCs Serial Interface Kit User's Guide, ed. 2

148-page community scan with an OCR text layer; the English part is the first
about 25 pages, followed by other languages. Software (Link48: Filer and
Screen Capture) by Sparcom, 1994. Read the English part in full.

- Supports 48S, SX, G, GX; COM1-4; 2400 or 9600 baud (`-s2400`, `-s9600`)
  (1-2, 1-3).
- Calculator settings it expects: S/SX I/O setup IR/wire = wire,
  ASCII/binary = ASCII, baud 9600, parity none 0, checksum type 3,
  translate code 3; G/GX Transfer form PORT Wire, TYPE Kermit, FMT ASC, XLAT
  128-255, CHK 3, BAUD 9600, PARITY None (1-4, OCR partly garbled).
- File transfer requires the HP 48 in Kermit server mode; the PC lists the HP
  directory on Connect; ASCII mode (default) gives editable files, binary
  mode is faster and needed for libraries and System RPL; HP paths and names
  are case-sensitive (2-1, 2-2).
- Backslash codes \128 ... \247 for HP characters (2-4).
- Archive and Restore of the whole HP 48 memory through the server; Restore
  needs key presses on the calculator to finish (2-4, 2-5).
- Remote Command: the PC sends command lines to the server (the C host
  command) and shows the resulting stack (3-1, 3-2).
- Screen capture: wire at 9600 with flags -33 clear and -34 set (IR at 2400
  with -33 set); on the S/SX also execute PR1 to open the port; then ON-MTH
  (S/SX) or ON-1 (G/GX), the print-screen key combination, sends the screen
  as printer data (4-1, 4-2).

Feeds [[protocols/kermit-hp]] and [[protocols/server-commands]].
