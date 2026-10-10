---
title: "What do the HP 48S/SX system flags mean?"
type: question
status: answered
tags: [flags, rom]
models: [48sx]
---

# What do the HP 48S/SX system flags mean?

**Answered 2026-10-05.** The HP 48SX Owner's Manual is in the library now
([[sources/hp48sx-om]]); its appendix E lists every system flag -1 to -64
with the meaning of clear and of set, and [[hardware/system-flags-48sx]]
is transcribed from it, with nothing carried over from the 48G.

What was asked, and the answers (src: [[sources/hp48sx-om]] p. E-1 to
E-7):

1. -30 on the S/SX: function plotting (whether y is plotted next to f(x)
   for an equation y = f(x)). -59: the Equation Catalog's display (equation
   and name, or name only).
2. The I/O flags -33 to -39 and the keyboard flags -60 to -63 have the same
   meanings as on the 48G. Apart from -30 and -59 the differences are the
   flags the S/SX does not use (-14, -27, -28, -29, -54), as the HP 48 FAQ
   says (src: [[sources/hp48-faq]] 10.1), and the wording of -40 and -58.
3. 64 system flags: yes, the appendix ends at -64. The appendix does not
   count the user flags; the ROM keeps one 64-bit word of each kind
   ([[hardware/hp48-system-ram]]).

The bit order of the word-size flags -5 to -10 and of the digit-count
flags -45 to -48 is in neither manual. Inferred from saturnus's decompiler
matching the ROM's display (2026-10-05): the lowest-numbered flag is the
least significant bit. -5 is bit 0 of the word size minus 1, and -45 is
bit 0 of the digit count.
