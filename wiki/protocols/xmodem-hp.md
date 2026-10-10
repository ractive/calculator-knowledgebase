---
title: "XModem on HP calculators (48G XSEND/XRECV, 49G, XSERV transfers)"
type: protocol
status: draft
sources: ["[[sources/hp48g-ug]]", "[[sources/hp48-faq]]", "[[sources/conn4x-ymodem-pas]]", "[[sources/conn4x-help]]", "[[sources/hp49-rom118-notes]]", "[[sources/serial49-sansonovski]]"]
tags: [xmodem, transfer, satx]
models: [48gx, 49g]
---

# XModem on HP calculators

Generic protocol: [[protocols/xmodem]]. Server command layer:
[[protocols/xserv]].

## Which models

- 48G/GX: XSEND and XRECV, XModem, binary mode only (src:
  [[sources/hp48-faq]] 10.1). The
  48S/SX have no XModem.
- "The XMODEM protocol built into the HP 48 doesn't perform any CRC
  checking"; with a CRC-capable host the transfer works after the host gives
  up on CRC and falls back to checksum (src: [[sources/hp48g-ug]] 27-14). So
  the 48G receiver opens with NAK and the 48G sender ignores `C`.
- 48G: send with TYPE XModem and SEND (host receives first), receive with
  RECV then start the host's send; OVRW controls name clashes (src:
  [[sources/hp48g-ug]] 27-14).
- 48G before ROM R: XRECV fails or loses memory unless about twice the file
  size is free; FXRECV on Goodies Disk 9 fixes it, and must not be used on
  ROM R (src: [[sources/hp48-faq]] 3.5, 6.14).
- 49G: XModem with 128-byte blocks and checksum, 1K blocks, and 1K with CRC
  (src: [[sources/hp48-faq]] 2.14). XModem is noticeably faster than Kermit
  on the 49G (src: [[sources/serial49-sansonovski]] p. 3).
- Start order: for XModem start the sender first, then the receiver, with a
  short gap (src: [[sources/hp48-faq]] 6.15).

## Observed in the Conn4x client (facts only)

From [[sources/conn4x-ymodem-pas]] (the part of the code in brackets):

- Receiver start characters understood by the PC's sender: NAK (checksum),
  `C` (CRC-16) and `D` (an HP-specific CRC, 2 bytes, computed by a routine not
  in the source) [YModem sender]. Which one each calculator sends
  when receiving is not visible in this code:
  [[questions/xmodem-hp-crc-mode]].
- When receiving from a calculator, the PC opens with NAK, i.e. checksum
  mode, and accepts SOH and STX blocks [YModem receiver]. After two bad
  blocks in CRC mode it drops back to checksum.
- Retry limits: 10 NAKs per block, 10 EOTs, 5 receive errors; aborts with 3-4
  CAN [YModem sender and receiver].
- Files sent to the calculator start with `HPHP49-` for a 49G and `HPHP48-`
  for a 48; Conn4x warns on a mismatch [header check].
- **49G padding limit**: if the final block padding exceeds about 255 bytes
  the 49G does not convert the received string into an object. Conn4x
  therefore uses 128-byte blocks for small files (under 7 x 128 bytes) and
  for the tail of large ones [comment in the YModem sender; put command]. The comment
  also gives the 49G file layout: `HPHP49-R`, prolog, length, data, padding.
- Manual transfers to a 48 (XRECV of the server library) use 128-byte blocks
  [DownLd.pas].
- Timeouts: 2 s for data, 1 s for command acknowledgements; "clear line"
  means reading until 200 ms of silence [receive timeout and line clearing].

## Measured on the saturnng emulator (satx, 2026-10-05)

Measured on the saturnng emulator by satx, 2026-10-05, against HP 49G ROM
2.15 and HP 48GX ROM R; not yet confirmed on hardware. Byte-level evidence
is in the saturnus repository under `crates/xmodem-proto/traces/` (trace names
below), not a document.

Starting a transfer:

- XRECV and XSEND cannot be started through the Kermit server: a host
  command `'NAME' XRECV` answers `Error: Port Not Available` on both models.
  Start them from the keyboard after Kermit FINISH. Details on
  [[protocols/server-commands]] (`49g-server-xrecv.trace`,
  `48gx-server-xsend.trace`).
- After a keyboard-started XRECV or XSEND the stack is empty and the
  calculator is no longer in server mode.
- The 49G boots in ALG mode, where `XRECV` typed without an argument gives
  "Invalid Syntax"; `-95 CF` switches to RPN.

49G:

- XRECV start characters: `D` about every 3 s (4 seen), then NAK every 10 s
  about 10 times, then "XRECV Error: Receive Error" after roughly 108 s
  (`49g-xrecv.trace`).
- `D` mode is CRC-16/KERMIT (the Saturn CRC, polynomial #1021 reflected, as
  in the Kermit type-3 check), sent high byte first. The 49G never answers
  the standard `C` start character, so CRC-16/XMODEM is unused. See
  [[questions/xmodem-hp-crc-mode]].
- Accepts 1k (STX) and 128-byte (SOH) blocks in both directions
  (`49g-xrecv-1k.trace`, `49g-xsend-1k.trace`). XSEND of an 1824-byte object
  sends one 1k block and then seven 128-byte blocks, the same short-tail
  split Conn4x uses.
- XSEND pads the last block with memory garbage, not a fixed byte; only a
  walk of the object's length can strip it. XRECV strips the SUB (0x1A)
  padding itself when it stores a string.
- XRECV does not overwrite an existing variable: with SATXX present it stored
  the object as SATXX.1.
- `-95 CF` over Kermit, FINISH, XRECV or XSEND from the keyboard, `SERVER`,
  then `-95 SF` brings back ALG mode (satx).

48GX:

- Checksum only. XRECV starts with NAK; as a sender XSEND ignores `C` and
  `D` (`48gx-xrecv.trace`, `48gx-xsend-hpcrc.trace`).
- XRECV NAKs every STX (1k) block and sends CAN CAN CAN after 9 attempts, so
  1k blocks must not be used with the 48G series (`48gx-xrecv-1k.trace`).
- XSEND pads the last block with 0x00 (`48gx-xsend.trace`).
- A cancelled XRECV leaves an empty string in the target variable.
- XRECV onto an existing name stops with "XRECV Error: Name Conflict": no
  start character is sent, the name stays on the stack, and there is no
  `.1` fallback; flag -36 was not tried (satx, saturnng 48GX ROM R,
  2026-10-05).

## Settings

IOPAR `{ 9600 0 0 0 x x }`: 9600 baud, no parity, no XON/XOFF either way;
checksum and translation fields are irrelevant to XModem (src:
[[sources/conn4x-help]] ConnectivityCom). The 49G's Transfer screen resets
Type from XModem back to Kermit when left (ConnectivityHP49Ops). A host that
stores IOPAR on a 49G must use reals (`{ 9600. 0. 0. 0. 3. 1. }`); see
[[protocols/iopar]].

## Open

- Confirm the saturnng measurements above on hardware.
- Answered on saturnng: [[questions/xmodem-hp-crc-mode]] (49G `D` mode is
  CRC-16/KERMIT, high byte first; 48GX checksum only), and the 48G's XRECV
  rejects 1K blocks.
