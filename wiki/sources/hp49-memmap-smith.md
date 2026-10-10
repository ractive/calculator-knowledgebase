---
title: "Smith, HP 49G memory map"
type: source
authors: ["Eric Smith"]
year: 1999
raw: "raw/saturn-hardware/hp49-38-39/memmap/memmap.txt"
status: digested
tags: [memory, flash]
models: [49g]
---

# Smith, HP 49G memory map

A 1999-08-25 comp.sys.hp48 post, written after the HHUC conference, with an
ASCII memory map. Cite as "Smith".

- 2 MB flash = 16 banks of 128 KB. Any of banks 0-3 can be mapped
  at #00000-#3FFFF; any of the 16 at #40000-#7FFFF.
- The flash ignores the top Saturn address line, so the same banks reappear
  at #80000-#FFFFF if the RAM there is unconfigured.
- Banks 0-7 hold the OS, banks 8-15 the user flash (port 2), with hooks for
  the OS to grow. First 64 KB of bank 0 is the write-protected boot sector;
  second half of bank 0 belongs to port 2.
- On reset bank 0 is at #00000; after boot one of banks 1-3 is mapped there.
- RAM: 512 KB in three areas: 0-256 KB IRAM (HOME and port 0), normally
  at #80000-#FFFFF; 256-384 KB and 384-512 KB ERAM (port 1), mapped
  at #40000-#7FFFF half at a time when needed.
- The I/O registers at #00100 hide flash at that address; the internal serial
  number is at #00108 of flash bank 0, the boot version string at #00214
  (seen at #40108 / #40214 in the memory viewer).

Speculative in places ("I'm not sure which one"); author disclaims inside
knowledge. Feeds [[hardware/hp49g]].
