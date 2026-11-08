---
title: "Filer protocol: status byte or direction byte, error replies, CRC details"
type: question
status: open
models: [100lx, 200lx]
sources:
  - "[[sources/wfx-hplx]]"
  - "[[sources/j200lx-filer]]"
  - "[[sources/lxtools]]"
  - "[[sources/hplx-connectivity-pack]]"
tags: [protocol, serial, crc]
---

# Filer protocol: open points and contradictions

Collected from [[protocols/filer-hplx]] ("Contradictions" and "Open").
Most points were settled on 2026-11-08 by a static analysis of HP's PC
programs for the 100LX ([[sources/hplx-connectivity-pack]]). Those
answers were read in HP's PC build of the Filer, which holds both client
and server; for the palmtop's own server they are inferred, so a run
against a real palmtop is still needed to confirm them.

## Contradictions between the sources (answered)

- Is the reply's byte after the counter a status (j200lx: "the result of
  the operation") or a fixed direction marker 0x81 (Zampach, and every
  reply in the recording)? (src: [[sources/j200lx-filer]];
  [[sources/wfx-hplx]] Z1, Z2) **Answer:** a direction marker. The next
  byte, which every text reads as STX, is a protocol revision (2, or 1
  for older peers), and the status is a word after the header (src:
  [[sources/hplx-connectivity-pack]] C1).
- How many zero bytes start a listing reply: two (both texts) or four
  (the recording)? **Answer:** four, a status word and a result word
  (src: [[sources/hplx-connectivity-pack]] C1).
- Rename: is there a zero pair between the two names (Zampach) or not
  (j200lx)? **Answer:** a parameter word follows each name, so Zampach
  is right (src: [[sources/hplx-connectivity-pack]] C1).
- CONNECT's reply: empty (j200lx) or two zero bytes (Zampach, the
  recording)? **Answer:** two, the status word alone (src:
  [[sources/hplx-connectivity-pack]] C1).

## Formerly undocumented (answered)

- Error replies: the status word carries protocol refusals (0xFF3B to
  0xFF3E), the result word file-system codes 200 to 222: 208 file does
  not exist, 209 path does not exist, 217 file exists (create 03), 203
  disk full, 211 access denied (also a non-empty directory), 214
  write-protected. Table on [[protocols/filer-hplx]] "Errors" (src:
  [[sources/hplx-connectivity-pack]] C1, C2).
- The CRC: start value 0, so CRC-16/ARC; a doubled DLE enters it once;
  the CRC bytes are never doubled (src:
  [[sources/hplx-connectivity-pack]] C1).
- Timeouts and retries: three attempts with the same frame and counter;
  2 s for CONNECT's first reply byte, 5.9 s for others, then a
  speed-dependent window for the whole frame. A repeated counter is a
  retransmission: the server resends its last reply without running the
  command again (src: [[sources/hplx-connectivity-pack]] C1).
- Nothing must precede the first CONNECT: HP's client sends no NUL bytes,
  and the receiver hunts for SYN (src: [[sources/hplx-connectivity-pack]]
  C1).

## Still open

- Whether the palmtop's own server gives the same codes and answers as
  HP's PC Filer server, in particular for a write to the ROM drive D:
  (214 or 211, depending on its disk driver), the drive list with an
  empty card slot, and the listing filter (hidden and system files).

To settle: record the bytes on the wire (both directions, framing and CRC
included) between a PC client and a palmtop in server mode, with each
error case provoked once.
