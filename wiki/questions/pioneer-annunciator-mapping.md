---
title: Which Lewis annunciator positions carry the 17B, 17BII and 27S symbols?
type: question
status: open
tags: [lewis, display, annunciators, pioneer]
models: [17b, 17bii, 27s, 42s]
sources:
  - "[[sources/hp17bii-om]]"
  - "[[sources/hp17b-om]]"
  - "[[sources/hp27s-om]]"
  - "[[sources/commons-pioneer-photos]]"
  - "[[sources/garnier-hp42s-new-facts]]"
---

# Which Lewis annunciator positions carry the 17B, 17BII and 27S symbols?

The Lewis has seven annunciator words; on the 42S they are #40218 ▲▼,
then shift #40220, print #40228, busy ((•)) #40230, battery #40238,
G #40240 and RAD #40248 (src: [[sources/garnier-hp42s-new-facts]] 2.2; 42S ROM).

The manuals name fewer symbols:

- 17B and 17BII: shift, print, alarm ((•)), battery (src:
  [[sources/hp17bii-om]] p. 17; [[sources/hp17b-om]] p. 17).
- 27S: shift, print, appointment ((•)), battery, RAD (src:
  [[sources/hp27s-om]] p. 20).

Photographs of the self-test pass screens show ▲▼ and RAD lit on a 17BII
and ▲▼ lit on a 27S (src: [[sources/commons-pioneer-photos]] P1, P3), so
the glass carries segments the manuals do not mention, in the 42S's
places as far as the photographs show (▲▼ top left, RAD right of centre).

Assumed until a ROM shows otherwise: the same addresses as on the 42S,
with the alarm or appointment symbol at #40230 (the 42S's busy symbol has
the same shape), and #40240 either blank or a G segment never lit.

Open:

- Does the 17B/17BII/27S ROM write #40230 for the alarm and also for busy?
- Is there a G segment on these displays (does the self-test light the
  word at #40240)?
- What do ▲▼ and RAD mean on a 17BII, if anything outside the self-test?
  (▲▼ may mark lists or the history stack; unverified.)

How to settle it: run the ROMs and watch writes to #40218-#4024C, and
compare a self-test DISP pass with photographs.
