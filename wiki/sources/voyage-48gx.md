---
title: "Courbis and Lalande, Voyage au centre de la HP 48 G/GX"
type: source
authors: ["Paul Courbis", "Sébastien Lalande"]
year: 2000
raw: "raw/saturn-hardware/hp48gx-voyage.pdf"
status: digested
tags: [hp48, 48gx, io-ram, memory, display, uart, ir, timers, french]
---

# Courbis and Lalande, Voyage au centre de la HP 48 G/GX

The French reference book on the 48G/GX (2000 edition, 612-page PDF). The
text layer is OCR of a scan and is noisy (digits and `#` misread, tables
mangled); every fact taken here was read in context. Cite **book page
numbers**; book page = PDF page - 8 in part 2.

## Read (part 2, "Le langage-machine")

| Ch. | Book pages | PDF pages | Content |
| --- | --- | --- | --- |
| 2 | 79-90 | 87-98 | Saturn registers, IN/OUT, keyboard table (OCR unusable), buzzer, RSTK use by interrupts, module managers and priority, ST flags 10-15 |
| 3 (parts) | 102, 130-132 | 110, 138-140 | IN/OUT opcodes, SHUTDN, RESET, CONFIG, UNCNFG, C=ID results, RSI, SETDEC/SETHEX |
| 5 | 183-190 | 191-198 | memory organisation and default maps (figures 1-5, OCR poor) |
| 6 | 191-204 | 199-212 | I/O RAM #100-#13F bit by bit |
| 7 | 205-207 | 213-215 | bank switcher |

Skipped: part 1 (user guide), ch. 4 (objects), ch. 8 (reserved RAM, only the
display "save" addresses noted), ch. 9 and the program library. The keyboard
OUT/IN table on book p. 81 is a rotated scan that OCR could not read; use
the Read tool on PDF p. 89 if it is ever needed.

## Reliability

High for the GX: written from ROM study, matches Mastracci on the UART bits
and Giesselink on C=ID. Disagreements:

- Bus priority puts the bank switcher (CE1) above port 1 (CE2) (p. 86-87);
  Giesselink and Mastracci say CE2 > CE1: [[questions/bus-priority-ce1-ce2]].
- #129 bit 3: 0 = full 512 KB ROM, 1 = low 256 KB mirrored at #80000 (p.
  202), the opposite polarity to the tutorial's DA19 example:
  [[questions/da19-polarity]].
- The interrupt handler checks that no module is unconfigured (p. 185);
  Duchesne says only the S handler does: [[questions/config-check-in-handler]].
