---
title: "Garzotto et al., LX Tools README and lxbackup(1)"
type: source
authors: ["Andreas Garzotto", "Paul Bernard", "Peter Watkins", "Gerhard Gonter", "Edward A. Falk"]
year: 2000
raw: "https://github.com/gonter/lxtools-ng"
status: digested
tags: [filer, protocol, serial]
models: [100lx, 200lx]
---

# LX Tools README and lxbackup(1)

LX Tools (1995-2000, GPL) are Unix commands (`lxdir`, `lxcopy`, `lxdel`,
`lxmkdir`, `lxrmdir`, `lxbackup`) that act as a Filer client against the
palmtop's Filer in server mode. Read on 2026-11-08; web pages, not in
`raw/`. Only the documentation was read, not the C sources.

| Item | Document | Used for |
| --- | --- | --- |
| L1 | `lxtools/README` in <https://github.com/gonter/lxtools-ng> (versions 1.0a to 1.1e) | only Filer is needed on the palmtop; 1.1b (1999) added retransmission after errors "more apparent on high speed connections", 57600 and 115200 baud, and ignores RTS/CTS and carrier detect |
| L2 | lxbackup(1), <https://man.cx/lxbackup(1)> | Filer setup (Remote Settings: 38400, Com1, server mode enabled), default 38400, try 19200 on trouble, names upper case on the palmtop |

Facts used on [[protocols/filer-hplx]] and [[hardware/hp100lx-200lx]].
