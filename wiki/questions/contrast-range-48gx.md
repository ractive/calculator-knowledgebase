---
title: "48GX contrast: keyboard range 3-19 or 9-24?"
type: question
status: open
tags: [48gx, display]
---

# 48GX contrast: keyboard range 3-19 or 9-24?

Voyage (a G/GX book) says ON+/ON- reach contrast values #3-#13, i.e. 3-19
(src: [[sources/voyage-48gx]] p. 193). Emu48's KML table gives 3-19 for the
48SX and 9-24 for the 48GX, reset 11 and 14 (src: [[sources/kml20]] LCD).
Probably Voyage repeats the S/SX figure. Low priority: contrast does not
affect emulation correctness, only rendering.

## Progress (2026-10-07)

ROM R writes 14 as its power-on contrast, KML's 48GX reset value, and ROM
J writes 11, KML's 48SX value (observed in saturnus 2026-10-07). That fits
KML's per-model table rather than Voyage's single range. The keyboard
limits themselves were not measured; saturnus takes 9-24 from KML. To
settle it, press ON+ and ON- until #101 and #102 stop changing.
