---
title: "Custom ROMs and replacement systems for the Saturn calculators"
type: question
status: open
tags: [rom, flash, homebrew, toolchain]
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
sources: ["[[sources/shellos-manual]]", "[[sources/hp49g-faq]]", "[[sources/hpcalc-homebrew-listings]]", "[[sources/homebrew-projects-web]]", "[[sources/sasm-reference]]", "[[sources/mastracci-saturn-guide]]", "[[sources/saturn-tutorial]]", "[[sources/hp48-faq]]", "[[sources/comp-sys-hp48-archive]]", "[[sources/hpmuseum-forum]]", "[[sources/wickes-rpl-name]]"]
---

# Custom ROMs and replacement systems for the Saturn calculators

Has anyone written their own operating system or ROM for the Saturn-based
calculators, and what would a free ROM that boots on an emulator, or on a
real 49G, have to do? Researched 2026-10-12 from web pages and manuals, then
the same day from the comp.sys.hp48 archive
([[sources/comp-sys-hp48-archive]], cited as C plus a message number) and
the HP Museum forum ([[sources/hpmuseum-forum]]). Status open: the 49G boot
code's full acceptance test and its download protocol are still only partly
known (see the end).

## 1. Replacement systems

- **On the Saturn models, one full replacement OS is documented: ShellOS**
  (Stephane Cocquereaumont, Philippe Dedieu, 1998-1999), a multitasking
  Unix-like system in assembly for the 48GX only, living on a 32 KB or
  128 KB RAM card in slot 1. Its authors say it uses no part of the HP ROM; it
  is started from the RPL OS and can switch back to it (src:
  [[sources/shellos-manual]] p. 2, 4, 12;
  [[sources/hpcalc-homebrew-listings]] details 79). It is not a ROM: the HP
  ROM still runs at reset, and ShellOS is entered from it. No source, no
  licence statement.
  - How it takes over: a user describes the installer copying the kernel to
    the card at #C0000 and mapping it over #00000 (src:
    [[sources/comp-sys-hp48-archive]] C12, user report, not the authors).
    Emu48's maintainer expected the memory controllers to be cleared by the
    reset button, "otherwise ShellOS ... will be restart[ed] after a
    hardware reset" (src: [[sources/comp-sys-hp48-archive]] C15), which fits
    a kernel mapped at address 0. Avenard, arguing that ShellOS was not
    serious enough for a product, describes its multitasking as done in
    software: look at the instruction about to run, save the registers, run
    it, restore the context; he guessed this at some 50 times slower than
    hardware support (src: [[sources/comp-sys-hp48-archive]] C13).
  - Fate of the code: the sources were lost to a disk crash and then a
    theft; in 2001 Cocquereaumont planned a public-domain release of an
    older copy (src: [[sources/comp-sys-hp48-archive]] C11). No such release
    was found. He also judged the audience for a 48 OS too small to be worth
    it (src: [[sources/comp-sys-hp48-archive]] C8).
- **Other OS projects, none finished** (all 2001, all started from HP's
  system, not at reset):
  - **SOS** (Saturn Operating System), Clément Pillias: a multitasking
    kernel for the 49G, adaptable to the 48, only a small part of the kernel
    written, sources published on his site (src:
    [[sources/comp-sys-hp48-archive]] C7).
  - **WinSOS**, Yves Brissaud: an OS mainly for the 49G, "maybe" also the 48
    and 39G/40G, with no RPL stack, switching to and from the HP OS as
    ShellOS does (src: [[sources/comp-sys-hp48-archive]] C10; C7).
  - A modular multitasking OS for the 48 by George Tsiros, Jonathan Busby
    and others, abandoned at the design stage; Busby turned to improving
    HP's RPL OS from a RAM card in port 1 instead, without a new ROM chip
    (src: [[sources/comp-sys-hp48-archive]] C9, C41).
- **Meta Kernel** (Maubert Development Group, 48GX, sold on a 128 KB ROM
  card for slot 1) replaces the user interface (stack, editor, writers,
  filer) but keeps HP's RPL kernel and stays compatible with it (src:
  [[sources/hpcalc-homebrew-listings]] Meta Kernel review;
  [[sources/hp48-faq]] 13.1). Jean-Yves Avenard, one of its authors,
  presented the 49G for HP's calculator division, whose system was
  "completely rewritten, mainly in assembly" and is said to be faster than a
  48 running the Meta Kernel (src: [[sources/hp48-faq]] 2.14).
