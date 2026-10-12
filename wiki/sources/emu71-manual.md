---
title: "Gießelink, Emu71 Manual"
type: source
authors: ["Christoph Gießelink"]
year: 2023
raw: "https://hp.giesselink.com/Emu71/Emu71.htm"
status: skimmed
tags: [emu71, 71b, hp-il, rom]
---

# Gießelink, Emu71 Manual

The manual of Emu71 for Windows (v1.19), with the emulator's web page
(<https://hp.giesselink.com/emu71.htm>). Web pages, not in `raw/`; read
on 2026-10-12 for the hardware and the ROM situation. Documentation
shipped with an emulator: a fact source under the clean-room rule; the
GPL source is not.

- 1 General: the HP-71B ("Titan") emulation models the 1LK7 Saturn CPU,
  the 1LF3 display driver, and the 1LG7 ROM and 1LG8 RAM chips; ports 0
  to 5 take RAM and ROM modules and the HP-IL modules. The HP-IL I/O
  processor is simulated with code from J-F Garnier.
- 3 ROM Images: HP's ROMs are copyrighted and the author has no licence
  to distribute them. A short BASIC program by Garnier reads the ROM
  with `PEEK$` and sends it to an HP-IL printer device, captured on a PC
  through Garnier's PIL-Box USB-to-HP-IL interface; the same works for
  the HP-IL module's 16 KB ROM. A checksum tool (TITANCHK) validates
  images, packed or unpacked.
- 9.1 Port Configuration: the internal 16 KB "port 0" RAM is four 4 KB
  chunks; a ROM or RAM "port" in a module may hold several chips, each a
  power-of-two size on a base address that is a multiple of its size
  (an address-decoder limit). The system ROM is hard configured (fixed
  address); module ROMs and RAMs are soft configured: the operating
  system chooses the base address at run time and each chip answers an
  ID with its type, size and whether it is the last chip in the chain.
  The 1LG7 is a 16 KB ROM chip designed for this; larger ROMs chain
  several. The FORTH/Assembler and HP-41 Translator modules hold a hard
  and a soft configured ROM.
- 13 Virtual HP-IL: HP-IL is a two-wire loop; a frame is 11 bits. Emu71
  carries each frame as two bytes over TCP between virtual devices,
  compatible with Garnier's ILPer peripheral emulator.

Used on [[hardware/saturn-models]].
