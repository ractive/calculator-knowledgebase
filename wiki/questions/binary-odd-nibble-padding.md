---
title: "HPHP48 binary files: how is an odd nibble count padded?"
type: question
status: open
tags: [object-format, hptx]
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