- **newRPL and HPGCC are ARM-only.** newRPL installs on the 50g, 39g+, 39gs,
  40gs, 48gII and Prime G1, all ARM machines, through their ARM boot
  loaders; its installation guide names no Saturn model. HPGCC compiles C
  for the ARM models. No Saturn port of either was found (src:
  [[sources/homebrew-projects-web]] newRPL, HPGCC).
- **Self-built and modified HP systems on the 49G.** Bernard Parisse's
  "casrelease" package lets anyone build the 49G's ROM 1.24 with Cygwin; the
  build produces a `Vger.flash` for HP's flashing tools, an Emu48 image and
  a raw binary for a flash programmer (src:
  [[sources/comp-sys-hp48-archive]] C32). Only Parisse's CAS in it is LGPL;
  the rest of the ROM is HP's and the built binary may not be redistributed
  (src: [[sources/comp-sys-hp48-archive]] C33). Parisse also released a 49G
  ROM update built from it that adds an interactive geometry application
  (src: [[sources/comp-sys-hp48-archive]] C34). These are the only
  third-party systems found flashed onto a real 49G, and they are HP's
  system rebuilt, not replacements.
- **Later HP ROMs on the 49G**: the 49g+/50g "emulator" builds up to at
  least 2.10 were installed on the original 49G (src:
  [[sources/homebrew-projects-web]] Wikipedia; secondary). These are HP's
  own system, not homebrew; the saturnus observation that the 2.15 image
  boots as a 49G fits ([[hardware/hp49g]] "Bring-up findings").
- **38G, 39G, 40G**: no replacement system was found. The 38G's ROM is
  one-time-programmable and the 39G/40G's is mask ROM ([[hardware/hp38g]],
  [[hardware/hp39g-40g]]), so there is nothing to flash. The 39G still has a
  "no system" screen, reached the same way as the 49G's (reset, hold + and
  ENTER, press ON) (src: [[sources/comp-sys-hp48-archive]] C40); what it
  offers there was not found.
- **Nobody booted their own code at #00000** on a 48 or 49, as far as the
  archive shows. In 2000 Giesselink asked what the CPU holds after a
  hardware reset and said it was nearly impossible to test on a real machine
  without replacing the ROM; he added that HP's calculator division did not
  seem to know either (src: [[sources/comp-sys-hp48-archive]] C14, C15). A
  2005 thread asking to replace the 48's ROM chip with a rewritten system
  ended with "HP has been there and done that, and called it the HP49G" and
  with Emu48 as the practical answer (src: [[sources/comp-sys-hp48-archive]]
  C1, C2).

## 2. Programs that take the machine over

- Doom-style games exist for the 48: **Doom Preview II** (Clément Pillias,
  1998, greyscale, two monsters, four weapons) and **Doom Alpha for the GX**
  (Cyrille de Brébisson and Paul Lancaster, 1997, partly greyscale, two
  players over the link), plus first-person maze games (Lab3D, Rescue 3D)
  (src: [[sources/hpcalc-homebrew-listings]] details 486, 487, arcade page).
  Many fast 2D greyscale games were written in Saturn assembly, 1995-1998
  (src: [[sources/homebrew-projects-web]] sunhpgames).
- **Speed and technique, from Brébisson's own posts**: a ray-casting engine
  in the style of Wolfenstein 3D for the 48G and 48S; 26 frames a second in
  November 1995, 50 a second in greyscale by January 1996; no monsters, only
  the other players, linked by wire; the game shows its own frames per
  second and link bytes per second on screen and offers a choice of
  rendering engines. He wrote it to prove a 3D engine was possible and
  called it unfinished and buggy (src: [[sources/comp-sys-hp48-archive]] C4,
  C5, C6). The engine's internals are not described. Pillias' Doom Preview
  II for the 49G is on hpcalc.org with its source (src:
  [[sources/hpcalc-homebrew-listings]] search page; source not opened);
  Pillias was still working on Doom in 2001 (src:
  [[sources/comp-sys-hp48-archive]] C7).
- A 1990s "Doom for the HP48" rumour came from a GROB slideshow of Doom
  screenshots made with ANIMATE, as its maker explained in 1996 (src:
  [[sources/comp-sys-hp48-archive]] C3).
- All of these run as code objects started from the RPL OS, not as ROMs
  (src: [[sources/hpcalc-homebrew-listings]]).
