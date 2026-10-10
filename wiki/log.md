---
title: Log
type: meta
---

# Log

Append-only. One entry per operation, newest last.

## [2026-10-04] setup | Wiki created

Skeleton created: `.hyalo.toml` schema, `CLAUDE.md`, `wiki/` directories,
`index.md`, `log.md`, `overview.md`. Raw sources are being collected into `raw/`.

## [2026-10-04] ingest | Mastracci, Guide to the Saturn Processor 1.0b

Read `saturn.txt` in full (sections 1-8; 5.12-5.17 schematics skimmed).
Created [[sources/mastracci-saturn-guide]] and seeded [[hardware/saturn-cpu]],
[[hardware/memory-controller]], [[hardware/io-ram]], [[hardware/interrupts]],
[[hardware/timers]], [[hardware/uart]], [[hardware/display]],
[[hardware/keyboard]], [[hardware/crc]], [[hardware/card-ports]]. Sections 3.2
and 3.3 are empty in the source. Raised six questions: C=IN even address,
timer expiry, UART LPB bit, display offset axis, shift-key matrix positions,
CRC register width.

## [2026-10-04] ingest | Small hardware notes: Taplin, Gariepy CRC, Brittenson, Ervin, Horn, Teuwen, HP49G notes

Read in full: `hdwreg.txt`, `checksum.txt`, `screen/SCREEN`,
`input/keybrd_input.txt`, `bank.txt`, `hphard/hphard.txt`, `memmap.txt`,
`memory49.txt`, `keyb49.txt`, `Buf49.PDF`, `serial49.pdf` (Portuguese). The
`mlstarterkit` bundle was not in scope and stays unread.
Eleven source pages. Created [[hardware/hp48sx]], [[hardware/hp48gx]],
[[hardware/hp49g]]; merged into crc, uart, display, keyboard, interrupts,
timers, card-ports, memory-controller. Findings: CRC is 16-bit (answered
[[questions/crc-register-width]]); Taplin disagrees with Mastracci on the
UART #110/#112 bits; Ervin's "yel"/"blu" labels favour Teuwen's shift-key
positions; Sylvester says C=IN needs an odd address, Mastracci even; Teuwen
v0.05 is the origin of Mastracci ch. 5, so not independent. New questions:
uart-register-bit-layout, display-start-address-taplin, register-10e-role,
hp48sx-rom-extent, hp49g-ram-controllers, hp49g-flash-write.

## [2026-10-04] ingest | Fernandes and Rechlin, Introduction to Saturn Assembly Language (3rd ed.)

