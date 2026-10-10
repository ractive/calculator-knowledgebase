---
title: "de Brébisson and Graves, Conn4x YModem.pas (XModem client and XSERV client code)"
type: source
authors: ["Cyrille de Brébisson", "William G. Graves"]
year: 2002
raw: "raw/protocols/xserv-conn4x/conn4xsource/YModem.pas"
status: digested
tags: [xserv, xmodem, hptx]
models: [49g]
---

# de Brébisson and Graves, Conn4x YModem.pas (XModem client and XSERV client code)

Delphi unit from Conn4x 2.0: XModem send/receive threads and the client side
of the XSERV command protocol. Started by Cyrille de Brébisson (HP) on
2000-07-11, changes by Graves 2002. **License: no commercial use without
permission; read for facts only, nothing copied.** No prose specification of
XSERV exists; this code plus the ROM release notes are the sources. Cite by a
plain description of the part of the code (e.g. "command packet sender",
"YModem sender"), not by routine name or line.

Also glanced at `FixObj.pas` (its object-fixing step) for how a host trims an HPHP file
to the object length.

## Facts extracted

Recorded on [[protocols/xserv]], [[protocols/xmodem-hp]],
[[protocols/hp-object-format]]: command letters V, E, G, P, L, M, Q, o; the
length/checksum framing of command and reply packets; the directory record
layout; the three receiver start characters NAK, C and D; 128/1024-byte block
use and the 49G padding limit; retry and timeout values.

## Reliability

Working client code by an HP engineer, but with visible defects: the
"CRC16" routine is a reflected table labelled by Graves as "obviously wrong"
and "not detected in use with HP49", and the CRC byte order differs between
the send and receive paths. The HP-CRC routine lives in a unit
that is not in raw/. Treat byte-order details as unverified.
