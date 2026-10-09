---
title: "What is the LPB bit in UART register #112?"
type: question
status: answered
tags: [saturn, uart]
---

# What is the LPB bit in UART register #112?

Mastracci lists #112 bit 2 as "LPB" without explanation (src:
[[sources/mastracci-saturn-guide]] 4.5). Possibly loopback. Check
[[sources/voyage-48gx]] and [[sources/io-guide]].

Voyage's #112 table lists only bits 0 and 1; bits 2 and 3 are blank (src:
[[sources/voyage-48gx]] p. 197). Still unknown.

## Answer

LPB is loop-back: transmitted bytes are fed back to the receiver (src:
[[emulators/emu48]] CHANGES SP15, SP22, SP28). Emu48 also names TCS bits
TBF, TBZ and BRK (send break) (SP26).
