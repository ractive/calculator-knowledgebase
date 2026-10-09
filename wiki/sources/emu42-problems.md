---
title: "Gießelink, Emu42 1.33 PROBLEMS.TXT"
type: source
authors: ["Christoph Gießelink"]
year: 2025
raw: "raw/emulator-docs/emu42/PROBLEMS.TXT"
status: digested
tags: [emu42, lewis, registers]
---

# Gießelink, Emu42 1.33 PROBLEMS.TXT

The known-restrictions note of Emu42 1.33. It lists the Lewis I/O bits Emu42
does not emulate, giving register names, offsets and bit names, which is
the only published Lewis register naming found:

| Offset | Name | Bits not emulated |
| --- | --- | --- |
| 0x302 | DSPTEST | VDIG LID CLTM1 CLTM0 |
| 0x303 | DSPCTL | SDAT BIN |
| 0x309 | LPE | EVRAM RST |
| 0x30B | RAMTST | PLEV XTRA DDP DPC |
| 0x30C | INPORT | RX SREQ ST1 ST0 |
| 0x30D | LEDOUT | UREG EPD DRL |

and: "Register and Display/Timer MMU configuration isn't separated", so the
Lewis has separate memory-controller windows for its registers and for its
display and timer block (inferred from the wording). The Sacajawea and Bert
lists are not relevant here. Used on [[hardware/lewis]].
