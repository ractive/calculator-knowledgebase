---
title: "Forsberg (ed.), XMODEM/YMODEM Protocol Reference (10-14-88)"
type: source
authors: ["Chuck Forsberg", "Ward Christensen", "John Byrns"]
year: 1988
raw: "raw/protocols/ymodem.txt"
status: digested
tags: [xmodem, ymodem, protocol, transfer, hptx]
---

# Forsberg (ed.), XMODEM/YMODEM Protocol Reference (10-14-88)

Omen Technology's reference, edition typeset 10-14-88 (page headers say
"June 18 1988"). Plain text; cite by section and the page number in the
running header. Contains Christensen's 1982 XMODEM overview as **section 7**
(raw/README.md says section 6; in this file it is 7) and Byrns' 1985
XMODEM/CRC description as section 8.

## Read

| Section | Pages | Content |
| --- | --- | --- |
| 2 | 4-5 | YMODEM minimum requirements |
| 4.1-4.3 | 10-12 | graceful abort (2 CAN), CRC-16 option, 1K blocks (STX) |
| 5 | 13-16 | YMODEM batch: block 0 header with name, length, date, mode |
| 7 | 20-23 | XMODEM: framing, checksum, timeouts, retries, receiver-driven flow |
| 8 | 24-28 | XMODEM/CRC: polynomial, byte order, C handshake and fallback |

Skimmed: 1, 3 (history), 5.1 (KMD/IMP quirks), 6 (YMODEM-g), 9-11.

## Reliability

The de facto standard. HP's XModem variants (128 checksum, 1K, 1K CRC on the
49G; XSERV) are covered on [[protocols/xmodem-hp]]; generic facts on
[[protocols/xmodem]].
