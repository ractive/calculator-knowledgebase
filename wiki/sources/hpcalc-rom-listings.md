---
title: "hpcalc.org ROM image listings for the 38G, 39G/40G and 48"
type: source
authors: ["Eric Rechlin"]
year: 2026
raw: "https://www.hpcalc.org/hp38/pc/"
status: digested
tags: [rom]
models: [48sx, 48gx, 38g, 39g, 40g]
---

# hpcalc.org ROM image listings for the 38G, 39G/40G and 48

Web pages, not in `raw/`, fetched 2026-10-05. Nothing was downloaded.
Pages read: <https://www.hpcalc.org/hp38/pc/>,
<https://www.hpcalc.org/hp39/pc/>, the details pages 4775 (38G ROM), 6739
(39/40 ROM), 4272 and 4273 (beta-ROM emulator packages), and for comparison
<https://www.hpcalc.org/hp48/pc/emulators/> and details page 4369 (48GX
ROM P).

| Model | Details page | File | Archive size | Content |
| --- | --- | --- | --- | --- |
| 38G | <https://www.hpcalc.org/details/4775> | `38grom.zip` (`https://www.hpcalc.org/hp38/pc/38grom.zip`) | 323,277 bytes | `38G_A167.ROM`, 524,288 bytes; "Revision A", dated 1999-12-16; author "Hewlett-Packard"; "for use with the Emu48 emulator" |
| 39G, 40G | <https://www.hpcalc.org/details/6739> | `rom3940.zip` | 563,090 bytes | `rom.39g`, 2,097,152 bytes, dated 2000-11-19; author "Hewlett-Packard"; "ROM image from the 39G/40G suitable for using with Emu48" |
| 39G, 40G beta | <https://www.hpcalc.org/details/4272> | `emu48-39.zip` | 1,004 KB | Emu48 1.21 with "an HP 39G/40G beta ROM" and KML |
| 39G, 40G beta | <https://www.hpcalc.org/details/4273> | `emu39.zip` | 1,253 KB | HP's own "YorkeM 39" emulator with the beta ROM |

The direct 38G URL is derived from the relative link on the details page;
the 39/40 archive's direct path was not shown and was not probed.

- **Permission.** Every 48 ROM details page says "HP graciously began
  allowing this to be downloaded in mid-2000" (page 4369). **Neither the
  38G nor the 39/40 page carries that sentence**, or any other permission
  statement; both name Hewlett-Packard as author. The Emu48 manual says
  that since fall 2000 the ROMs "for the HP38, 39, 40, 48 and 49 are freely
  available on different Internet sites" and that, lacking a distribution
  licence, Emu48 does not bundle them (src: [[sources/emu48-manual]] 3).
- Upload tools: ROMUPL39 1.3 by Gießelink (details 7801) uploads a
  39G/40G ROM to a PC; Emu48 documents a "ROM UPLOAD" aplet for the 38G
  (src: [[sources/emu48-manual]] 3.1).

## Reliability

The archive's listing data (names, sizes, dates) is exact. The permission
question is a legal reading, not a fact this page settles.

Facts used on [[hardware/hp38g]], [[hardware/hp39g-40g]].
