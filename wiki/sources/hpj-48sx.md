---
title: "HP Journal, June 1991: The HP 48SX Scientific Expandable Calculator"
type: source
authors: ["Hewlett-Packard"]
year: 1991
raw: "raw/saturn-hardware/hp-journal/hpj-48sx/hpj-48sx.pdf"
status: skimmed
tags: [hp-journal, hardware, timing]
models: [48sx]
---

# HP Journal, June 1991: The HP 48SX Scientific Expandable Calculator

A 38-page scan of the June 1991 Hewlett-Packard Journal articles on the
HP 48SX. The text layer is a poor OCR; read the page images where the OCR
garbles digits (`pdftoppm -f N -l N`). Only the custom-IC sidebar was read
for this page (2026-10-05).

Cite as `(src: [[sources/hpj-48sx]] p. N)` with the journal's page number.

## The 1LT8 IC (sidebar "HP 48SX Custom Integrated Circuit", p. 30, Preston D. Brown)

- A 4-bit CPU "identical to the CPU used in the HP 28S", a leveraged
  redesign of the 1LK7 CPU, with faster instruction execution times and
  full compatibility with the 1LF2, 1LK7 and the HP 71B bus architecture.
  Internal and external data paths are 4 bits wide.
- A memory controller interfacing up to five commercial byte-wide RAMs,
  ROMs or plug-in ports, which "requires some careful interfacing to the
  4-bit world of the CPU".
- The LCD controller "halts the CPU to access data in main RAM" (see
  [[hardware/display]]).
- A 32-bit quartz-crystal-controlled timer, a 1200-9600 baud UART, a CRC
  generator.
- "a crystal oscillator, a frequency multiplier (which generates the 8-MHz
  CPU clock from the 32-kHz crystal)". The usual "2 MHz" figure for the
  48SX is a quarter of this (inferred: the SASM cycle is four clock
  phases; no source says so). See [[hardware/saturn-cpu]] "Timing".
