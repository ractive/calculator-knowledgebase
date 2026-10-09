---
title: "How does the shared 39G/40G ROM tell which model it runs on?"
type: question
status: answered
tags: [39g, 40g, rom]
---

# How does the shared 39G/40G ROM tell which model it runs on?

The 39G and 40G run the same ROM image (src:
[[sources/giesselink-emu48-25-years]]). The 40G shows a CAS and has no IR
port (src: [[sources/hp39g40g-ug]] 1-2, 1-29, 16-5). Emu48 needs a KML
`Class 39` or `Class 40` line to emulate one or the other (src:
[[sources/kml20]] Global). So the ROM reads a hardware difference.

Candidates, none sourced: an unused keyboard IN line or OUT/IN combination
strapped on the board, a bit in an I/O register (#11F, #10F, the IR
control register #11A), or a value in the ROM chip itself at a model-
dependent address. Emu48's change log may say what `Class` changes, but
CHANGES.TXT lies in the reference tree this work may not open; ask someone
who may read it to look for the 39G/40G entries and record the fact
only.

How to settle without that: boot the ROM, trace every read of I/O
registers and every IN after an OUT during the cold start, and find the
branch that enables the CAS menu. Context: [[hardware/hp39g-40g]].

## Answer from a boot on saturnus (2026-10-05)

The ROM reads **#11A bit 3**. On the 39G that bit is the latched IR
receive sample ([[hardware/uart]] "IR"). Found by tracing every read of
the I/O registers during the cold start on the clean-room saturnus
emulator: the only read of #11A comes from one RPL primitive, code at
nibble #66F39 in ROM bank 1 (`D0=(5) #0011A`, `A=DAT0 B`, `?ABIT=1 3`,
then a GOSBVL that pushes TRUE on carry, FALSE otherwise). Its only
caller, the secondary at #66F16 (a ROMPTR target in the bank-1 library),
uses the flag to choose between two objects.

Effect, shown on saturnus: with #11A bit 3 read as 1, HOME shows a CAS
label on menu key 6, the 40G's look; with it 0 (what the ROM itself
writes there), the key is blank, as on the 39G. So a set bit means "40G".

Inferred, not sourced: the 40G board, which has no IR receiver, holds that
line high. A 39G that sees IR light at the moment of the read might take
itself for a 40G; not tested. The IN lines read during the cold start (OUT
#1FF, #080 and #001) returned nothing that differs between the models.
