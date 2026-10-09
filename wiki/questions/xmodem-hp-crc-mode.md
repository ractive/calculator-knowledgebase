---
title: "Which XModem start character and CRC do HP calculators use?"
type: question
status: answered
tags: [xmodem, hp49g, hp48, hptx]
---

# Which XModem start character and CRC do HP calculators use?

The Conn4x sender accepts three start characters: NAK (checksum), `C`
(CRC-16) and `D` (an "HP CRC", 2 bytes, routine not available) (src:
[[sources/conn4x-ymodem-pas]] YModem sender). Its "CRC16" routine is a
reflected table that Graves calls "obviously wrong" and "not detected in use
with HP49", and the CRC byte order differs between its send and receive
paths. The 49G supports "1K CRC" per the FAQ (src: [[sources/hp48-faq]]
2.14).

To settle by experiment against the saturnng/Emu48 49G and a 48GX:

- what the calculator sends as receiver (NAK, C or D);
- what it accepts as sender;
- the `D`-mode CRC algorithm (probably the Saturn/Kermit LSB-first CRC,
  unverified) and its byte order.

## Progress

48G series: no CRC at all; it uses the checksum variant and a CRC-capable host
must fall back (src: [[sources/hp48g-ug]] 27-14). Still open for the 49G (which
advertises "1K CRC") and for XSERV's `D` mode.

## Answer (saturnng, 2026-10-05)

Measured on the saturnng emulator by hptx, 2026-10-05 (HP 49G ROM 2.15, HP
48GX ROM R); not yet confirmed on hardware. Evidence: hptx
`crates/xmodem-proto/traces/49g-xrecv.trace` and the other traces there.

- 49G as receiver: sends `D` about every 3 s (4 seen), then falls back to
  NAK every 10 s about 10 times, then gives up with "XRECV Error: Receive
  Error" after roughly 108 s.
- `D` mode is CRC-16/KERMIT: the Saturn CRC, polynomial #1021 reflected, as
  in the Kermit type-3 check, sent high byte first. Recomputing it over the
  data of the 49G trace blocks reproduces the transmitted trailers.
- The 49G never answers the standard `C` start character, so CRC-16/XMODEM
  is unused.
- 48GX: checksum only. XRECV opens with NAK and XSEND ignores both `C` and
  `D`, which agrees with [[sources/hp48g-ug]] 27-14.

Details and block-size findings on [[protocols/xmodem-hp]]. Still open:
whether XSERV uses the same `D` CRC (likely, unverified), and hardware
confirmation.
