---
title: "XSERV: the HP 49G (and XSrvr48) XModem server protocol"
type: protocol
status: draft
sources: ["[[sources/hp49-rom118-notes]]", "[[sources/conn4x-ymodem-pas]]", "[[sources/conn4x-help]]", "[[sources/xsrvr48-readme]]"]
tags: [xserv, xmodem, server, satx]
models: [48sx, 48gx, 49g]
---

# XSERV

A command server layered on XModem. Built into the 49G (ROM after 1.10);
provided on the 48 by the XSrvr48 library (src: [[sources/hp49-rom118-notes]];
[[sources/xsrvr48-readme]]). There is no prose specification: everything
below on framing comes from the HP-written client code in
[[sources/conn4x-ymodem-pas]]. A host using that framing worked against
a real 49G with ROM 1.18 on 2026-11-08 (section below).

## Starting

- 49G: right-shift, pause, right-arrow; pressed quickly together the same keys
  start the Kermit server. Also XSERV from the catalog (src:
  [[sources/conn4x-help]] ConnectivityHP49Ops).
- 48: XSERV from the XSRVR library menu (ConnectivityHP48Ops).
- On the owner's real 49G (ROM 1.18, 2026-11-08) the right-shift ▶
  shortcut started XSERV twice and then the Kermit server twice, so which
  server it starts was unpredictable there; see
  [[hardware/hp49g-keyboard]]. The press timing was not measured, so this
  fits the timing rule above (a pause gives XSERV, quick presses Kermit).
- ROM 2.15 has XSERV: typed on the saturnus emulator with that ROM, it
  showed "Xmodem server" and answered `M` (observation on the emulator,
  2026-11-08).
- Not from the Kermit server: `XSERV` sent as a host command fails with
  "Port Not Available" ([[protocols/server-commands]]).
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

## Observed on a real 49G (2026-11-08)

The owner's HP 49G, ROM 1.18 ("Version HP49-C Revision #1.18"), from a PC
over an FTDI USB serial adapter at 9600 baud (observations on real
hardware by the owner and Claude, 2026-11-08):

- XSERV answered every command tried. `M`: a reply packet holding
  `245478.`, a real with the 49G's trailing point (the release notes call
  `M` "get the calculator memory", src: [[sources/hp49-rom118-notes]]).
  `L`: 13 variables with sizes, types and CRCs, in the record layout
  above. `G` and `P`: XModem in HP's CRC mode (the `D` start,
  [[protocols/xmodem-hp]]), with byte-identical round trips of a 47-byte
  and a 4013-byte object. `E`: RPL executed, e.g.
  `{ SATXXM SATXXN } PURGE`.
- Once, an `L` sent right after an `E` got no complete reply; with a 2 s
  pause between the two it worked.
- The sizes in an `L` reply are smaller than those of the Kermit server's
  directory listing ([[protocols/server-commands]]): IOPAR 20 bytes in
  XSERV against 29.5 in Kermit, X 10.5 against 16. The XSERV record
  gives the object's nibbles; the differences (9.5 and 5.5 bytes) fit
  Kermit counting the name's directory entry as well, 4.5 bytes plus one
  per name character (inferred, not checked on more names).
- Speed, the same 4013-byte string fetched by each server, connection
  setup included, 9600 baud: Kermit server 16.7 s, XSERV 8.4 s.
- `SERVER` sent through `E` starts the Kermit server inside XSERV's `E`:
  a Kermit FINISH then returns to XSERV, which answers again. Done twice
  in a row; the stack was unchanged afterwards.

## Observed on the emulated 49G, ROM 2.15 (2026-11-08)

On the saturnus emulator with ROM 2.15 (typed `XSERV`), a PC on its serial
port (observations on the emulator, 2026-11-08):

- `M` replies with the free memory as text (`245811.`); `V` replies
  `XModem Version 1.0`.
- `Q` ends XSERV; the calculator is back at its stack, with whatever an
  `E` left there.
- `E` is acknowledged before its RPL runs. Command bytes sent while the RPL
  still runs are dropped, not queued, which fits the unanswered `L` above.
  An RPL error inside `E` shows nothing on the link and XSERV goes on.
- `G` of a variable that does not exist leaves XSERV on an error box until
  a key is pressed.
- `G` sends the object in binary whatever flag -35 says. `P` of a file
  that is not an HP binary object stores a string that keeps the XModem
  padding.
- The `L` size plus 4.5 plus the name's length equals the Kermit server's
  size, on 7 variables; this confirms the inference above. The CRC in an
  `L` record is low byte first and equals the Kermit listing's.
- A Kermit server started through `E` (`SERVER`) does not pack the stack
  at FINISH, also in algebraic mode; XSERV answers again about 1 s after
  FINISH.
- XSERV ignores a Kermit Send-Init (`I` packet) sent in any of the three
  parities, and the Kermit servers of ROM 2.15, the 48GX's ROM R and the
  48SX's ROM J ignore a lone `M` byte.

## Interplay with XModem

See [[protocols/xmodem-hp]] for block sizes, the start characters (NAK, `C`,
`D`) and the 49G padding limit.

## Open

- Whether `M`'s number is free memory in bytes.
- What the calculator does on an unknown command byte.
- Whether ROM 1.18 knows `Q`.
- Whether XSrvr48 implements exactly the same set.
