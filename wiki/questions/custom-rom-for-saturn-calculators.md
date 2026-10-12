---
title: "Custom ROMs and replacement systems for the Saturn calculators"
type: question
status: open
tags: [rom, flash, homebrew, toolchain]
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
sources: ["[[sources/shellos-manual]]", "[[sources/hp49g-faq]]", "[[sources/hpcalc-homebrew-listings]]", "[[sources/homebrew-projects-web]]", "[[sources/sasm-reference]]", "[[sources/mastracci-saturn-guide]]", "[[sources/saturn-tutorial]]", "[[sources/hp48-faq]]"]
---

# Custom ROMs and replacement systems for the Saturn calculators

Has anyone written their own operating system or ROM for the Saturn-based
calculators, and what would a free ROM that boots on an emulator, or on a
real 49G, have to do? Researched 2026-10-12. Status open: the 49G boot
loader's expectations of a system image are unknown (see the end).

Not reachable on 2026-10-12: the HP Museum forum (Cloudflare challenge)
and comp.sys.hp48 on Google Groups (rate limit), so threads there were
not read. Their absence below means "not checked", not "does not exist".

## 1. Replacement systems

- **On the Saturn models, one full replacement OS is documented: ShellOS**
  (Stephane Cocquereaumont, Philippe Dedieu, 1998-1999), a multitasking
  Unix-like system in assembly for the 48GX only, living on a 32 KB or
  128 KB RAM card in slot 1. Its authors say it uses no part of the HP ROM;
  it is started from the RPL OS and can switch back to it (src:
  [[sources/shellos-manual]] p. 2, 4, 12; [[sources/hpcalc-homebrew-listings]]
  details 79). It is not a ROM: the HP ROM still runs at reset, and
  ShellOS is entered from it. No source, no licence statement.
- **Meta Kernel** (Maubert Development Group, 48GX, sold on a 128 KB ROM
  card for slot 1) replaces the user interface (stack, editor, writers,
  filer) but keeps HP's RPL kernel and stays compatible with it (src:
  [[sources/hpcalc-homebrew-listings]] Meta Kernel review; [[sources/hp48-faq]]
  13.1). Jean-Yves Avenard, one of its authors, presented the 49G for
  HP's calculator division, whose system was "completely rewritten,
  mainly in assembly" and is said to be faster than a 48 running the Meta
  Kernel (src: [[sources/hp48-faq]] 2.14).
- **newRPL and HPGCC are ARM-only.** newRPL installs on the 50g, 39g+,
  39gs, 40gs, 48gII and Prime G1, all ARM machines, through their ARM boot
  loaders; its installation guide names no Saturn model. HPGCC compiles
  C for the ARM models. No Saturn port of either was found (src:
  [[sources/homebrew-projects-web]] newRPL, HPGCC).
- **Later HP ROMs on the 49G**: the 49g+/50g "emulator" builds up to at
  least 2.10 were installed on the original 49G (src:
  [[sources/homebrew-projects-web]] Wikipedia; secondary). These are HP's
  own system, not homebrew; the saturnus observation that the 2.15 image
  boots as a 49G fits ([[hardware/hp49g]] "Bring-up findings").
- **38G, 39G, 40G**: no replacement system was found. The 38G's ROM is
  one-time-programmable and the 39G/40G's is mask ROM ([[hardware/hp38g]],
  [[hardware/hp39g-40g]]), so there is nothing to flash.

## 2. Programs that take the machine over

- Doom-style games exist for the 48: **Doom Preview II** (Clément Pillias,
  1998, greyscale, two monsters, four weapons) and **Doom Alpha for the
  GX** (Cyrille de Brébisson and Paul Lancaster, 1997, partly greyscale,
  two players over the link), plus first-person maze games (Lab3D, Rescue
  3D). No page gives a frame rate (src: [[sources/hpcalc-homebrew-listings]]
  details 486, 487, arcade page). Many fast 2D greyscale games were
  written in Saturn assembly, 1995-1998 (src:
  [[sources/homebrew-projects-web]] sunhpgames).
