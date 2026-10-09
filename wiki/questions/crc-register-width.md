---
title: "Is the hardware CRC register 8 or 16 bits wide?"
type: question
status: answered
tags: [saturn, crc]
---

# Is the hardware CRC register 8 or 16 bits wide?

Mastracci calls the CRC "8-bit" and lists #104-#105 (two nibbles), yet gives an
update rule that multiplies by #1081 and keeps 16 bits (src:
[[sources/mastracci-saturn-guide]] 2.6, 4.11). See [[hardware/crc]]. Check
[[sources/checksum-crc]] and [[sources/hdwreg-taplin]].

## Answer

16 bits. Gariepy: "the 16 bit CRC at address #00104" (src:
[[sources/checksum-crc]]). The register therefore spans #104-#107. Confirm
the exact span when [[sources/voyage-48gx]] is ingested.
