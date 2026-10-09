---
title: "Where does the 48SX ROM end in the address map?"
type: question
status: answered
tags: [48sx, memory]
---

# Where does the 48SX ROM end in the address map?

The 48SX has 256 KB of ROM, which is #80000 nibbles, but RAM sits at #70000
(src: [[sources/mastracci-saturn-guide]] 2.1; [[sources/keyboard-ervin]] 3.4)
and Brittenson describes the ROM as 00000-6FFFF (src:
[[sources/screen-brittenson]]). Is the top 64 K nibbles of ROM hidden behind
RAM, or does RAM sit elsewhere? Check [[sources/voyage-48gx]] /
[[emulators/emu48]].

## Answer

The ROM fills #00000-#7FFFF, but RAM (NCE2, higher priority) is configured
at #70000-#7FFFF and covers the top 32 KB, so only #00000-#6FFFF is visible as
ROM in normal operation (src: [[sources/saturn-tutorial]] p. 156). The same
answer also settles the SX RAM size at 32 KB.
