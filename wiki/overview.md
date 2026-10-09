---
title: Overview
type: meta
---

# Overview

The wiki covers HP Saturn-based graphing calculators (HP48 S/SX/G/GX, HP49G,
HP38G, HP39G, HP40G) and the HP 42S (Lewis chip, a Saturn core) for two
projects:

- **hptx** (<https://github.com/ractive/hptx>): Rust file transfer over
  serial, Kermit and XModem, with a sans-IO protocol core, a CLI and later a
  Tauri app. The saturnng Docker container in `hptx/emulator/` is the current
  test target.
- **saturnus** (<https://github.com/ractive/saturnus>): a from-scratch
  Saturn emulator in Rust, headless core with pluggable UIs, written from
  documentation and ROM behaviour only (see the clean-room rule in `CLAUDE.md`).

Both repos keep their own plans in `kb/` (hyalo knowledge bases) and point
back here for calculator facts. GPL emulator sources are not part of this
repository and are read for facts only.

State as of the first ingest pass (2026-10-04): 36 source pages, 15 hardware
pages, 8 protocol pages, 1 emulator note, 23 questions (11 answered). State
on 2026-10-09: 59 source pages, 22 hardware pages, 9 protocol pages, 1
emulator note, 35 questions (16 answered). See [[index]] and [[log]].

## Hardware: what is solid

- **CPU** ([[hardware/saturn-cpu]]): register set, fields, nibble order,
  RSTK behaviour, HST bit order (settled from HP's SASM manual), the
  instruction encoding scheme and the quirks an accurate core needs (even-
  address IN, constant add/subtract field overrun, hex-only pointer and P
  arithmetic, SB on shifts and rotates). The full opcode table is in
  [[sources/saturn-tutorial]] and HP's SASM manual; it is cited, not copied.
- **Memory controller** ([[hardware/memory-controller]]): six controllers,
  daisy-chain configuration, size-as-mask semantics, priority, C=ID codes,
  default maps for every model, the 48GX bank latch (including its
  off-by-one read quirk) and the 49G flash banking. Mostly from Giesselink
  (tutorial ch. 66-67), cross-checked against Voyage and Emu48.
- **I/O RAM** ([[hardware/io-ram]]): every nibble of #100-#13F has a name and
  a source; the UART ([[hardware/uart]]), timers ([[hardware/timers]]),
  display ([[hardware/display]]), keyboard ([[hardware/keyboard]]),
  interrupts ([[hardware/interrupts]]) and CRC ([[hardware/crc]]) pages hold
  the bit-level detail.
- **ROM expectations**: Duchesne's reading of the interrupt handler lists
  what the ROM checks on every interrupt (TIMER2 running, RAM check word,
  configuration on the 48S, battery, card switches), which defines the
  minimum an emulator must model to avoid warm starts.
- **Models**: [[hardware/hp48sx]], [[hardware/hp48gx]], [[hardware/hp49g]],
  [[hardware/hp38g]] and [[hardware/hp39g-40g]] are drafts. The 38G is a
  48G with its 32 KB RAM at #F0000; the 39G/40G are a 49G cut to 1 MB ROM
  and 256 KB RAM, one ROM for both; controller wiring for both is open.
- **Emulator evidence**: [[emulators/emu48]] records the behaviour Emu48's
  change log encodes; it settled DA19 polarity, TIMER2 expiry and the LPB
  bit.
- **Settled by running the ROMs in saturnus** (no oracle unless stated):
  the 49G bank latch (Sousa's bit order boots,
  [[questions/hp49g-bank-latch-bits]]); the 39G/40G model strap (#11A
  bit 3, [[questions/hp39g-40g-model-detection]]); the 38G and 39G RAM
  wiring as the ROMs configure it ([[hardware/memory-controller]]); and
  pages on system RAM ([[hardware/hp48-system-ram]]), the command line
  ([[hardware/command-line]]), the system flags of each model, RPL
  libraries and menus ([[protocols/rpl-libraries]]) and the 42S and its
  Lewis chip ([[hardware/hp42s]], [[hardware/lewis]]).

## Protocols: what hptx needs

- [[protocols/kermit]] (generic, from the 6th-edition manual) and
  [[protocols/kermit-hp]]: HP uses 9600 8N1, no flow control, no long
  packets or windows, block check 3 by default, requires every control
  character prefixed, and receives binary files as strings until it sees
  `HPHP48-x`.
- [[protocols/server-commands]]: the HP server answers only R, S, C (runs
  the text, returns the stack), G D, G F, G L, plus I.
- [[protocols/iopar]]: all six fields with defaults; wire/IR and ASCII/binary
  are flags -33 and -35.
- [[protocols/xmodem]], [[protocols/xmodem-hp]], [[protocols/xserv]]: the
  48G XModem is checksum-only; the 49G adds 1K and CRC and the XSERV command
  server (P, G, E, M, L), whose framing is known only from HP-written client
  code.
- [[protocols/hp-object-format]]: `HPHP48-x` / `HPHP49-x` header, nibble
  packing, GROB layout, the `%%HP:` ASCII header.
- Line-level rules from HP's I/O guide on [[hardware/uart]]: 11.375 bit
  times per byte sent, 255-byte receive buffer, avoid inter-byte gaps of
  4 frames to 4 frames + 5 ms.

## Biggest open points

- The 38G/39G/40G directory file and aplet format:
  [[questions/hp38g-39g-transfer-protocol]].
- Why the real machines are slower than the cycle counts:
  [[questions/instruction-speed-vs-hardware]].
- Whether the 42S ROM image passes its own CRC test:
  [[questions/hp42s-rom-crc]].
- Constant add/subtract in DEC mode: [[questions/dec-mode-constant-bug]].
- CE1 vs CE2 priority: [[questions/bus-priority-ce1-ce2]].
- 49G flash programming command set: [[questions/hp49g-flash-write]].
- Odd-nibble padding in binary transfer files:
  [[questions/binary-odd-nibble-padding]].

## Sources not yet used

Now used: RPLMAN, the SASM manual beyond the HST table (2.7, 8), the HP 28S
processor notes, MAKEROM, the three HP Journal issues, the 49G Advanced
User's Guide (skimmed), and saturnng as a black box. Not yet used: Cannon's
tips, the first Voyage book, the ML starter kit, the 82240B printer guide,
SATURN.TXT from the 1993 SDK, and x48ng.
