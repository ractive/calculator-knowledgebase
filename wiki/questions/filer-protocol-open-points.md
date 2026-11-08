---
title: "Filer protocol: status byte or direction byte, error replies, CRC details"
type: question
status: open
models: [100lx, 200lx]
sources:
  - "[[sources/wfx-hplx]]"
  - "[[sources/j200lx-filer]]"
  - "[[sources/lxtools]]"
tags: [protocol, serial, crc]
---

# Filer protocol: open points and contradictions

Collected from [[protocols/filer-hplx]] ("Contradictions" and "Open").

## Contradictions between the sources

- Is the reply's byte after the counter a status (j200lx: "the result of
  the operation") or a fixed direction marker 0x81 (Zampach, and every
  reply in the recording)? (src: [[sources/j200lx-filer]];
  [[sources/wfx-hplx]] Z1, Z2)
- How many zero bytes start a listing reply: two (both texts) or four
  (the recording)?
- Rename: is there a zero pair between the two names (Zampach) or not
  (j200lx)?
- CONNECT's reply: empty (j200lx) or two zero bytes (Zampach, the
  recording)?

The recording of HP's own client and server outranks the texts where it
shows the command (src: [[sources/wfx-hplx]] Z2); rename was not recorded.

## Not documented anywhere read

- Error replies: which bytes change, and their codes, for a missing file,
  an existing name with create (03), a full disk, a non-empty directory,
  a write to the ROM drive D:.
- The CRC start value (0 would make it CRC-16/ARC); whether a doubled DLE
  enters the CRC once; whether a 0x10 inside the CRC bytes is doubled.
- Timeouts and retries on HP's side; whether a repeated counter is taken
  as a retransmission.
- Whether anything must precede the first CONNECT.

To settle: record the bytes on the wire (both directions, framing and CRC
included) between a PC client and a palmtop in server mode, with each
error case provoked once.