- They run as code objects started from the RPL OS, not as ROMs (all are
  distributed as 48 objects or directories; src:
  [[sources/hpcalc-homebrew-listings]]).
- The documented takeover techniques:
  - Disable interrupts (INTOFF masks only the keyboard; to stop
    everything including ON the ROM's own routine leaves the CPU "in
    service" after a deliberate interrupt) ([[hardware/interrupts]]
    "Interrupt-in-service flag").
  - Write the display registers directly: main bitmap start #120, line
    count #128, menu bitmap #130 ([[hardware/display]]); greyscale code
    waits on the row counter read from #128 and flips buffers (src:
    [[sources/mastracci-saturn-guide]] 6.1, via [[hardware/display]]).
  - Scan the keyboard with OUT and C=IN ([[hardware/keyboard]]).
  - Own interrupt handler: every Saturn interrupt is a subroutine call
    to address #0000F (src: [[sources/sasm-reference]] 2.10), which is ROM, so a
    program maps RAM at #00000 (copying low RAM away first) or configures a
    RAM card at address 0 with its handler at offset #F; the reverse
    steps return to the OS (src: [[sources/mastracci-saturn-guide]] 6.8).
    The guide says this only makes sense for programs written entirely in
    machine code that need the whole CPU, such as games.

## 3. Writing a ROM onto a real calculator

### 48SX, 48GX

- The 48SX's ROM is "commercial byte-wide" ROM on the memory controller
  bus (src: [[sources/hpj-48sx]] p. 30); the GX has a 512 KB byte-wide ROM
  chip (src: [[sources/teuwen-gx-hardware]] 6). No source read says
  whether it is mask ROM or OTP, and none describes a field
  reprogramming path. The FAQ says HP had a GX ROM upgrade programme in
  early 1994 that had ended (src: [[sources/hp48-faq]] 3.9); how it was
  done is not stated (unknown).
- Card-based replacement: the CPU always starts in NCE1 (ROM) at reset,
  because every other controller is unconfigured then
  ([[hardware/memory-controller]]). A card can only take over after the HP
  ROM has configured it at #00000, so no card replaces the boot code.
  ShellOS and the #0000F takeover above are the closest there is.

### 49G

- The 49G's 2 MB flash holds a 64 KB boot sector (first half of bank 0)
  that is write-protected in hardware, the system in banks 1-7 (plus the
  second half of bank 0 from ROM 1.19-6), and user banks (src:
  [[sources/saturn-tutorial]] p. 161-162; [[sources/hp49g-faq]] 1.7, 1.12).
  At reset bank 0 is at #00000 ([[hardware/hp49g]]), so **HP's boot code
  always runs first**, and any custom system on a real 49G is entered
  through it.
- The boot code offers a "No System" menu (+ and ENTER held, ON pressed);
  key 4 enters terminal mode, key 1 "Download System" receives a system
  from HP's PC Connectivity Kit 3.0 in about 15 minutes. After a failed
  upgrade: reset hole, then the same menu again (src:
  [[sources/hpcalc-homebrew-listings]] 49/50 ROM page). Because the boot
  sector cannot be erased, an erased or bad system should always be
  recoverable this way (src: [[sources/hp49g-faq]] 1.7); no source
  reports a 49G bricked by a bad system image (not checked on the HP
  Museum forum).
- Update files are `.flash`; Giesselink names their parts Part0 (boot),
  FS, System, Part1, Part2 ... (src: [[sources/saturn-tutorial]] p. 162).
  The first 256 bytes of every bank are reserved for identification data
  (src: [[sources/hp49g-faq]] 1.12). On saturnus, the HP boot code stops
  in "No System" when its bank scan finds no system
  ([[questions/hp49g-bank-latch-bits]]).
