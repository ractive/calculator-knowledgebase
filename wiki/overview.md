---
title: Overview
type: meta
---

# Overview

The wiki covers HP Saturn-based graphing calculators (HP48 S/SX/G/GX, HP49G,
HP38G, HP39G, HP40G) for two projects:

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
pages, 8 protocol pages, 1 emulator note, 23 questions (11 answered). See
[[index]] and [[log]].

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

- XModem CRC variants on the 49G and XSERV's `D` mode:
  [[questions/xmodem-hp-crc-mode]].
- Constant add/subtract in DEC mode: [[questions/dec-mode-constant-bug]].
- CE1 vs CE2 priority: [[questions/bus-priority-ce1-ce2]].
- 49G flash programming command set: [[questions/hp49g-flash-write]].
- Odd-nibble padding in binary transfer files:
  [[questions/binary-odd-nibble-padding]].

## Sources not yet used

`hp-tools-1991/` (SASM beyond the HST table, RPLMAN),
`hp48-sdk-1993/SATURN.TXT`, `hp28s-procnotes.txt`, Cannon's tips, the other HP
Journal articles, the first Voyage book (`hp48-voyage.pdf`), the ML starter
kit, the 82240B printer guide, the image-only 49G AUG, and the
x48ng, saturnng and other emulator trees. RPLMAN is the next source for
object prologs.
