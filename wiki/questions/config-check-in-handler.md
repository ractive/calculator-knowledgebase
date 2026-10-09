---
title: "Does the 48G/GX interrupt handler halt on an unconfigured module?"
type: question
status: open
tags: [48gx, interrupts, memory]
---

# Does the 48G/GX interrupt handler halt on an unconfigured module?

- Duchesne: the S handler checks C=ID and halts with "configuration anomaly"
  if a module is unconfigured; the G handler does not, so RPL code can
  unconfigure modules on a G (src: [[sources/duchesne-interrupts-en]] p. 10).
- Voyage (G/GX book): interrupts must be off while a module is unconfigured,
  because one of the first things the handler does is check that no module
  is unconfigured, and it forces a system halt otherwise (src:
  [[sources/voyage-48gx]] p. 185).

Duchesne shows both disassemblies side by side, which is strong evidence.
Voyage may be repeating S-era knowledge. An emulator is unaffected either
way (it runs the real ROM). Check by tracing a G ROM's #0000F path.
