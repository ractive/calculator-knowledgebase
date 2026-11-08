---
title: "HP, Connectivity Pack for the HP 100LX: the PC programs (static analysis)"
type: source
authors: ["Hewlett-Packard"]
year: 1994
raw: "not stored; the programs are on archive.org, item HP100LX-Connect (https://archive.org/details/HP100LX-Connect)"
status: digested
tags: [filer, protocol, serial, connectivity]
models: [100lx]
---

# HP, Connectivity Pack for the HP 100LX (the PC programs)

HP's PC software for the 100LX (Connectivity Pack F1021A, see
[[hardware/hp100lx-200lx]]): a DOS version of the palmtop's Filer that
works on the palmtop's files over the cable, and a redirector. Programs
dated September 1993 to January 1994. Proprietary; the programs are not
in `raw/` and no binary is kept here. Statically analysed on 2026-11-08
for interoperability facts about the Filer protocol (disassembly and
decompilation, nothing executed), under the exception in this wiki's
`CLAUDE.md`: facts only, no code, no routine names, no addresses, no
layouts copied from it.

| Item | Program | Used for |
| --- | --- | --- |
| C1 | the PC Filer | framing as built and parsed, the CRC, the client's timeouts, retries and connect, the full Filer server (status and result words, handles, counter rule, search contexts, drive list, which commands it answers), the client's command sequences and error handling, the line settings it asks for |
| C2 | the communications kernel (the resident layer that plays the palmtop's System Manager on the PC) | UART set-up (registers, FIFO, flow control), the file-system error codes and the DOS errors behind them, what a listing contains, the drive list on the PC |

The pack's redirector (server and client) speaks a different protocol
(DLE STX frames with no SYN lead-in and a byte-sum checksum); it was only
classified, not analysed.

## Reliability

- HP's own code, so for what the PC programs do it outranks every
  third-party text. Pages mark each fact "read" (seen directly in the
  code) or "inferred" (from usage or consistency, not traced end to end).
- The PC Filer carries both client and server, and its make-up (for
  example an empty stub for powering the 95LX's serial port) shows it is
  the palmtop Filer's code built for DOS. That the palmtop's own Filer
  behaves the same is **inferred**, not confirmed on a palmtop: the
  palmtop runs its own System Manager, so its file-system error codes,
  drive list and listing filter may differ in detail.
- How the Filer's service calls map to the kernel's routines was matched
  by behaviour, not traced.

Facts used on [[protocols/filer-hplx]] and [[hardware/hp100lx-200lx]];
questions settled on [[questions/filer-protocol-open-points]] and
[[questions/hplx-serial-speed]].
