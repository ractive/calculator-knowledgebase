---
title: "UART registers #110-#112: which bit is which?"
type: question
status: answered
tags: [saturn, uart]
---

# UART registers #110-#112: which bit is which?

Mastracci and Taplin disagree on the bit layout of #110 (interrupt control)
and #112 (status), and on whether #10E-#10F belong to the baud register (src:
[[sources/mastracci-saturn-guide]] 4.5; [[sources/hdwreg-taplin]]). Table on
[[hardware/uart]].

This matters for both the emulator (the ROM's serial driver must see the
right bits) and for understanding receive overruns in hptx tests.

Resolve with [[sources/voyage-48gx]], [[sources/saturn-tutorial]] and
[[emulators/emu48]] CHANGES.

## Answer

Mastracci's layout is right. Voyage documents #110 (bit 0 interrupt on
receive start, 1 on receive buffer full, 2 on transmit buffer empty, 3 serial
port active), #111 (0 byte present, 1 receiving, 2 error) and #112 (0 transmit
buffer full, 1 transmitting) exactly as Mastracci does, and puts card
detection in #10E-#10F and only the baud code in #10D bits 0-2 (src:
[[sources/voyage-48gx]] p. 195-197). Taplin's 1991 list is wrong for these.
