---
title: "HP-28S Reference Manual"
type: source
authors: ["Hewlett-Packard"]
year: 1989
raw: "raw/manuals/hp28s-rm-en.pdf"
status: digested
tags: [manual, clamshell, display, keyboard, printer]
models: [28s]
---

# HP-28S Reference Manual

Edition 5 (August 1989), reorder number 00028-90068. A 356-page scan
with hpcalc.org's OCR as a text layer (per-page text in
`raw/manuals/text/hp28s-rm-en.txt`, block N = PDF page N). Public copy:
<https://literature.hpcalc.org/community/hp28s-rm-en.pdf>. Book page =
PDF page - 2. The dictionary is alphabetical by menu.

Digested for [[hardware/hp28s]], only these entries: SYSEVAL (p. 126),
the PLOT pixel coordinates and DRAW (p. 153, 159), the PRINT
introduction (p. 168-169), KEY (p. 197) and →LCD / LCD→ (p. 269). The
rest of the dictionary was not read.

## Where the useful parts are

- **p. 126**: SYSEVAL runs the system object at an absolute address;
  `# 10d SYSEVAL` shows the version and copyright message.
- **p. 153**: pixel numbering: 0-136 across, 0-31 up from the lower
  left; the centre pixel is (68, 15).
- **p. 159**: DRAW plots up to 137 points, one per pixel column.
- **p. 168-169**: printing to the HP 82240A over one-way infrared from a
  diode on the top edge of the right-hand half; the printer annunciator;
  flag 52 for the faster rate on mains-powered printers.
- **p. 197**: KEY and the 15-key key buffer; the strings for the keys
  without a plain key-top text, including "CURSOR" for the cursor-menu
  key.
- **p. 269**: a display image is 548 bytes, 137 per display line, each
  byte a column of eight pixels, least significant bit at the top.
