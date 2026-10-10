---
title: "Finseth, HP Calculator Data: hp38g"
type: source
authors: ["Craig A. Finseth", "Detlef Mueller"]
year: 1996
raw: "https://www.finseth.com/hpdata/hp38g.php"
status: digested
tags: [memory, hardware]
models: [38g]
---

# Finseth, HP Calculator Data: hp38g

Web page, not in `raw/` (fetched 2026-10-05; the `raw` field holds the URL).
A model data sheet in Craig Finseth's long-running HP calculator database,
with a comment by Detlef Mueller (the author of the 64 KB RAM 38G ROM that
Emu48 later supported, see [[sources/giesselink-emu48-25-years]]). The year
is approximate.

- CPU "Yorke (00048-80063, 4 MHz)", the same part number as the 48G/GX
  Yorke ([[hardware/hp48gx]]).
- RAM: 32 KB total, about 22 KB available to the user. ROM: 512 KB,
  labelled "ELSIE OTP Rev 1.67" (Elsie is the 38G code name; OTP =
  one-time-programmable).
- Display 131 x 64, 8 lines of 22 characters. I/O: "4-wire serial, I/R
  I/O, beeper, overhead display out (on rest of 10 pins in the serial
  connector)". Three AAA cells. Introduced 1995-04-06 at $118.
- **Mueller on RAM upgrades**: "the build-in 32k memory is mapped to address
  F0000-FFFFF, a bigger chip would just do nothing and cause trouble if the
  RAM is mapped away temporarily to access the underlaying ROM." He also
  mentions a card connector and advises against using it.

## Reliability

Medium-high for the memory statement: Mueller worked on the 38G ROM at
this level (he built the 64 KB RAM variant). It agrees with the display
ghost registers at #F062C ([[hardware/display]]). The introduction date
conflicts with Mastracci's "09/1995"; see [[hardware/hp38g]].

Facts used on [[hardware/hp38g]].
