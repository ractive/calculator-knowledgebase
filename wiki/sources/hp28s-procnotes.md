---
title: "Gariepy, HP28S Processor Notes (1989)"
type: source
authors: ["Alonzo Gariepy"]
year: 1989
raw: "raw/saturn-hardware/hp28s-procnotes.txt"
status: skimmed
tags: [28s, lewis, saturn]
---

# Gariepy, HP28S Processor Notes (1989)

Notes on the HP 28S CPU, its instruction set (renamed mnemonics) and machine
code use. The 28S is a Clamshell machine on the Lewis (two chips, see
[[sources/emu42-changes]]). Read for the memory map and I/O on 2026-10-05.

- Architecture "Overview": 128 KB ROM at #00000-#3FFFF, 32 KB RAM at the
  range #C0000-#CFFFF aliased at #D0000-#DFFFF, "other little chunks" for hardware.
- Sample program SCR: the 28S display memory starts at #FF840 (left columns)
  and #FFC00 (right columns), two columns per 16 nibbles; "each 1/2 of LCD is
  34*2 columns (+1 on right)".
- "I/O Registers": a 16-bit IN register reads the keyboard, a 12-bit OUT
  register drives the keyboard and the beeper.

The 28S places the Lewis's blocks at other addresses than the 42S
([[hardware/hp42s]]): the Lewis windows are configurable, and each ROM sets
its own (inferred; see [[questions/lewis-memory-map]]).