- The documented takeover techniques:
  - Disable interrupts (INTOFF masks only the keyboard; to stop everything
    including ON the ROM's own routine leaves the CPU "in service" after a
    deliberate interrupt) ([[hardware/interrupts]] "Interrupt-in-service
    flag").
  - Write the display registers directly: main bitmap start #120, line
    count #128, menu bitmap #130 ([[hardware/display]]); greyscale code waits on
    the row counter read from #128 and flips buffers (src:
    [[sources/mastracci-saturn-guide]] 6.1, via [[hardware/display]]).
  - Scan the keyboard with OUT and C=IN ([[hardware/keyboard]]).
  - Own interrupt handler: every Saturn interrupt is a subroutine call to
    address #0000F (src: [[sources/sasm-reference]] 2.10), which is ROM, so
    a program maps RAM at #00000 (copying low RAM away first) or configures
    a RAM card at address 0 with its handler at offset #F; the reverse steps
    return to the OS (src: [[sources/mastracci-saturn-guide]] 6.8). The
    guide says this only makes sense for programs written entirely in
    machine code that need the whole CPU, such as games. ShellOS appears to
    do the same with its kernel (section 1).

## 3. Writing a ROM onto a real calculator

### 48SX, 48GX

- The 48SX's ROM is "commercial byte-wide" ROM on the memory controller bus
  (src: [[sources/hpj-48sx]] p. 30); the GX has a 512 KB byte-wide ROM chip
  (src: [[sources/teuwen-gx-hardware]] 6).
- **Mask ROM in production.** Avenard (HP) says the 48 series uses masked
  ROM (src: [[sources/comp-sys-hp48-archive]] C18). Dave Arnett, who
  released the 48 ROMs, says G/GX prototypes ran from one-time-programmable
  ROM chips that he burned and soldered in himself, that this was never done
  to a unit sold to the public, and that HP does not replace ROM chips to
  upgrade a 48 (src: [[sources/comp-sys-hp48-archive]] C16). A user names
  the part as a 23C4000-type mask ROM (src:
  [[sources/comp-sys-hp48-archive]] C19, user report).
- **No field upgrade path.** Rev R was the last 48 ROM released to
  production; the early "upgrade" programme was for owners with real
  problems caused by early bugs and had ended by 1998 (src:
  [[sources/comp-sys-hp48-archive]] C17; matches [[sources/hp48-faq]] 3.9).
  How that programme swapped ROMs is still not stated.
- Card-based replacement: the CPU always starts in NCE1 (ROM) at reset,
  because every other controller is unconfigured then
  ([[hardware/memory-controller]]). A card can only take over after the HP
  ROM has configured it at #00000, so no card replaces the boot code.
  ShellOS and the #0000F takeover above are the closest there is. No
  card-boot trick was found in the archive.

### 49G

- The 49G's 2 MB flash holds a 64 KB boot sector (first half of bank 0) that
  is write-protected in hardware, the system in banks 1-7 (plus the second
  half of bank 0 from ROM 1.19-6), and user banks (src:
  [[sources/saturn-tutorial]] p. 161-162; [[sources/hp49g-faq]] 1.7, 1.12).
  At reset bank 0 is at #00000 ([[hardware/hp49g]]), so **HP's boot code
  always runs first**, and any custom system on a real 49G is entered
  through it.
- **What the boot sector does** (Avenard, HP, 1999): it holds the default
  tests that check the flash contents are valid and the basic upgrade
  software; if the system is missing or damaged it shows the download
  screen; after an interrupted download it checks again at the next boot and
  returns to that screen (src: [[sources/comp-sys-hp48-archive]] C20).
- **The write protection** can be lifted by shorting two points on the
  board; Brébisson (HP) did so to reprogram the boot block while debugging,
  and a botched attempt meant desoldering, programming and resoldering the
  flash chip (src: [[sources/comp-sys-hp48-archive]] C22). HP's own
  developers killed five 49Gs while upgrading boot sectors on early
  unprotected units; the only repair was a new flash chip (src:
  [[sources/comp-sys-hp48-archive]] C21). A flash chip replaced without a
  boot sector cannot be reflashed at all; HP's downloadable system images do
  not contain the boot sector, Emu48's full images do (src:
  [[sources/comp-sys-hp48-archive]] C31).
