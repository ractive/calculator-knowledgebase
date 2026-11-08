---
title: "HP 100LX/200LX: what is the highest reliable serial speed, 19200 or up to 115200?"
type: question
status: open
models: [100lx, 200lx]
sources:
  - "[[sources/palmtops-faq]]"
  - "[[sources/palmtop-paper]]"
  - "[[sources/lxtools]]"
  - "[[sources/bsd-filer]]"
tags: [serial, uart, timing]
---

# HP 100LX/200LX: highest reliable serial speed

The sources disagree ([[hardware/hp100lx-200lx]] "Speeds"):

- The FAQ: the stock internal 8250 UART is good for 19200; 115200 only
  for receiving through a buffered 16550 on a PCMCIA card; 38400 with a
  double-speed crystal (src: [[sources/palmtops-faq]]).
- Filer's Remote Settings offer up to 115,200, quoted for a link between
  two 100/200LX (src: [[sources/palmtop-paper]] P2, P3); Scardina gives
  300 bps to 115 kbps between palmtop and PC, halved by the System Manager
  for an interrupt-driven program (src: [[sources/palmtop-paper]] P1).
- Filer-protocol tools: lxbackup's default is 38400 with 19200 as the
  fallback (src: [[sources/lxtools]] L2); LX Tools 1.1b added 57600 and
  115200 and retries for errors at high speed (src: [[sources/lxtools]]
  L1); LXFS4D ran at 57600 with a 200LX by IR and by cable (src:
  [[sources/bsd-filer]] B3).

The Filer protocol has no flow control and sends up to 2048 data bytes in
one packet ([[protocols/filer-hplx]]), so a single-byte UART buffer on the
receiving side is the likely limit. The FAQ's figure may be about
interrupt-driven DOS programs under the System Manager rather than about
Filer.

To settle: copy a large file each way with Filer in server mode at 19200,
38400, 57600 and 115200, and note retries and failures per speed.
