---
title: "Sousa, HP49 Memory Explained v1.00"
type: source
authors: ["Fernando Steve Domingues Sousa"]
year: 2000
raw: "raw/saturn-hardware/hp49-38-39/memory49/memory49.txt"
status: digested
tags: [hp49g, memory, flash, bank-switching]
---

# Sousa, HP49 Memory Explained v1.00

Reading the HP49G flash from ML (2000-06-18), derived from a circuit diagram;
the author did not own a 49. Includes a Giesselink newsgroup post of
2000-03-07.

- Flash chip: one Intel 28F160S5 (2 MB). The Yorke addresses only 512 KB, so
  the upper flash address lines come from the external bank-switch flip-flop,
  128 KB per bank (Giesselink quote).
- Two views: view 1, controller NCER at #00000-#3FFFF, banks 0-3, selected by
  reading `bankswitcher + #20*n`; view 2, NCER at #40000-#7FFFF, banks 0-15,
  selected by reading `bankswitcher + 2*n`. The bank switcher is the CE1
  window (as on the 48GX).
- Bank-select address byte (bit 7..0): don't care, A18, A17, A20, A19, A18,
  A17, always-0 (nibble to byte). Bits 5-6 apply in #00000-#3FFFF, bits 1-4
  in #40000-#7FFFF; both can be written at once.
- "NEVER access memory at or above #40000 using NCER" (warning, reason not
  given).
- RAM blocks by controller: block 0 CE0 #40000-#FFFFF, block 1
  CE3 #80000-#FFFFF, block 2 CE0 #00000-#3FFFF, block 3 CE2 #00000-#FFFFF
  (as written; ranges are the possible windows).
- Giesselink: 64 KB of the first bank and banks 2-8 (1-based) hold the OS;
  the second half of bank 1 and banks 9-16 are user flash. Agrees with
  [[sources/hp49-memmap-smith]] once converted to 0-based numbering.

Reliability: author's own guesswork plus a reliable quote. The RAM block table
is hard to interpret; see [[questions/hp49g-ram-controllers]]. Feeds
[[hardware/hp49g]].
