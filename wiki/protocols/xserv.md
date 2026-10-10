---
title: "XSERV: the HP 49G (and XSrvr48) XModem server protocol"
type: protocol
status: draft
sources: ["[[sources/hp49-rom118-notes]]", "[[sources/conn4x-ymodem-pas]]", "[[sources/conn4x-help]]", "[[sources/xsrvr48-readme]]"]
tags: [xserv, xmodem, server, hptx]
models: [48sx, 48gx, 49g]
---

# XSERV

A command server layered on XModem. Built into the 49G (ROM after 1.10);
provided on the 48 by the XSrvr48 library (src: [[sources/hp49-rom118-notes]];
[[sources/xsrvr48-readme]]). There is no prose specification: everything
below on framing comes from the HP-written client code in
[[sources/conn4x-ymodem-pas]] and is unverified against a calculator.

## Starting

- 49G: right-shift, pause, right-arrow; pressed quickly together the same keys
  start the Kermit server. Also XSERV from the catalog (src:
  [[sources/conn4x-help]] ConnectivityHP49Ops).
- 48: XSERV from the XSRVR library menu (ConnectivityHP48Ops).
- The 49G ROM notes also give XGET and XPUT (client side, name argument) and
  ROMUPLOAD (src: [[sources/hp49-rom118-notes]]).

## Commands

The host sends a single ASCII command byte after making sure the line is
quiet [command sender]. Official letters (src: [[sources/hp49-rom118-notes]]):

| Byte | Meaning | Follow-up (from the client code) |
| --- | --- | --- |
| `P` | put a file into the calculator | command packet with the variable name, then an XModem send from the host [put command] |
| `G` | get a file from the calculator | command packet with the name, then an XModem receive by the host [get command] |
| `E` | execute a command line | command packet with RPL text, e.g. `HOME dir1 dir2` to change directory, `'name' IFERR PURGE THEN PGDIR END` to delete [command execution, directory change, file deletion] |
| `M` | memory | reply packet [memory query] |
| `L` | list the current directory | reply packet with one record per variable [directory listing] |

Seen only in Conn4x (not in HP's notes): `V` returns a version string as a
reply packet [connection setup]; `Q` quits the server, "added" by Graves [server quit]; `o`
with a name packet [file execution], meaning unknown.

## Packet framing (client code)

- Command packet, host to calculator: 2-byte length, high byte first; the
  bytes; 1-byte checksum = sum of the bytes mod 256. The calculator answers
  ACK; the host retries up to 5 times, clearing the line between tries
  [command packet sender].
- Reply packet, calculator to host: same framing; the host answers ACK, or
  NAK to get a resend, giving up after 4 failures [reply packet receiver].
- Directory record in an `L` reply: 1 byte name length, the name, 2 bytes of
  the prolog's low 16 bits (low byte first), 3 bytes size in nibbles (low
  byte first), 2 bytes CRC [directory listing]. Prolog values the client
  tests: #2A96 directory, #2A2C string, #2DCC and #2D9D shown with a program icon.
- Detecting the model: the client runs `VERSION DROP '$$$t' STO` via `E`, then
  `G`s `$$$t`; a 48 file starts `HPHP48-x` (x = ROM letter), a 49 reply
  contains "Revision #...@" [connection setup].

## Interplay with XModem

See [[protocols/xmodem-hp]] for block sizes, the start characters (NAK, `C`,
`D`) and the 49G padding limit.

## Open

- Exact reply formats of `M` and `V`.
- What the calculator does on an unknown command byte.
- Whether XSrvr48 implements exactly the same set.
