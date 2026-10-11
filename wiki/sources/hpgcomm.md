---
title: "HPGComm 2.01 (2001): README, help and source"
type: source
authors: ["Jean-Pierre Bergamin", "Colin Croft"]
year: 2001
raw: "https://github.com/ractive/hpcomm"
status: digested
tags: [connectivity, transfer, kermit, xmodem]
models: [38g, 39g, 40g]
---

# HPGComm 2.01 (2001): README, help and source

HPGComm is the PC side of the 38G, 39G and 40G link: the successor of
HPComm38, shipped with HP's connectivity kit, GPL-2. Version 2.01 is dated
1 July 2001. Not in `raw/`: the source lives in the owner's private
repository (the URL above), and the README and help pages were published
with the program. The source is read under this wiki's documented
exception (CLAUDE.md, "The clean-room rule"): facts only, no code, no
routine names, no layouts.

What it covers:

- **README** (public): HP 39G and 40G support added to HPComm38; aplets
  can be uploaded to a 39G or 40G by XModem ("Recv" on the calculator,
  then "HP39/40 (Wire)"), but only aplets kept in binary format; any other
  file makes the calculator report "Transfer Failed", and must go the
  normal way. No XModem in the other direction.
- **Help pages** (public): the PC only picks the serial port and the
  directory; SEND and RECV in the Aplet Library, List, Matrix, Program and
  Notepad views drive every transfer.
- **Source** (facts read 2026-10-11): how the PC side recognises a
  "directory" on disk (the two files `HP38DIR.CUR` and `HP38DIR.000`, or
  their `HP39DIR` pair), what it does when the calculator asks for a file
  that is missing, how the first byte of an incoming transfer selects
  Kermit, XModem or a screen capture, and the XModem block and check
  choices.

## Reliability

The README and help describe the shipped program; the source is the
program itself, so it says what the PC did, not what the calculator
requires. Every fact used on [[protocols/aplet-transfer]] was checked
against a black-box run of the 38G and 39G ROMs where that was possible;
the page says which facts rest on the source alone.

Facts used on [[protocols/aplet-transfer]],
[[questions/hp38g-39g-transfer-protocol]].
