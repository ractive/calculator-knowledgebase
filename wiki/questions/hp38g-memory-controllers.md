---
title: "HP 38G: which controller drives what, and what stops a 48G emulator?"
type: question
status: open
tags: [38g, memory]
---

# HP 38G: which controller drives what, and what stops a 48G emulator?

Known: 512 KB ROM, 32 KB RAM at #F0000-#FFFFF, Yorke 00048-80063 (src:
[[sources/finseth-hp38g]]; [[sources/hp38g-ug]] 9-8). Gießelink: one
"important difference" from 48G hardware kept the 38G ROM from running on
an unmodified Emu48, without saying which (src:
[[sources/giesselink-emu48-25-years]]).

Open:

- Which controller selects the RAM (NCE2 as on the 48G?), and does the ROM
  configure it at #F0000 itself, or is it fixed?
- Are CE1 (48G bank latch), CE2 and NCE3 wired to anything? Mueller
  mentions a card connector on the board (src: [[sources/finseth-hp38g]]).
- How does the ROM use DA19 (#129 bit 3) with no port 2, and what do #10F
  (card status) and #11F read?
- How does the OS reach the ROM's top 64 K nibbles under the RAM? Mueller:
  the RAM is "mapped away temporarily" (src: [[sources/finseth-hp38g]]).
- Is the "important difference" the RAM address, or something else?

How to settle: boot the hpcalc 38G ROM A1.67 on the 48G model with RAM on
NCE2, nothing on CE1/CE2/NCE3, and log every CONFIG, UNCNFG and C=ID and
every read of #10F, #11F and #129. Context: [[hardware/hp38g]],
[[hardware/memory-controller]].

## Partial answer from a boot on saturnus (2026-10-05)

ROM A1.67 (`38G_A167.ROM`, SHA-256
`3c9f747f637757d3adc414ed14d7f3636033f34f0a72e6e453ee197987f16be7`) boots
on the clean-room saturnus emulator. saturnus models it as a 48G: RAM on
NCE2, the CE1 bank latch, DA19 as on the GX, and nothing on CE2 or NCE3.
There is no oracle, so this shows only that the ROM accepts that wiring,
not that the hardware matches it.

- **RAM.** The ROM configures HDW at #00100, then the next controller,
  NCE2 in the daisy chain, as size #F0000 at #F0000. So the ROM itself
  puts 32 KB at #F0000; the address is not fixed in hardware. It later
  configures size #FE000 twice, which suggests the RAM window is shrunk
  to reach the ROM beneath it, as Voyage describes for the 48G
  ([[hardware/memory-controller]]). This was not traced further.
- **CE1, CE2, NCE3.** These get the 48G pattern: CE1 as #FF000 at #7F000
  (the bank switcher), and CE2 and NCE3 as #FF000 at #7E000 (empty-slot
  parking). There are also passes that configure them at #C0000 and
  unconfigure them again, which looks like a card probe. Whether anything
  answers there on real hardware is still open.
- **DA19 and #11F.** At HOME, DA19 (#129 bit 3) is set, so upper ROM is
  selected, and #11F holds #F, the RAM base nibble ([[hardware/io-ram]]).
- Reads of #10F were not logged. Gießelink's "important difference" is
  still unnamed. The RAM at #F0000 does not stop this emulator, because
  the ROM configures it itself.