- **Bank 0's second half ("FS")** was set aside to take over from the boot
  sector if a serious bug were found in it, and must carry the name FS. ROM
  1.19 uses it as extra system space by posing as a new file system whose
  code patches the boot process; the 49G will not boot a system unless this
  bank was updated first (src: [[sources/comp-sys-hp48-archive]] C23, C25).
  Because it can replace the boot sector, a corrupted FS bank can make
  reflashing "tricky"; HP published a P0ERASE program to erase it from the
  terminal screen (src: [[sources/comp-sys-hp48-archive]] C24).
- **Bank header** (Brébisson, HP, 2000): each 128 KB bank carries a header
  from nibble offset #200 (byte #100). Its fields, in order: a bank-type
  code (ROM, RAM, System or Boot, with at most one System and one Boot bank
  in the flash); the physical bank number and an erase counter, which the
  programming step fills in, so a file must carry FFs there; the offset of
  the bank's CRC; a revision and an ID, both unused; an initialisation hook
  (zeros, or a call the boot runs at every start); and a count of "flash
  pointers" followed by the pointers (src: [[sources/comp-sys-hp48-archive]]
  C26). Giesselink's observation of uploaded banks agrees: type, bank number
  and further bytes at byte #100 differ between file and flash because the
  boot code rewrites them, and bytes 0-#FF of each bank hold code copied
  from the boot block (src: [[sources/comp-sys-hp48-archive]] C27). This
  fits the "first 256 bytes ... reserved for identification data" of
  [[sources/hp49g-faq]] 1.12. The exact type codes are in the post (src:
  [[sources/comp-sys-hp48-archive]] C26).
- **What "Download Pack" checks**: that the system in flash is valid and
  complete (src: [[sources/comp-sys-hp48-archive]] C23); a user reports a
  "Bad Crc error" at that step (quoted in [[sources/comp-sys-hp48-archive]]
  C43), so a CRC check is part of it. Whether the check covers anything
  beyond the bank headers and CRCs (a signature, a version) is not said;
  nobody in the archive mentions a signature.
- **`.flash` files**: a global header giving the number of parts, then for
  each part a header and the bank data (Bunte, who wrote a Linux flashing
  program, 2001; src: [[sources/comp-sys-hp48-archive]] C28). The parts are
  named FS, System, Part1 ... Part7 (Part5 absent in 1.17); the names inside
  the file seem unused (src: [[sources/comp-sys-hp48-archive]] C27;
  [[sources/saturn-tutorial]] p. 162). `ROM.E49` (Emu48) is instead a
  whole-chip image including boot sector and port 2 (src:
  [[sources/comp-sys-hp48-archive]] C30).
- **The download**: boot menu reached with reset, + and ENTER held, ON; key
  4 "terminal mode" shows a blank screen and, by Meyers' reading, waits for
  four special bytes from HP's Flash Download Program (FDP) or from another
  49G running ROMUPLOAD, which mark a proper sender (src:
  [[sources/comp-sys-hp48-archive]] C29, hedged "may be"). After that
  handshake the menu offers Download System, Download Packs, Upload Bank and
  Erase Bank (src: [[sources/comp-sys-hp48-archive]] C37; C42, user report).
  The XModem and flash-writing routines live in the boot loader; the
  49g+/50g still carry the original 49G boot loader for those routines but
  do not boot from it (src: [[sources/comp-sys-hp48-archive]] C35). The
  transfer itself takes about 15 minutes (src:
  [[sources/hpcalc-homebrew-listings]] 49/50 ROM page). The exact four bytes
  and the per-bank framing were not found.
- **Recovery**: from "Try To Recover Memory" or a dead screen, reset with +
  and ENTER held, press ON, then key 4 and FDP (src:
  [[sources/comp-sys-hp48-archive]] C36); Download System rather than
  Download Packs after a bad CRC (src: [[sources/comp-sys-hp48-archive]]
  C43).
- **Bricking**: no report of a 49G bricked by a bad system image was found.
  The bricked units in the archive had a damaged boot sector (src:
  [[sources/comp-sys-hp48-archive]] C21) or a damaged FS bank (src:
  [[sources/comp-sys-hp48-archive]] C24). The risk for a custom system lies
  in bank 0's second half, not in banks 1-7.
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
| casrelease (Parisse) | builds the 49G ROM 1.24 into `.flash`, Emu48 and raw images (src: [[sources/comp-sys-hp48-archive]] C32) | CAS LGPL; the rest HP's, not redistributable (src: [[sources/comp-sys-hp48-archive]] C33) | contains HP ROM objects: off limits for clean-room work |

No free standalone Saturn ROM or monitor program (one that runs from #00000
without HP code) was found. HP48XGCC's output and every game above run under
the HP OS.

