---
title: "Mesquita, HP Pioneers ROM Dumping (compilation)"
type: source
authors: ["José Mesquita", "Christoph Gießelink", "Jean-François Garnier", "Raymond Del Tondo"]
year: 2017
raw: "https://www.hpcalc.org/details/7847"
status: skimmed
tags: [pioneer, clamshell, rom, ir, lewis]
models: [42s]
---

# Mesquita, HP Pioneers ROM Dumping (compilation)

A 2014 compilation (draft v0.2, edited March 2017) of the forum posts and
readmes on dumping Pioneer and Clamshell ROMs with an HP 48 as the IR
receiver, by "jebem" of the HP Museum forum, from the work of Gießelink,
Garnier and Del Tondo. PDF on hpcalc.org; read on 2026-10-12, not kept
in `raw/`.

- Gießelink's 2003 forum post: a faster dump for the high-end Pioneers
  (17B, 17BII, 27S, 42S): about 50 bytes of machine code typed in with
  the built-in memory scanner (destroying RAM) send the ROM through the
  ROM's own IR byte routine to a binary-safe INPRT on an HP 48. The 48
  needs about 70 KB free for a 64 KB image, 103 KB for a 96 KB one (a
  48G+, 48GX or any 48 with a merged 128 KB card). About 16 to 17 min
  of transfer for 64 KB, 40 to 45 min in all.
- A memory-editor summary for the 17BII family: ROM at #00000, display
  at #40000, 8 KB RAM at #50000; the editor's PRT key sends a line over
  IR.
- The rest repeats Garnier's 42S method ([[sources/garnier-hp42s-new-facts]])
  and Gießelink's `PIONEER.TXT` and `CLAMSHEL.TXT`
  ([[sources/emu42-pioneer-dump]]), plus an HP 48 serial cable how-to.
- The title lists the 19BII and 32SII, but the document has no method
  for them: the 19BII has no memory scanner and the low-end Pioneers have
  no IR port ([[sources/hpmuseum-forum]] F3).

Used on [[hardware/saturn-models]].
