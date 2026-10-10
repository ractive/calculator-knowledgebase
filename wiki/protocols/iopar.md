---
title: "IOPAR: the HP48 I/O parameter list"
type: protocol
status: draft
sources: ["[[sources/hp48g-aur]]", "[[sources/hp48g-ug]]", "[[sources/hp48-kermit-hints]]", "[[sources/hp48-faq]]", "[[sources/io-guide]]", "[[sources/conn4x-help]]"]
tags: [iopar, kermit, settings, satx]
models: [48sx, 48gx]
---

# IOPAR

A list in the reserved variable IOPAR in HOME. It is created the first time
the user transfers data or opens the port (OPENIO) and is updated whenever
the I/O settings change; all entries are integers (src: [[sources/hp48g-aur]]
D-5). Deleting it restores the defaults (src: [[sources/hp48-kermit-hints]],
Communications settings).

The Kermit server writes IOPAR in HOME when it ends: purged during a
session, it is back after `G F` (saturnus iteration 12b, 2026-10-08).
Storing IOPAR back with the same values grows the 48SX variable from 29.5
to 37.5 bytes (satx, 2026-10-05).

## Fields (48G series)

| # | Field (command) | Values | Default |
| --- | --- | --- | --- |
| 1 | baud (BAUD) | 1200, 2400, 4800, 9600 | 9600 |
| 2 | parity (PARITY) | 0 none, 1 odd, 2 even, 3 mark, 4 space; negative = use on transmit only, no receive check | 0 |
| 3 | receive pacing | non-zero: send XOFF when the receive buffer is nearly full, XON when it can take more; not used by Kermit | 0 |
| 4 | transmit pacing | non-zero: stop on XOFF, resume on XON; not used by Kermit | 0 |
| 5 | checksum (CKSM) | Kermit block check requested when the HP initiates SEND: 1, 2, 3 | 3 |
| 6 | translation code (TRANSIO) | 0 none; 1 LF to/from CR LF; 2 also characters 128-159; 3 also 128-255 | 1 |

(src: [[sources/hp48g-aur]] D-5, D-6; the same values in
[[sources/hp48g-ug]] 27-15 and [[sources/hp48-kermit-hints]]). Earlier
models may have fewer fields; translation "might be new to G series" (src:
[[sources/hp48-kermit-hints]]).

## Settings that are not in IOPAR

They are system flags (src: [[sources/hp48g-ug]] App. D, p. D-3, D-4):

| Flag | Clear | Set |
| --- | --- | --- |
| -33 | I/O to the serial port (wire) | I/O to IR |
| -34 | printer output to the IR printer | printer output to the serial port, if -33 is clear |
| -35 | objects transmitted in ASCII | objects transmitted in binary (memory image) |
| -36 | received names that clash get a numeric extension | received objects overwrite existing variables |
| -39 | I/O messages shown | I/O messages suppressed |

The G series TRANSFER form shows PORT (wire/IR), TYPE (Kermit/XModem), FMT
(ASC/BIN), XLAT, CHK, BAUD, PARITY and OVRW together; FMT, XLAT, CHK and
PARITY apply to Kermit only (src: [[sources/hp48g-ug]] 27-8, 27-9).

## Notes

- XSERV/XModem only care about baud, parity and pacing: `{ 9600 0 0 0 x x }`
  (src: [[sources/conn4x-help]] ConnectivityCom).
- The serial hardware itself supports all five parities with receive checking
  optionally off; checking is done in software (src: [[sources/io-guide]] 2,
  2.4.1).
- Self-test output ignores IOPAR (src: [[sources/hp48-faq]] 4.7).
- 49G: the entries must be reals. A host-stored `{ 9600 0 0 0 3 1 }` of
  exact integers is accepted by STO, but the server then stops with
  "Invalid IOPAR" and answers nothing; `{ 9600. 0. 0. 0. 3. 1. }` works, and
  the 48GX reads that list as well. Changing the checksum field with valid
  reals does not disturb a running server session; it applies at the next
  SERVER. (Measured on the saturnng emulator by satx, 2026-10-05, HP 49G ROM
  2.15 and HP 48GX ROM R; not yet confirmed on hardware.) The AUR's "all
  entries are integers" above means integer-valued reals: the 48 has no exact
  integers, and the 49G rejects its exact integers in IOPAR.