## 5. What a ROM must do at reset

From documentation and from booting HP ROMs on saturnus:

- **Entry at #00000.** SASM says a return through an empty return stack
  lands at address 0, "the memory reset code of the operating system" (src:
  [[sources/sasm-reference]] RTN). No HP document read states the CPU's
  program counter after a hardware reset; every HP ROM booted on saturnus
  starts executing at #00000 (observation).
- **Register state after reset is undocumented**, even to Emu48's
  maintainer, who found HP's calculator division unsure (src:
  [[sources/comp-sys-hp48-archive]] C14, C15). A ROM should set every
  register, mode flag and I/O register it relies on.
- **Interrupt vector at #0000F**, a subroutine call for every interrupt
  (src: [[sources/sasm-reference]] 2.10). Code at #00000 must therefore jump
  over #0000F within the first 15 nibbles, and a handler (even a bare RTI)
  must be at #0000F before interrupts can fire. The return stack holds 8
  entries and pops zeros when empty (src: [[sources/sasm-reference]] RTN).
- **Memory configuration.** After reset only NCE1 (ROM) is mapped; HDW (I/O,
  64 nibbles), NCE2 (RAM), CE1, CE2 and NCE3 are unconfigured and are set up
  in daisy-chain order with CONFIG; all should be configured, unused ones at
  the minimum size (src: [[sources/saturn-tutorial]] p. 99, 151-152;
  [[hardware/memory-controller]]). No RAM, so no data stack and no display
  bitmap, until NCE2 is configured. Per-model addresses (RAM at #70000 on
  the 48SX, #80000 on the GX and 49G, #F0000 on the 38G) are on
  [[hardware/memory-controller]] and the model pages.
- **Display.** Set the main bitmap address (#120), line count (#128), menu
  bitmap (#130), contrast (#101-#102) and display enable (#100 bit 3)
  ([[hardware/display]], [[hardware/io-ram]]).
- **Timers and sleep.** TIMER2 runs from the 8192 Hz clock; SHUTDN stops the
  CPU until an interrupt such as ON or a timer ([[hardware/timers]],
  [[hardware/interrupts]]).
- **49G specifics.** The boot sector at #00000 is HP's on real hardware;
  bank switching through the CE1 latch ([[hardware/hp49g]]). A system in
  banks 1-7 needs the bank header above, type System, with a correct CRC.

## 6. Forth and the name RPL

- **The name.** Bill Wickes, who led the HP-28C project, wrote in 1991 that
  RPL stands for Reverse Polish Lisp, coined by the development team as a
  play on RPN and a nod to its Forth and Lisp roots; "ROM-based procedural
  language" was a more dignified reading of the same initials made up around
  the August 1987 HP Journal article on the HP-28C (src:
  [[sources/wickes-rpl-name]]). His 1987 and 1988 articles say the same:
  Forth's threaded interpretation and unlimited stacks, Lisp's list handling
  and local variables; first shipped in the HP-18C in June 1986; the Saturn
  gained PC=(A) for threaded execution (src: [[sources/hpmuseum-forum]] F2;
  [[sources/comp-sys-hp48-archive]] C38).
- **Forth on the 48 and 49 (Saturn).** None was found. Requests for one in
  1993, 1995, 1999 and 2009 got no answer naming one (src:
  [[sources/comp-sys-hp48-archive]] C39; [[sources/comp-sys-hp48-archive]]
  "Searches"). The Saturn Forth that exists is HP's FORTH/Assembler ROM for
  the HP-71B, written by the team that went on to RPL (src:
  [[sources/hpmuseum-forum]] F2). The one Forth for a 49-series machine on
  hpcalc.org is "Forth 1.0" (Darren Hosking, 2023), a C port of Jonesforth
  for HPGCC, so for the ARM 49g+ and 50g only (src:
  [[sources/hpcalc-homebrew-listings]] search page).

## Open

- The 49G download protocol in detail: the four handshake bytes (if that
  reading is right), the per-bank framing, and whether anything beyond the
  bank headers and per-bank CRCs is checked. A black-box run of FDP or
  ROMUPLOAD against an emulated 49G would show the serial side; the owner's
  HPComm sources are the other documented route.
- The CRC algorithm and coverage of a bank's CRC.
- How HP's early 48 ROM upgrade programme swapped ROMs.
- The CPU's reset address and register state from a hardware document (only
  inferred above).
- The technique of the Doom engines (only speed and feature claims are
  published).