Extracted with pdftotext. Read ch. 21-31, 32-54 (instruction set), 60
(keyboard), 66-68 (Giesselink's HP48/HP49 memory management and graphics).
Skipped Parts I-II, V (objects) and the programming examples. The book has no
UART or timer chapter. Created [[sources/saturn-tutorial]] and
[[sources/sasm-reference]] (skimmed, only to settle the HST order). Updated
saturn-cpu (encoding facts, DEC-mode carry bug, RSTK behaviour, timing),
memory-controller (controller model, C=ID codes, default maps, GX bank latch
off-by-one and write-protect wiring), display (refresh, margins, bit names,
ghost registers), keyboard, interrupts (in-service flag, SHUTDN wake),
timers, hp48sx, hp48gx, hp49g, uart. Answered: hst-bit-order (Mastracci
wrong), keyboard-matrix-shift-keys (Mastracci 4.9 wrong), display-offset-axis
(horizontal), hp48sx-rom-extent, hp49g-ram-controllers. New:
hp49g-bank-latch-bits (Sousa vs Giesselink). Mastracci's 48SX RAM size
(128 KB) is contradicted by the tutorial's 32 KB.

## [2026-10-04] ingest | Duchesne, HP48 Interrupts: Tricks to Know (EN rev. 07c; FR original)

English PDF read in full via pdftotext (cited by PDF page). French
`hpregint.txt` spot-checked against it (skimmed). The ROM disassembly in 2.2
was read for behaviour only; no code copied. Created
[[sources/duchesne-interrupts-en]], [[sources/hpregint-fr]]. Updated
interrupts (ordered list of what the ROM handler checks, S-only C=ID check,
RSTK budget), timers (TIMER2 must run; timekeeping), uart (transmit busy-wait
in the handler), keyboard (OUT shadow on G, ON combinations). New question
[[questions/interrupt-maskability]]; added #10E evidence to
[[questions/register-10e-role]]. Both language editions say flag 15 "set"
where the code means "clear".

## [2026-10-04] ingest | Courbis and Lalande, Voyage au centre de la HP 48 G/GX

pdftotext gives an OCR text layer (noisy). Read part 2 ch. 2 (Saturn, IN/OUT,
module managers; book p. 79-90), ch. 3 excerpts (p. 102, 130-132), ch. 5
(memory organisation, p. 183-190), ch. 6 (I/O RAM, p. 191-204), ch. 7 (bank
switcher, p. 205-207). Skipped part 1, ch. 4, 8, 9 and the program library;
the keyboard table scan (book p. 81) is unreadable as OCR. Facts written in
English. Created [[sources/voyage-48gx]]. Updated io-ram (names for every
nibble, battery test procedure, #11F meaning), uart (Voyage confirms
Mastracci; IR modes), timers (control bit functions, TIMER2 ranges), display,
memory-controller, interrupts (RSI semantics, ST 13-15), keyboard, saturn-cpu.
Answered: uart-register-bit-layout (Taplin wrong), c-equals-in-even-address
(even). New contradictions: [[questions/da19-polarity]] (tutorial vs
Mastracci+Voyage), [[questions/bus-priority-ce1-ce2]] (Voyage vs
Giesselink+Mastracci), [[questions/config-check-in-handler]] (Voyage vs
Duchesne).

## [2026-10-04] ingest | HP 48 FAQ 4.62 (I/O and object sections) and HP 48 I/O Technical Interfacing Guide

FAQ (text edition): read 2.14, 3.1-3.2, 3.4-3.5 excerpts, 4.1, 4.6-4.7,
4.19, 6.4-6.18, 6.23, 6.26-6.27, 7.6, 8.13, 8.18, 8.19 excerpt, 9.2,
12.1-12.3; the rest is user-level and skipped. I/O guide: `io2.txt` read in
full. Created [[sources/hp48-faq]], [[sources/io-guide]] and the first
protocol pages: [[protocols/hp-object-format]] (draft),
[[protocols/kermit-hp]], [[protocols/iopar]], [[protocols/server-commands]]
(stubs, to be filled by the Kermit hints and manuals). Updated uart (HP's
frame format, 11.375 bit times per byte, receiver model, IR pulse format,
inter-byte gap rule), hp49g (no IR, XModem variants, 15360 internal),
display (pixel order), interrupts (WSLOG codes). New questions:
binary-odd-nibble-padding, ascii-header-spelling.

## [2026-10-04] ingest | Protocols: Kermit manual, HP-48 Kermit hints, XMODEM/YMODEM reference, XSERV material

Kermit Protocol Manual 6th ed.: pdftotext, read ch. 1-7.1 and App. I in full;
6.6, 7.2 (windows), 8, 9 skimmed. HP-48 Kermit hints: read in full.
`ymodem.txt`: sections 2, 4, 5, 7, 8 read (Christensen's overview is section
7 in this file, not 6 as raw/README says). ZMODEM: intro only (skimmed).
XSERV: ROM 1.18 notes, XSrvr48 and Conn4x ReadMe, five Conn4x help pages,
`YModem.pas` read for facts only (non-commercial licence; nothing copied),
`FixObj.pas` glanced at. Created sources kermit-protocol-manual,
hp48-kermit-hints, ymodem-reference, zmodem (skimmed), conn4x-ymodem-pas,
conn4x-help, xsrvr48-readme, hp49-rom118-notes; protocols
[[protocols/kermit]], [[protocols/xmodem]], [[protocols/xmodem-hp]],
[[protocols/xserv]]; filled [[protocols/kermit-hp]], [[protocols/iopar]],
[[protocols/server-commands]] (now draft). Findings: Kermit type 3 CRC is the
same LSB-first CRC as the Saturn hardware CRC (constant 4225 = #1081); XMODEM
CRC is the MSB-first 0x1021 form; HP Kermit requires all control characters
prefixed; 49G rejects objects with more than about 255 bytes of XModem
padding; XSERV arrived after ROM 1.10. New question:
[[questions/xmodem-hp-crc-mode]].

## [2026-10-04] ingest | Manuals: I/O chapters

- 49G User's Manual: text layer uses a +29 shifted font; decoded Appendix A
  (p. A-1 to A-3), the only I/O material. Digested.
- 39G/40G User's Guide: read p. 16-4/16-5 and the annunciators; no protocol
  named. Digested.
- HP 48 Serial Interface Kit guide (1994, Sparcom Link48): English part
  read in full. Digested.
- PC Connectivity Kit guide (1999): English part read. Digested.
- 48G User's Guide (scan): chapter 27 read as images (PDF 369-384) plus
  flags D-3/D-4 (PDF 469-470). Skimmed.
- 48G AUR (scan): contents and Appendix D IOPAR (PDF 703-707) read; I/O
  command entries located but not read. Skimmed.
- 49G Advanced User's Guide and 38G guide (scans): not opened.

Rewrote [[protocols/iopar]] from the AUR (all six fields, defaults, flags
-33 to -39). Updated kermit-hp, server-commands (HP's own list: GET, SEND,
REMOTE DIR, REMOTE HOST, FINISH, LOGOUT), xmodem-hp (48G XModem has no CRC).
Created stub pages [[hardware/hp38g]] and [[hardware/hp39g-40g]].

## [2026-10-04] ingest | Emu48 manual, KML 2.0, Emu48 CHANGES.TXT

Emu48 manual read in full; KML 2.0 read for the Hardware/Model/Class
keywords, contrast table and the HP48/HP49 OutIn tables. CHANGES.TXT (SP1-68)
filtered to hardware modules and read; no Emu48 code opened. CHANGES has no
dates, so citations use service-pack numbers. Created [[sources/emu48-manual]],
[[sources/kml20]], [[sources/emu48-changes]] and [[emulators/emu48]].
Answered: da19-polarity (DA19 = 0 disables upper ROM; Mastracci and Voyage
reversed), timer-expiry-semantics (TIMER2 fires on wrap through zero),
uart-lpb-bit (loop-back); added evidence to c-equals-in-even-address,
register-10e-role, bus-priority-ce1-ce2. New: dec-mode-constant-bug (Emu48
vs tutorial), contrast-range-48gx (Voyage vs KML). Cross-linked findings into
memory-controller, timers, uart, interrupts, keyboard, display, saturn-cpu,
crc.

## [2026-10-04] lint | First full lint pass

`hyalo lint` clean (86 files); no orphans, no broken links; no source left
`unread` (5 skimmed: sasm-reference, hpregint-fr, zmodem, hp48g-ug,
hp48g-aur). Fixed seven dead-end source pages by linking the pages they feed.
Swept for claims overturned by later sources: corrected the DA19 polarity in
memory-controller, hp48gx and display, and the #100 offset axis in display.
Stubs: hardware/hp38g, hardware/hp39g-40g. Questions: 23 (12 open, 11 answered).
Rewrote [[overview]] with the big picture and the next sources to ingest
(RPLMAN, HP Journal 38G, first Voyage book, and the x48ng and saturnng
manuals and change logs: documentation only; their source code is not read
for this wiki).

## [2026-10-04] setup | Linked the hptx and saturnus repos

Overview now names both consumer repos and their `kb/` plans.

## [2026-10-04] ingest | SASM manual sections 2.7, 6.5-6.6, 8 for the saturnus CPU core

Read while implementing the Saturn CPU in saturnus iteration 1. Added
"Facts settled while building saturnus" to [[hardware/saturn-cpu]] (constant
forms always hex, carry rule of A=-A-1, SB rules per shift direction, NOP3
820 vs 420, 80B is BUSCC, relative branch bases, D0=AS width, RSI) and a
working answer to [[questions/dec-mode-constant-bug]]. The saturnus decoder
round-trips 391 of the 485 records in HP's `SASM.OPC`; the rest are
assembler directives and pseudo-ops.

## [2026-10-05] ingest | hptx iteration 3 emulator observations

Observations from driving the three emulated models with the hptx Kermit
client: C reply format and errors, ARCHIVE in server mode, the port-0
backup and restore route, flag -35, binary headers per ROM, the TRANSIO 3
trigraph table and the directory object layout. Added to
[[protocols/server-commands]] and [[protocols/hp-object-format]];
[[questions/ascii-header-spelling]] answered.

## [2026-10-05] query | saturnus iteration 3: carry-over checks against saturnng

Compared the clean-room saturnus emulator with saturnng 6.1.1 (black box in
Docker, 48SX, ROM J, empty slots) by screen and by saturnng's
`--debug-implementation` instruction log. Results in
[[hardware/memory-controller]] and [[hardware/card-ports]] ("Checked
against saturnng"): the stock ROM never reads the empty slot windows and
never overlaps CE1 with CE2, so neither the open-bus value nor
[[questions/bus-priority-ce1-ce2]] can be settled this way; the experiments
that would settle them are listed there. [[hardware/interrupts]]: the OFF
path executes SHUTDN with OUT = #000 and saturnng wakes it through #0000F,
not by jumping to zero. Also seen: saturnng's TUI keeps drawing the display
bitmap while the ROM has switched the display off, so its screen dumps are
not comparable in the OFF state.

## [2026-10-05] query | 48SX #10F card bits follow the chip select

ROM J tests the card flagged by #10F bits 1/3 in the CE2 window (#C0000)
and calls it port 2; bits 0/2 go with CE1 (#80000, port 1). Mastracci's
port names for #10F fit the GX, not the SX. Found while matching a RAM-card
scenario between saturnus and saturnng; details in [[hardware/card-ports]].

## [2026-10-05] query | Open-bus probe on saturnng (48SX)

A hand-assembled machine-code probe sent over Kermit (hptx) read #80000,
#C0000 and #D0000 on saturnng's 48SX with empty slots: all zero, with a ROM
control read confirming the reads. Emulator against emulator, not hardware;
recorded in [[hardware/memory-controller]]. The CE1/CE2 priority stays open
([[questions/bus-priority-ce1-ce2]]): saturnng cannot load a 48SX port-2
card and saturnus only reports the priority it implements.

## [2026-10-05] query | ROM J serial path and clock drift (saturnus)

Disassembled and traced ROM J's serial code while building the saturnus
UART: the handler polls IOC/RCS (never #118), waits between received bytes
with a baud-indexed TIMER2 timeout of about four frames (table at #0048B),
and the send path polls TBF before writing TBR. Its Kermit server received
a file over the emulated UART. ROM J's displayed clock showed no measurable
drift over ten emulated minutes. Details in [[hardware/uart]] and
[[hardware/timers]].

## [2026-10-05] question | Instruction speed versus real hardware

Opened [[questions/instruction-speed-vs-hardware]]: saturnus runs a 2000
iteration `START NEXT` loop in 5.97 s of emulated time; saturnng's 145 ticks
is no oracle. Needs a measurement on a real 48SX.

## [2026-10-05] ingest | HP 38G, 39G and 40G hardware

Ingested the three HP Journal June 1996 38G articles ([[sources/hpj-38g]]),
the image-only 38G user's guide through a local OCR pass
([[sources/hp38g-ug]]), the 39G/40G guide's keyboard, reset and 38G
differences pages, and four web pages: Finseth's 38G data sheet with
Detlef Mueller's note that the 32 KB RAM sits at #F0000-#FFFFF
([[sources/finseth-hp38g]]), Gießelink's Emu48 history, which gives the
39G/40G as a 49G cut to 1 MB ROM and 256 KB RAM with one ROM for both
([[sources/giesselink-emu48-25-years]]), the hpcalc.org ROM listings
([[sources/hpcalc-rom-listings]]) and Wikipedia's 39/40 rows
([[sources/wikipedia-hp39-40]]). [[hardware/hp38g]] and
[[hardware/hp39g-40g]] went from stub to draft, with key-by-key matrix
tables (positions inferred from the keyboard figures and Emu48's OutIn
tables) and ROM availability: 38G A1.67 (`38grom.zip`) and the 39/40 ROM
(`rom3940.zip`) are on hpcalc.org, but without the "HP graciously began
allowing this" sentence the 48 pages carry. Opened
[[questions/hp38g-memory-controllers]], [[questions/hp39g-40g-memory-map]],
[[questions/hp39g-40g-model-detection]],
[[questions/hp38g-39g-transfer-protocol]] and
[[questions/hp38g-release-date]]. Emu48's CHANGES.TXT was not re-read (it
sits in the reference tree); its 39G entries could answer the model
detection question.

## [2026-10-05] ingest | Intel 28F160S5 datasheet; 49G ROM images

Added the Intel 28F160S5/28F320S5 datasheet (order 290609-004, Wayback copy
of Intel's developer site) as `raw/saturn-hardware/datasheets/` and
[[sources/intel-28f160s5]]: command set, status register, identifier and
query data, erase block layout. Facts carried to [[hardware/hp49g]] and
[[questions/hp49g-flash-write]] (still open on the nibble-to-byte write
path). Inspected the hpcalc.org ROM images: the "ROM for Emulators 2.15"
zip is labelled for the 49g+/50g and lacks the original boot sector, but
saturnng boots it as a 49G; the 2.10 package and the 1.19-6 beta carry the
original 49G boot sector ([[hardware/hp49g]]).

## [2026-10-05] saturnus | 48GX ROM R on saturnus vs saturnng

saturnus now emulates the 48GX: DA19, the CE1 bank latch, port 1 on CE2
and banked port 2 on NCE3. Boot, keys, menus, OFF/ON and the card ports
match saturnng. New facts are on [[hardware/hp48gx]] ("Facts settled while
building saturnus") and [[hardware/card-ports]] ("Checked on the 48GX"):
#11F holds 8 and the contrast 14 after boot. ROM R latches banks with byte
reads at #7F040 + 2n. The 4 MB-card "Invalid Card Data" appears with an
exact latch. The #10F bit pairing follows the chip select on the GX.

## [2026-10-05] saturnus | 38G ROM A1.67 boots as a 48G configuration

The 38G ROM boots on saturnus with a 48G-style wiring: RAM on NCE2, which
the ROM configures at #F0000 itself, and the CE1 latch and DA19 as on the
GX. It shows a "Memory Clear" box, then HOME, and takes key input (`6*7`
gives 42). Recorded on [[hardware/hp38g]] and as a partial answer on
[[questions/hp38g-memory-controllers]]. There is no oracle.

## [2026-10-05] query | HP 49G bring-up on saturnus

Booted ROMs 2.15, 2.10 and 1.19-6 on the saturnus emulator. Answered
[[questions/hp49g-bank-latch-bits]]: Sousa's bit order (A1-A4 high view,
A5-A6 low view) boots, Giesselink's does not. Found that SHUTDN must not
clear the 49G's latch, that the ROM programs port 2 with write to buffer
([[questions/hp49g-flash-write]]), and the alpha letter positions
([[hardware/keyboard]]). Details on [[hardware/hp49g]].

## [2026-10-06] query | UART interrupt must stay a level across RTI (ROM J)

A Kermit server in saturnus went deaf when a packet arrived 1-3 ms after its
NAK: the start-bit interrupt hit while ST bit 15 was clear, ROM J returned
without servicing it, and its later RSI/RTI saw no new edge. Treating the
UART request as a level at RTI fixes it. Details in [[hardware/uart]].

## [2026-10-05] query | XModem behaviour measured on saturnng by hptx

The hptx project drove XRECV and XSEND on the saturnng emulator (49G ROM
2.15, 48GX ROM R) and recorded byte traces. Not yet confirmed on hardware.
Answered [[questions/xmodem-hp-crc-mode]]: the 49G receiver opens with `D`,
which is CRC-16/KERMIT sent high byte first, and the 48GX is checksum only.
Block sizes, padding and name clashes are on [[protocols/xmodem-hp]]. The
server refuses XRECV/XSEND with "Port Not Available" ([[protocols/server-commands]]).
49G IOPAR must hold reals ([[protocols/iopar]]).

## [2026-10-05] ingest | HP Museum summation benchmark; 39G/40G alpha letters from the user's guide figure

Created [[sources/hpmuseum-summation-benchmark]] and added real-hardware
versus saturnus timings to [[questions/instruction-speed-vs-hardware]]:
saturnus runs the 48SX about 16% and the 48GX about 35% too fast. Read the
39G/40G user's guide keyboard figure (page 1-3) as an image and added the
alpha letter table to [[hardware/hp39g-40g]].

## [2026-10-05] query | Timing calibration (saturnus iteration 7); 39G/40G letters corrected

Profiled the summation benchmark on saturnus and narrowed
[[questions/instruction-speed-vs-hardware]]. The G series needs the Meta
Kernel cycle counts ([[hardware/saturn-cpu]] "Timing"), and a 19-34%
residual remains with no documented cause. Skimmed the HP Journal 48SX
and 48G/GX articles for clock facts ([[sources/hpj-48sx]],
[[sources/hpj-48gx]]); added a speed note to [[hardware/hp48gx]].
Corrected the 39G/40G alpha table in [[hardware/hp39g-40g]] from the ROM's
behaviour: the letters belong one key row up from the first reading of
the user's guide figure.

## [2026-10-05] ingest | RPLMAN object bodies; binary objects decoded by saturnus (iteration 9)

Skimmed chapter 3 of RPLMAN ([[sources/rplman]]) for the object bodies:
real, complex, string, hex string, array, unit, names. Added an "Object
bodies" section to [[protocols/hp-object-format]] with byte-level examples
checked on the 48SX and the 49G in saturnus, the 49G integer layout
(observed, no document), ROM pointers to built-in constants inside lists,
and what the calculators accept in a binary send (the ROM letter is not
checked; the 48SX turns a file with a trailing extra byte into a string).

## [2026-10-05] query | System RAM pointers and directory layout (saturnus iteration 12a)

Read the RAM part of the 1991 internals address list
([[sources/hp48-internals-address-list]], the `mlstarterkit` bundle) and
RPLMAN 19.2-19.3 on directories. Created [[hardware/hp48-system-ram]]:
HOME, end of HOME, current directory, saved D1, stack end and flag words
for the 48SX (list confirmed on ROM J), 48GX and 49G (found by RAM scans,
RAM diffs across SF/CF and a trace of the ROM's D1 restore in saturnus);
HOME's library-count header, the variable record chain, the BYTES size
and checksum rule, the stack's end marker, the 49G's RCLF word order and
its algebraic-mode stack. Linked from [[hardware/hp48sx]],
[[hardware/hp48gx]] and [[hardware/hp49g]].

## [2026-10-05] ingest | HP 42S and the Lewis chip (saturnus iteration 15)

Read the Emu42 manual ([[sources/emu42-manual]]), the Pioneer ROM dump notes
and LEWISCRC readme ([[sources/emu42-pioneer-dump]]), Emu42's PROBLEMS.TXT
([[sources/emu42-problems]]) and its changelog for register names only
([[sources/emu42-changes]]), Garnier's "HP-42S: New Facts" from the Wayback
Machine (the live HP Museum page answers 403 to plain fetches;
[[sources/garnier-hp42s-new-facts]]), Hosoda's 42S hardware notes
([[sources/hosoda-hp42s]]), Gariepy's 28S notes
([[sources/hp28s-procnotes]]) and the 42S rows of KML 2.0
([[sources/kml20]]). Traced @ractive's revision C ROM in saturnus.
Created [[hardware/lewis]] (fixed 42S map, display RAM interleave, the seven
annunciator words, registers at #40300-#4030F, timers at #403F7-#403FF in the
48's bit layout, CRC, beeper on OUT bits 10/11) and [[hardware/hp42s]] (board
summary, key matrix, key chords, self-test steps). Opened
[[questions/hp42s-rom-crc]] (the image's self-test CRC is #1BE8, not #FFFF),
[[questions/lewis-memory-map]], [[questions/lewis-clock-and-rate]] and
[[questions/lewis-registers]].

## [2026-10-05] query | Display noise during RAM remaps (saturnus)

Traced the whole-screen noise flashes @ractive saw on the 48SX in
saturnus: renders taken while ROM J had unconfigured the RAM holding the
display bitmaps (the RAM is resized at #0C0B2-#0C0FA), so the bitmaps
decoded to the ROM. The 48GX ROM R does the same at #72386-#72D6B. Added
"Memory remaps while the display is on" and an open question on whether
the row fetches use the chip selects to [[hardware/display]].

## [2026-10-05] ingest | System flags (saturnus iteration 12)

Read the system flag appendix of the 48G User's Guide
([[sources/hp48g-ug]] App. D, PDF 467-472) and cross-checked it with the
Advanced User's Reference ([[sources/hp48g-aur]] App. C and p. 1-42 to
1-44). Created [[hardware/system-flags-48gx]] (all 64 flags in our own
words). No S/SX manual is in the library: [[hardware/system-flags-48sx]]
carries the 48G meanings over as assumed, except what
[[sources/hp48-faq]] 10.1 says changed (-14, -27, -28, -29, -54 unused on
the S/SX; -30 used; -59 unclear); opened [[questions/hp48sx-system-flags]].
Neither 49G guide has a flag list (the Advanced User's Guide defers to the
Pocket Guide, not in the library); new source page [[sources/hp49g-aug]]
(OCR pass), and [[hardware/system-flags-49g]] records the 12 flags the
guides state in passing, the rest unknown; opened
[[questions/hp49g-system-flags]]. The tables feed saturnus'
`scripts/flags-json.py`.

## [2026-10-05] ingest | RPL libraries and the decompiler (saturnus iteration 12c)

Read RPLMAN chapter 2 on commands and XLIB names ([[sources/rplman]]
p. 13) and MAKEROM ([[sources/hp48-sdk-makerom]], new source page) for
the parts of a library. The tables' layouts are not documented; observed
them in the 48SX J, 48GX R and 49G ROM images in saturnus and wrote
[[protocols/rpl-libraries]]: the 23-nibble library header, hash table
(names by length, number table), link table, the XLIB body before every
built-in command, the structure words of library #700, the unit operator
markers, the CK0-CK4 dispatchers that give argument counts, and how
programs and algebraics are laid out. Added the differences between an
ASCII transfer and the stack display to [[protocols/hp-object-format]]
and the meaning of HOME's library entries to [[hardware/hp48-system-ram]].

## [2026-10-05] ingest | HP 48SX Owner's Manual, appendix E (system flags)

The owner's manual is in `raw/manuals/` now. Read appendix E (p. E-1 to
E-7 = PDF 743-749) from the text layer, with E-1 and E-2 checked against
the page images, and rewrote [[hardware/system-flags-48sx]] from it: 57
flags described, 7 not used, none assumed from the 48G any more. New
source page [[sources/hp48sx-om]]; [[questions/hp48sx-system-flags]]
answered. Looked again for a 49G flag list: the Advanced User's Guide in
the library says on p. 2-1 that the full list is in the HP 49G Pocket
Guide, and its command reference names only -3, -103 and -105, so
[[hardware/system-flags-49g]] and [[questions/hp49g-system-flags]] stand
as they were.

## [2026-10-06] ingest | The built-in menus of the 48GX and 49G (saturnus iteration 13b)

Extended [[protocols/rpl-libraries]], "Built-in menus", from the 48SX to
the 48GX and 49G: the menus are the unnamed commands of library #A9 (169),
named by a system binary in `MENU`'s code; definitions are arrays of XLIB
names, keys the commands of library #A8; how keys lead to submenus; the
49G's soft menus need flag -117. Added "Menu" to
[[hardware/hp48-system-ram]]: where each model keeps the current menu.

## [2026-10-06] ingest | The command line in RAM (saturnus iteration 19)

New page [[hardware/command-line]], all by observation in saturnus on
the 48SX J, 48GX R and 49G 2.10 ROMs (no document gives the location;
RPLMAN mentions the edit line only as `InputLine`'s argument): the text
reversed and NUL-terminated below `local_vars`, the cursor and the
editor's nibbles (active, lowercase, insert, algebraic and program entry,
shifts, alpha lock, message shown), what every key types in alpha mode
and in program entry, the accent keys, the 48GX and 49G CHARS
applications, clearing inside EDIT, where an error's number and message
end up, and the key timing the ROM accepts (measured).

## [2026-10-07] ingest | The HP 49G Pocket Guide, system flags (saturnus iteration 12d)

@ractive photographed the booklet's "System Flags" pages (p. 76-79), now
in `raw/manuals/hp49g-pocket-guide/`. New source page
[[sources/hp49g-pocket-guide]]. Rewrote [[hardware/system-flags-49g]]
from it (stub to draft): 103 flags with set and clear meanings and
defaults, in our own words; 25 flags the booklet leaves out (gaps and
-121 to -128) recorded as not listed; no entry unreadable. Two notes
under Contradictions: the AUG gives -90 a set default, the booklet a
clear one; the booklet's -25 entry names itself where -21 is meant.
[[questions/hp49g-system-flags]] narrowed to the unlisted flags, the -90
default and the terse entries. Index and the AUG source page updated.
Also recorded the cold-start flags of ROM 2.10 as observed in saturnus
(-90 set as the AUG says; HEX, radians, -27, -95, -34 and -128 set).

## [2026-10-08] query | compiling text through the Kermit server

saturnus iteration 14 (the palette's object editor) sends edited text as a
string variable and compiles it with a C command (`STR→` on the text
wrapped in `{ }`). Recorded on [[protocols/server-commands]] under
"Compiling text through the server": the parse error comes back in the
reply, the list keeps commands from running, a no-op store keeps the
checksum, the reply's numbers follow the display mode.

## [2026-10-08] query | how STR→ splits a word holding @ or "

For the review of saturnus PR 51: compiled probe texts with `STR→` on
the 48SX, 48GX and 49G ROMs in saturnus. A mid-word `@` starts a comment
and a mid-word `"` a string, as at a word's start; a mid-word `{` opens a
list but `}` and `«` stay in the word (`Invalid Syntax`). Recorded on
[[protocols/server-commands]].

## [2026-10-09] query | Shift colours and printed labels

- [[hardware/keyboard]]: new section on which shift each printed label
  belongs to (48SX orange/blue, 48G purple/green with the green
  applications right-shifted, 49G blue/red), from the owner's manuals;
  48GX left/right shift + 7 measured on the saturnus emulator (ROM R).
  Settles the 48SX "left shift is orange, unverified" note.

## [2026-10-07] ingest | Power-on contrast per model (saturnus)

Recorded the contrast each ROM writes after a cold start, as observed in
saturnus: [[hardware/hp48sx]], [[hardware/hp48gx]], [[hardware/hp38g]],
[[hardware/hp49g]], [[hardware/hp39g-40g]], [[hardware/hp42s]].

## [2026-10-09] query | Time awake after a key (saturnus, "Key waits per model")

New section on [[hardware/keyboard]]: how long each ROM stays awake after
a shift, a digit, ENTER and a function key, and the per-model wait
saturnus uses before the next queued key.

## [2026-10-09] lint | Pre-publication review

Cross-checked every page against what saturnus and hptx now record.
Fixed contradictions inside the wiki (display offset axis on
[[hardware/io-ram]], the DEC-mode constant note on [[hardware/saturn-cpu]],
cold-start and transfer notes on [[hardware/hp38g]] and
[[hardware/hp39g-40g]], the timer expiry note on [[hardware/timers]]).
Added: per-model key waits, typing limits and shift colours
([[hardware/keyboard]]); 38G, 39G/40G, 49G and 42S rows in the default
maps and the 49G latch on SHUTDN ([[hardware/memory-controller]]);
power-on contrast per model ([[hardware/display]]); the CRC feed rule
([[hardware/crc]]); the timing calibration ([[hardware/saturn-cpu]]);
#11A bit 3 as the 39G/40G strap ([[hardware/uart]]); the server's own
packets, algebraic mode on the 49G, ON during a transaction and hptx's
observations ([[protocols/server-commands]]); the character set and two
ASCII-transfer notes ([[protocols/hp-object-format]]); IOPAR rewritten by
the server ([[protocols/iopar]]); XModem name conflicts and ALG mode
([[protocols/xmodem-hp]]). Progress notes on
[[questions/binary-odd-nibble-padding]], [[questions/contrast-range-48gx]],
[[questions/interrupt-maskability]], [[questions/hp38g-39g-transfer-protocol]]
(all still open). Answered the bit order part of
[[questions/hp48sx-system-flags]] (inferred). Replaced `raw/` mentions that
the manifest does not resolve. Updated [[index]] and [[overview]].

## [2026-10-09] lint | Page types

New types beside `hardware`, `protocol`, `source`, `question`: `model`
(the six model pages), `rom-behaviour` (system flags, command line, system
RAM, RPL libraries) and `file-format` ([[protocols/hp-object-format]]);
`emulator-note` became `emulator`. `models` items and `sources` links are
checked by pattern. Page paths are unchanged.

## [2026-10-09] lint | Schema and tag tidy

Every type now declares `tags` as a list and checks `models` and `sources`
by pattern where it allows them; `authors` on sources is a list, and
emulator and synthesis pages may list `sources`.
[[emulators/emu48]] lists the four sources it cites. Tags: `49g` folded
into `hp49g`, the all-numeric `48` into `hp48`; `memory` added to
[[questions/hp49g-bank-latch-bits]] and [[questions/lewis-memory-map]]
(suggested by a Jev pass over the 35 question pages, checked by hand).
Page paths are unchanged.

## [2026-10-09] query | Multi-flag fields as a flags list shows them

saturnus' Flags tab showed the model pages' notes about the sources ("bit
order not given in the guide") as help text, and could not name the
setting a field holds. New page [[hardware/system-flag-fields]]: per model
and field, a plain-words description where the Clear column holds notes,
and the named settings with the flags each needs, restated from the model
pages. The notes stay on [[hardware/system-flags-48sx]],
[[hardware/system-flags-48gx]] and [[hardware/system-flags-49g]], which
now link to it; saturnus' `scripts/flags-json.py` reads both.

## [2026-10-10] lint | Models in `models`, not in tags

The owner's decisions from the tidy review. Per-model tags (`48sx`,
`48gx`, `38g`, `39g`, `40g`, `42s`) and the family tags `hp48` and
`hp49g` are gone; each was first carried into the page's `models` field,
so 75 pages gained `models` (sources included, whose schema
now checks `models` by the same pattern as the other types). `hp48` became
`48sx` and `48gx` unless the page covers only one: the 48G-series manuals,
Teuwen, Voyage and the XModem pages (the 48S/SX has no XModem) got `48gx`;
the 48SX manual, Taplin, Ervin and Brittenson `48sx`. `saturn`, `lewis`
and `pioneer` stay: they name the CPU and the Pioneer series, which
`models` cannot. `28s` stays: [[sources/hp28s-procnotes]] is about the
HP 28S itself. Removed as low value: `web`, `pc`, `code`, and `lcd` next
to `display`. Five Jev suggestions applied by hand: `serial` on
[[questions/xmodem-hp-crc-mode]] and [[questions/uart-register-bit-layout]],
`memory` on [[questions/hp49g-flash-write]] and
[[questions/display-start-address-taplin]], `cpu` on
[[questions/lewis-clock-and-rate]]; the two model ones as `models`
(`48gx` on [[questions/instruction-speed-vs-hardware]], `48sx` on
[[questions/keyboard-matrix-shift-keys]]). Two pages left without a tag got
one: `history` on [[questions/hp38g-release-date]], `hardware` on
[[sources/wikipedia-hp39-40]]. `models` and `sources` stay optional on
questions; the empty decision and synthesis types stay. CLAUDE.md and the
README record a second clean-room exception: the owner's own
HPComm/HPGComm source, for protocol and file-format facts only.
