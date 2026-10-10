---
title: "HP 49G system flags the Pocket Guide does not cover"
type: question
status: open
tags: [flags, rom]
models: [49g]
---

# HP 49G system flags the Pocket Guide does not cover

The list was found: the *HP 49G Pocket Guide* (src:
[[sources/hp49g-pocket-guide]] p. 76-79), to which the Advanced User's
Guide defers, describes 103 system flags with both states and defaults,
and they are transcribed on [[hardware/system-flags-49g]]. It also
settles the count (128 system flags, -1 to -128) and the polarity of
-95 (set = algebraic). The earlier questions (whether -1 to -64 keep
their 48G meanings, which flags hold the word size and the display
digits) are answered there.

Still open:

1. The 25 flags the booklet leaves out (-4, -13, -30, -33, -34, -56,
   -75, -77, -78, -101, -102, -104, -107, -108, -112, -115, -118, -121 to
   -128): unused, or used without being documented? The calculator's own
   MODE > FLAGS browser (src: [[sources/hp49g-aug]] p. 2-1) would show
   any that the ROM names. ROM 2.10 sets -34 and -128 at a cold start
   (observed in saturnus), so those two are in use.
2. The default of -90: clear per the booklet (p. 78), set per the
   Advanced User's Guide (p. 1-4). ROM 2.10 sets it at a cold start
   (observed in saturnus), as it also sets HEX, radians, -27 and
   algebraic mode against the booklet's defaults; which ROM the booklet
   describes, and a check on @ractive's 49G, remain open.
3. What the terse entries apply to (-71 addresses, -86 program prefix,
   -89 unknowns, -93 header, -94 LASTCMD, -120 silent mode); the booklet
   names the states only.

No photograph was hard to read; nothing here waits on @ractive's
booklet.
