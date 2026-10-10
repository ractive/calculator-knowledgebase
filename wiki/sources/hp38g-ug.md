---
title: "HP 38G Graphing Calculator User's Guide"
type: source
authors: ["Hewlett-Packard"]
year: 1998
raw: "raw/manuals/hp38g-ug-en.pdf"
status: skimmed
tags: [manual, keyboard, reset, transfer, aplets]
models: [38g]
---

# HP 38G Graphing Calculator User's Guide

HP part F1200-90013, printing of January 1998 (back cover), 228-page scan
with **no text layer**. Read through a local OCR pass (tesseract at 150 dpi;
the OCR text is not kept in `raw/`) plus a visual check of the keyboard
figure. OCR mangles key glyphs, so key names below come from the figure,
not the OCR. Cite by the guide's printed page (`1-2`, `9-8`), or "inside
cover" for the keyboard figure (PDF page 2).

Read: chapter 1 (keyboard, display, annunciators, sending and receiving
aplets), the reference chapter 9 (batteries, reset, memory specifications).
The math and aplet chapters were not read.

## Facts

- **Keyboard figure** (inside cover): 47 keys. Rows from the top: six blank
  menu keys; PLOT, SYMB, NUM, a gap, up arrow, a gap; LIB, VAR, MATH, left,
  down, right; HOME, SIN, COS, TAN, X,T,θ, square root; a double-width ENTER,
  `(`, `)`, negate, x^y; A...Z (alpha shift), 7, 8, 9, divide; the shift key,
  4, 5, 6, multiply; DEL, 1, 2, 3, minus; ON, 0, point, comma, plus.
- Alpha letters are printed at the lower right of each key (1-2): A-F on the
  HOME row, G-J on `(`, `)`, negate, x^y, K-N on 7-9 and divide, O-R on 4-6
  and multiply, S-V on 1-3 and minus, W-Z on 0, point, comma and plus
  (inside cover; "to type Z, press A...Z +", 1-2).
- One shift key (turquoise), one alpha shift; holding alpha shift types a
  string (1-2).
- Contrast: ON together with a second key raises or lowers it (1-6; the
  key glyphs are lost in the OCR).
- Annunciators: shift, alpha, low battery, busy, "data is being transferred
  via infrared or cable", more history, angle mode (1-6).
- Aplets go to another 38G over IR (triangle marks aligned, within 2 in /
  5 cm) or by cable to an "aplet disk drive or computer" running a
  connectivity kit; SEND offers another HP 38G or a disk drive; when
  receiving from a disk drive or computer the calculator lists the aplets
  in its current directory (1-26, 1-27). Lists and matrices send and
  receive the same way (Matrices and Lists chapters).
- **Reset** (9-8): ON plus the third menu key ("top row, third from left")
  resets without clearing stored data; a reset hole on the back does the
  same in hardware. ON plus the leftmost and rightmost menu keys erases all
  memory and restores factory defaults; releasing only the menu keys and
  pressing the third one cancels.
- **Memory specifications** (9-8): "32 KB of RAM (user memory)", "512 KB of
  ROM (built-in software)".
- Three AAA cells; 4.5 V dc, 60 mA maximum; memory is lost if the
  batteries stay out longer than about 2 minutes (9-7).

## Reliability

Official HP manual. Silent on the memory map, clock, controllers and the
wire protocol.

Facts used on [[hardware/hp38g]].
