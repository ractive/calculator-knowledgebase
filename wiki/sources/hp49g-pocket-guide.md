---
title: "The HP 49G Pocket Guide"
type: source
authors: ["Hewlett-Packard"]
year: 1999
raw: "raw/manuals/hp49g-pocket-guide/"
status: digested
tags: [manual, flags]
models: [49g]
---

# The HP 49G Pocket Guide

HP's small printed reference booklet that came with the 49G; the
Advanced User's Guide refers to it for the full list of system flags
(src: [[sources/hp49g-aug]] p. 2-1). Not available as a PDF in the
library; the year is assumed from the 49G's release (unverified, the
photographed pages carry no date).

What the library has: four of @ractive's photographs of a printed copy, taken
2026-10-06, of the "System Flags" section only, in
`raw/manuals/hp49g-pocket-guide/` (with a README). They are not online and
not distributed with this repository; check against your own copy:

- `p76-flags--1-to--26.jpeg`: p. 76, the introduction (flag numbering,
  STOF and RCLF) and flags -1 to -26, with the word-size footnote.
- `p77-flags--27-to--61.jpeg`: p. 77, flags -27 to -61.
- `p78-flags--62-to--96.jpeg`: p. 78, flags -62 to -96.
- `p79-flags--97-to--120.jpeg`: p. 79, flags -97 to -120, where the
  list ends.

Pages 76 and 78 carry printed numbers; 77 and 79 follow from the
sequence. Every entry gives the set and the clear meaning, and a bullet
marks the default state. All four images are sharp enough to read every
entry.

Reliability: an official HP document. One internal slip (-25's set
state is conditioned on -25 itself, evidently meant to be -21) and one
disagreement with the Advanced User's Guide (the default of -90); both
on [[hardware/system-flags-49g]] under Contradictions.

Taken from it: the whole flag table of [[hardware/system-flags-49g]]
(103 flags, restated in our own words, with defaults), and the flag count
(128 system, 128 user) for [[questions/hp49g-system-flags]].
