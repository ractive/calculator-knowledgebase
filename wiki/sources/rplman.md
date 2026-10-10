---
title: "RPLMAN: HP's RPL manual (System RPL object formats)"
type: source
authors: ["Hewlett-Packard"]
year: 1991
raw: "raw/saturn-hardware/hp-tools-1991/RPLMAN.DOC"
status: skimmed
tags: [object-format, rpl]
models: [48sx, 48gx]
---

# RPLMAN: HP's RPL manual

HP's own System RPL reference from the 1991 HP 48 tools (`RPLMAN.DOC`, plain
text, page numbers in the file). Only chapter 3, "Object types" (pages 18-28,
around lines 1400-1960 of the file), has been read so far, for the object
bodies a host must decode (saturnus iteration 9). Authoritative for the 48
series; it predates the 49G and says nothing about its integer, long real or
matrix types.

- Real number (DOREAL): 16 BCD nibbles, from low memory `EEE`, 12 mantissa
  digits, `S`. Sign 0 nonnegative, 9 negative; implied decimal point after
  the first mantissa digit, which is nonzero unless the number is 0;
  exponent in tens complement, -500 < EEE < 500 (p. 20-21).
- Extended real (DOEREL): 21 nibbles, `EEEEE`, 15 digits, `S` (p. 21).
- Complex (DOCMP): two real bodies, real part first (p. 22).
- Array (DOARRY): length field, type indicator (the elements' prolog
  address), dimension count, one length per dimension, then element bodies
  without prologs in lexicographic index order (p. 23).
- Character string (DOCSTR): length field and bytes. Hex string (DOHSTR):
  length field and nibbles; hex strings of 16 nibbles or fewer represent the
  user's binary integers (p. 25).
- Binary integer (DOBINT, the system one): a 5-nibble number (p. 20).
- Unit (DOEXT): a real, then unit name strings, prefix characters,
  unit operators and real powers, ended by SEMI (p. 26).
- Identifier (DOIDNT) and temporary identifier (DOLAM): a one-byte character
  count, then the characters (p. 18-19).

Field order and nibble order are as on [[protocols/hp-object-format]].

Also read (saturnus iteration 12c), in the SDK copy
`raw/saturn-hardware/hp48-sdk-1993/RPLMAN.TXT`: chapter 2.5-2.6 (p. 12-13)
on libraries, XLIB names and commands: a built-in command object is
preceded in ROM by the 6-nibble body of its XLIB name, which the
decompiler uses to find the name; commands start with CK0 or one of
CK1&Dispatch-CK5&Dispatch. Chapter 3.1.3 (p. 19-20): the XLIB body is two
12-bit fields, library and command. See [[protocols/rpl-libraries]].
