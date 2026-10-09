---
title: "Add/subtract-constant: DEC-mode bug or always-hex with field overrun?"
type: question
status: open
tags: [saturn, cpu]
---

# Add/subtract-constant: DEC-mode bug or always-hex with field overrun?

- Tutorial: in DEC mode, add/subtract of a constant greater than one on the
  S, XS, WP or P fields acts on the whole register and propagates carry;
  "in hexadecimal mode, however, everything works as expected" (src:
  [[sources/saturn-tutorial]] p. 59-60).
- Emu48: the r=r+CON and r=r-CON opcodes always work in hex mode, whatever
  SETDEC says (SP10), and commands with a single-nibble field selector overrun
  into other fields of the register (SP1) (src: [[emulators/emu48]]).

The two agree that single-nibble fields overrun; they differ on whether the
constant forms honour DEC mode. Emu48 is backed by running real ROMs. Test on
hardware or with a ROM trace before implementing.

## Answer (working)

Always hexadecimal: the SASM manual 2.7 lists every `r=r+CON` / `r=r-CON`
form among the instructions that ignore SETDEC (src:
[[sources/sasm-reference]] 2.7), which agrees with Emu48 and refutes the
tutorial's "DEC-mode bug" framing. The overrun is modelled in saturnus as a
nibble-serial add starting at the field's nibble and running through all 16
nibbles circularly (nibble 15 wraps to nibble 0); that reproduces both
tutorial examples on p. 59 (`FFFFFFFFFFFFFEF2` + 4 on XS gives
`00000000000002F3`). Still open: whether the overrun applies for a constant
of 1 and what carry reads after an overrun. SASM refuses S, P, WP and XS on
the constant forms ("rfs"), so HP ROM code never executes them; only a
hardware test with hand-assembled code can settle the rest.