- **Unknown**: the `.flash` file layout, the serial protocol of "Download
  System" (the PC first sends a download program), and what the boot
  code checks before it starts a system (header fields, checksum,
  signature, version). No published description was found. Possible
  settlements: HP's connectivity kit was distributed under the GPL
  ([[sources/connectivity-kit]]), so its documentation or the owner's own
  HPComm sources (this wiki's documented exception) may describe the
  upload side; a black-box run of the update on an emulator would show the
  serial exchange.
- Flash writes themselves are documented: [[questions/hp49g-flash-write]].

## 4. Toolchains

| Tool | What | Licence | Source |
| --- | --- | --- | --- |
| HP SASM, RPLCOMP, SLOAD, MAKEROM (1991-1993) | HP's assembler, RPL compiler, loader, library builder; DOS binaries | HP copyright, free redistribution without charge | [[sources/sasm-reference]], [[sources/hp48-sdk-makerom]] |
| HP Tools 3.0.8 (Avenard, HP); GNU HP Tools 2.1.9 | the same tool set rebuilt for Linux, Win32 | GPL, with source | [[sources/hpcalc-homebrew-listings]] |
| Areuh (David, Taillandier) | assembler and linker, C, modern build | CeCILL-B (BSD-like); some 48 opcodes missing | [[sources/homebrew-projects-web]] |
| KASM | MASD-syntax assembler, C++ | GPL-2, unmaintained since 2013 | [[sources/homebrew-projects-web]] |
| ASAP, STAR, CLASS and others | older assemblers | various | [[sources/hpcalc-homebrew-listings]] |
| HP48XGCC (Alex Ramos) | GCC 2.7.2 C cross-compiler for the Saturn | GPL-2, libraries LGPL | [[sources/hpcalc-homebrew-listings]] details 3794 |
| Jazz; the 49G's built-in assembler | on-calculator assemblers and disassemblers; the 49G's is in ROM "but not supported" ([[sources/hp48-faq]] 2.14) | Jazz: not checked; the 49G's: HP's | not read |

No free standalone Saturn ROM or monitor program (one that runs from
#00000 without HP code) was found. HP48XGCC's output and every game above
run under the HP OS.

## 5. What a ROM must do at reset

From documentation and from booting HP ROMs on saturnus:

- **Entry at #00000.** SASM says a return through an empty return stack
  lands at address 0, "the memory reset code of the operating system"
  (src: [[sources/sasm-reference]] RTN). No HP document read states the
  CPU's program counter after a hardware reset; every HP ROM booted on
  saturnus starts executing at #00000 (observation).
- **Interrupt vector at #0000F**, a subroutine call for every interrupt
  (src: [[sources/sasm-reference]] 2.10). Code at #00000 must therefore
  jump over #0000F within the first 15 nibbles, and a handler (even a
  bare RTI) must be at #0000F before interrupts can fire. The return stack
  holds 8 entries and pops zeros when empty (src: [[sources/sasm-reference]]
  RTN).
- **Memory configuration.** After reset only NCE1 (ROM) is mapped; HDW
  (I/O, 64 nibbles), NCE2 (RAM), CE1, CE2 and NCE3 are unconfigured and
  are set up in daisy-chain order with CONFIG; all should be configured,
  unused ones at the minimum size (src: [[sources/saturn-tutorial]] p. 99,
  151-152; [[hardware/memory-controller]]). No RAM, so no data stack and
  no display bitmap, until NCE2 is configured. Per-model addresses (RAM
  at #70000 on the 48SX, #80000 on the GX and 49G, #F0000 on the 38G) are on
  [[hardware/memory-controller]] and the model pages.
- **Display.** Set the main bitmap address (#120), line count (#128),
  menu bitmap (#130), contrast (#101-#102) and display enable (#100 bit
  3) ([[hardware/display]], [[hardware/io-ram]]).
- **Timers and sleep.** TIMER2 runs from the 8192 Hz clock; SHUTDN stops
  the CPU until an interrupt such as ON or a timer
  ([[hardware/timers]], [[hardware/interrupts]]).
- **49G specifics.** The boot sector at #00000 is HP's on real hardware;
  bank switching through the CE1 latch ([[hardware/hp49g]]).

## Open

- The 49G boot code's acceptance test for a system image and the
  `.flash` format (above).
- How ShellOS and the Doom games take the machine over in detail (their
  documentation does not say; no source published).
- Whether the 48SX/GX ROM chips are mask ROM or OTP.
- The CPU's reset address from a hardware document (only inferred
  above).
