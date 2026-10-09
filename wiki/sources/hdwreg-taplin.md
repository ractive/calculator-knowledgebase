---
title: "Taplin, HP48SX Hardware Registers Document v1.0"
type: source
authors: ["Julian Taplin"]
year: 1991
raw: "raw/saturn-hardware/hp48-hw-notes/hdwreg.txt"
status: digested
tags: [hp48, io-ram, 48sx]
---

# Taplin, HP48SX Hardware Registers Document v1.0

A 1991 comp.sources.hp48 post listing what was then known about the 48SX I/O
RAM. One page; no sections, cite as "Taplin" with the register address.

Covers #100-#103 (display offset, contrast, LCD voltage), #10B-#10C
(annunciators), #10D-#10F baud rate, #110 UART interrupts, #112 UART
status, #114-#117 receive/transmit bytes, #11A and #11C IR, #120-#129 display
pointers.

## Reliability

Early and self-described as incomplete ("compiled from various documents and
source codes"). Several bit assignments disagree with
[[sources/mastracci-saturn-guide]]: the #110 interrupt bits and the #112
status bits (see [[questions/uart-register-bit-layout]]), and #100 offset width
(2 bits vs 3). Its baud table (#600 = 9600 etc.) agrees with Mastracci.
Useful mostly as a dissenting witness.
