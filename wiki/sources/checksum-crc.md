---
title: "Gariepy and Kaffine, Built-in hardware CRC at #00104"
type: source
authors: ["Alonzo Gariepy", "Dave Kaffine"]
year: 1990
raw: "raw/saturn-hardware/hp48-hw-notes/checksum.txt"
status: digested
tags: [hp48, crc]
---

# Gariepy and Kaffine, Built-in hardware CRC at #00104

Two short newsgroup paragraphs (1990).

- Gariepy: the HP 48 has a 16-bit hardware CRC at #00104, updated on every
  nibble read; BYTES zeroes it and reads the object; libraries use the same
  CRC; disable interrupts when using it from ML.
- Kaffine: the CRC stored in a library object does not cover the 5-nibble
  prolog #02B40 (written "04B20" in nibble order); it starts at the length
  field.

Feeds [[hardware/crc]] and [[protocols/hp-object-format]]. Settles the width
question raised by [[sources/mastracci-saturn-guide]]: 16 bits.
