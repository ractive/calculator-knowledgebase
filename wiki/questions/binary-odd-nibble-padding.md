---
title: "HPHP48 binary files: how is an odd nibble count padded?"
type: question
status: open
tags: [object-format, satx]
---

# HPHP48 binary files: how is an odd nibble count padded?

Objects are nibble sequences and binary transfer files are bytes (src:
[[sources/hp48-faq]] 8.18). When an object has an odd number of nibbles the
last byte holds one spare nibble. Which value does the calculator send, and
does it accept either on receive? OBJFIX tolerates extra trailing bytes
(9.2), which suggests length comes from the object. Check
[[sources/hp48-kermit-hints]], the Conn4x FixObj.pas notes (facts only) and
the Emu48 object loader behaviour.

Conn4x's object-fixing step walks the object from nibble 16 and keeps ceil(nibbles/2)
bytes, so the spare half-byte is kept but its value is not checked (src:
[[sources/conn4x-ymodem-pas]], FixObj.pas). The value the calculator writes
there is still unknown.

## Progress (2026-10-08)

Every binary GET from the 48SX J, 48GX R and 49G 2.15 had 0 in the spare
half byte and no further padding (saturnng, satx; see
[[protocols/hp-object-format]]). On receive the object's own length
decides: exactly ceil(nibbles / 2) bytes after the header store the
object; the 48SX stores the file as a string if one more byte follows, the
49G (2009 ROM) accepts it (saturnus 2026-10-05). A file fetched and sent
back unchanged gives the same bytes (saturnus, iteration 12b). Still
unknown: whether a non-zero spare nibble is accepted.
