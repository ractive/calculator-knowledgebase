---
title: "Beers et al., A Graphing Calculator for Mathematics and Science Classes (HP Journal, June 1996)"
type: source
authors: ["Ted W. Beers", "Diana K. Byrne", "James A. Donnelly", "Robert W. Jones", "Feng Yuan"]
year: 1996
raw: "raw/saturn-hardware/hp-journal/hpj-38g/ju96a6.pdf"
status: digested
tags: [hp-journal, hardware, aplets, firmware]
models: [38g]
---

# Beers et al., A Graphing Calculator for Mathematics and Science Classes (HP Journal, June 1996)

Three PDFs from the June 1996 Hewlett-Packard Journal, written by the HP 38G
team (text layer present; read with `pdftotext -layout`):

| File | Content | Read |
| --- | --- | --- |
| `ju96a6.pdf` | Article 6: the HP 38G, its hardware platform, aplets, views, the topic outer loop | all |
| `ju96a6a.pdf` | Subarticle 6a: how the distributed team used web pages for project documents | all; no hardware content |
| `ju96a7.pdf` | Article 7: designing an aplet (PolySides example with user programs) | all; no hardware content |

Cite as `(src: [[sources/hpj-38g]] art. 6 p. N)`, with the journal's own
page numbers (the per-article page number at the foot of each page).

## Hardware facts (art. 6 p. 1)

- "The hardware platform of the HP 38G is very similar to that of the HP 48G:
  they both have 32K bytes of RAM, 512K bytes of ROM, the same CPU and the
  same display (131 by 64 pixels)."
- Both have a two-way infrared link (printer, calculator to calculator) and an
  RS-232 link for calculator-to-computer communication.
- The overhead-projector display accessory works with both; the 38G's cable
  connector was modified so that every 38G drives it without a special
  handset.
- The 48 case was redesigned with a sliding cover, and **two keys were
  removed** compared to the 48G "to give visual emphasis to the navigation
  keys". The article does not name the two keys; it notes that there is no
  "next-row" (NXT) key, since every 38G softkey set has at most six items
  (p. 4), and no alpha lock key (p. 4).

## Firmware facts

- Built "on the same software platform as the HP 48G family" (p. 1): input
  forms, choose boxes and the parameterized outer loop are reused; a new
  "topic outer loop" runs the views (p. 5-7).
- When the calculator is first turned on, the built-in aplets are empty
  (p. 2). Six built-in aplet types: function, parametric, polar, sequence,
  statistics, solve (p. 2).
- Aplets travel to other 38Gs over IR and to a computer, from the library
  (p. 4); programs, matrices, lists and notes are sent and received from
  their editors (p. 5).
- New aplet types can carry a RAM-based support library written in System
  RPL (p. 10; art. 7 p. 10).

## Reliability

High for the hardware summary: written by the HP team that built the
calculator. The hardware paragraph is a one-line comparison, not a
specification; it gives no memory map, clock or controller wiring.

Facts used on [[hardware/hp38g]].
