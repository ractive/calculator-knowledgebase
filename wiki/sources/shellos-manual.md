---
title: "ShellOS 3.0 beta 2.4 user manual"
type: source
authors: ["Stephane Cocquereaumont", "Sébastien Munch"]
year: 1999
raw: "homebrew/shellos-manual-en.pdf"
status: digested
tags: [rom, homebrew]
models: [48gx]
---

# ShellOS 3.0 beta 2.4 user manual

The 12-page English user manual of ShellOS, a third-party operating system
for the HP 48GX by Stephane Cocquereaumont and Philippe Dedieu (manual by
Cocquereaumont and Sébastien Munch, PDF dated June 1999). From
`shdocsen.zip` on hpcalc.org (details page 80); text conversion next to it.
Read in full on 2026-10-12. Cite by manual page.

- ShellOS is a multitasking OS that lives on a 32 KB or 128 KB RAM card in
  slot 1 of a 48GX. The authors state it uses no part of the HP 48 ROM and
  is independent of the built-in OS (p. 2).
- GX only: the manual says this version will not work on the 48G, G+, S or
  SX (p. 4).
- Installation runs an `Install` program from the RPL side; port 1 is
  overwritten, port 0 and user memory are kept. `RPLswitcher` goes back to
  the built-in RPL OS; `STARTSHELL` under RPL switches to ShellOS (p. 4,
  12).
- Uninstalling means switching back to RPL and storing any object in the
  card (p. 4), which overwrites it.
- Features listed include a 4x6 font, a double-buffered display, fast bank
  switching, screen splitting, a clipboard and about 100 documented
  routines for assembly programmers (p. 2-3), a key for a warm restart
  (p. 5), a startup file and a splash picture read at every start, and an
  XModem transfer program (p. 12).
- A file `RPLcomp` run at startup "patches" the kernel so that accesses to
  an RPL port are redirected to it (p. 12).

What the manual does **not** say: how ShellOS takes the machine over at
the hardware level (which controller it maps where, whether it installs
its own handler at #0000F), and whether it survives a reset without the
RPL OS. The program has no source and no licence statement on hpcalc.org
(details page 79).

## Reliability

Authors' own user manual of a beta. Good for what the system offers and
how it is installed; silent on internals.

Facts used on [[questions/custom-rom-for-saturn-calculators]].
