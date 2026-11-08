---
title: "The HP 100LX/200LX Filer protocol (remote Filer, server mode)"
type: protocol
status: draft
sources:
  - "[[sources/wfx-hplx]]"
  - "[[sources/j200lx-filer]]"
  - "[[sources/bsd-filer]]"
  - "[[sources/lxtools]]"
  - "[[sources/palmtop-paper]]"
  - "[[sources/hplx-connectivity-pack]]"
tags: [filer, protocol, serial, server, crc]
models: [100lx, 200lx]
---

# The Filer protocol

The link that the palmtops' built-in Filer uses to work on the files of
another machine, over the cable or IR ([[hardware/hp100lx-200lx]]). HP
never published it. The facts here come from third-party descriptions, a
recorded session between two 200LX machines, and a static analysis of
HP's PC programs for the 100LX. Z1 to Z3 are the items of
[[sources/wfx-hplx]], B1 to B3 those of [[sources/bsd-filer]], C1 (the PC
Filer) and C2 (the communications kernel) those of
[[sources/hplx-connectivity-pack]].

**How sure the C1/C2 facts are.** They were read in HP's code unless
marked "inferred". They describe HP's PC build of the Filer, which holds
both a client and a full server and is the palmtop Filer's code built for
DOS. For the palmtop's own server they are inferred, not confirmed on a
palmtop (src: [[sources/hplx-connectivity-pack]] reliability).

## Roles

- Two machines run Filer; the one that receives the connect request
  becomes the server, the other (the client) drives the session and sees
  the server's disks as if they were its own. The server can do nothing
  while serving except end server mode (src: [[sources/bsd-filer]] B1).
- On the palmtop the server role needs Filer's server mode enabled
  ([[hardware/hp100lx-200lx]] "Filer server mode"). A PC acts as client
  (LX Tools, WFX-HPLX, HP's Connectivity Pack) or as server (HP's desktop
  Filer, BSD Filer) (src: [[sources/lxtools]] L1; [[sources/wfx-hplx]] Z3;
  [[sources/palmtop-paper]] P4; [[sources/bsd-filer]] B1).
- With server mode off, the Filer does not look for a connect at all:
  frames are ignored and nothing is sent back (src:
  [[sources/hplx-connectivity-pack]] C1; inferred for the palmtop).
- Strict request and reply: each packet the client sends is answered by
  exactly one packet from the server (src: [[sources/wfx-hplx]] Z1;
  [[sources/j200lx-filer]]). Every exchange in the recorded session is one
  request followed by one reply (src: [[sources/wfx-hplx]] Z2). The
  exceptions are command 02, which gets no reply, and DISCONNECT, whose
  reply HP's client does not wait for (see below).

## Physical layer

- No flow control at all, neither RTS/CTS nor XON/XOFF; three wires (TX to
  RX both ways, ground) are enough (src: [[sources/wfx-hplx]] Z1, Z3
  history 1.01; [[sources/lxtools]] L1 ignores CTS/RTS and carrier
  detect). HP's PC Filer asks for no flow control either; its driver keeps
  DTR and RTS on and ignores CTS, DSR and DCD (src:
  [[sources/hplx-connectivity-pack]] C1, C2).
- Line settings: HP's PC Filer sets 8 data bits, no parity, 1 stop bit
  (src: [[sources/hplx-connectivity-pack]] C1). Remote Settings offer no
  parity or word-length choice (src: [[sources/palmtop-paper]] P4), so
  the palmtop's side is 8N1 too (inference: the two ends must match).
- Speeds: HP's PC Filer offers 1200, 2400, 4800, 9600, 19200, 38400,
  57600 and 115200 (src: [[sources/hplx-connectivity-pack]] C1); others
  report 2400 to 115200 (src: [[sources/wfx-hplx]] Z1). There is no speed
  negotiation: both sides must be set to the same speed beforehand (src:
  [[sources/hplx-connectivity-pack]] C1). Speeds in practice:
  [[questions/hplx-serial-speed]].

## Packet framing

Both directions use the same frame, in the style of IBM's binary
synchronous (BSC) links; BSD Filer's author lists two BSC texts as his
references (src: [[sources/bsd-filer]] B1). In order (src:
[[sources/wfx-hplx]] Z1; [[sources/j200lx-filer]]; the header order as
seen in Z2; all of it confirmed in [[sources/hplx-connectivity-pack]] C1):

1. Lead-in, not covered by the CRC: three SYN (0x16), then DLE STX (0x10
   0x02). HP's client sends nothing before the SYNs: no NUL bytes, no
   other preamble (src: [[sources/hplx-connectivity-pack]] C1).
2. Header: SOH (0x01); the command code; the packet counter; a direction
   byte, 0x01 in requests and 0x81 in replies; a **protocol revision**
   byte, 2 normally and 1 for older peers (src:
   [[sources/hplx-connectivity-pack]] C1). The published texts call this
   byte STX (0x02), which is its value in a revision-2 session.
3. In replies, a status word (see "Data conventions"). Then the command's
   data, if any. Numbers are little-endian.
4. DLE ETX (0x10 0x03).
5. The CRC, two bytes, low byte first, sent as they are.

- **DLE doubling.** In the frame body (from SOH to the last data byte,
  header included) every 0x10 byte is sent twice (src:
  [[sources/wfx-hplx]] Z1), so the command code 0x10 (change directory)
  and a counter value of 0x10 are doubled too. A doubled DLE enters the
  CRC once, and **the two CRC bytes are never doubled**, even when one of
  them is 0x10 (src: [[sources/hplx-connectivity-pack]] C1).
- **CRC.** CRC-16/ARC: reflected polynomial 0xA001 (x^16 + x^15 + x^2 +
  1, low bit first), start value 0, no final XOR; check value 0xBB3D over
  the ASCII digits "123456789" (src: [[sources/wfx-hplx]] Z1;
  [[sources/j200lx-filer]] for the update; start value and parameter set
  from [[sources/hplx-connectivity-pack]] C1, whose CRC routine was
  modelled and agreed with CRC-16/ARC). Covered: SOH, command, counter,
  direction byte, revision byte, the data and the ETX. Not covered: the
  three SYN, the leading DLE STX, the DLE in front of ETX, and the CRC
  itself (src: [[sources/wfx-hplx]] Z1; [[sources/hplx-connectivity-pack]]
  C1).
- **Example frames.** CONNECT from a client: `16 16 16 10 02 01 40 00 01
  02 10 03 10 BF`; the CRC's low byte is 0x10 and goes out once.
  DISCONNECT: `16 16 16 10 02 01 41 00 01 02 10 03 2D 7F` (src:
  [[sources/hplx-connectivity-pack]] C1; both CRCs recomputed as
  CRC-16/ARC).
- **Receiving.** The receiver ignores everything until a SYN, accepts any
  number of further SYNs, then needs DLE STX; at least one SYN is needed,
  and a DLE STX straight after line noise is not taken. Inside the body,
  DLE DLE is one data byte and DLE ETX ends it; DLE followed by any other
  byte drops the frame, and so does a body longer than 2080 (0x820)
  bytes. The first byte after DLE ETX is compared with the CRC's low byte,
  the second with its high byte; a mismatch drops the frame without an
  answer (src: [[sources/hplx-connectivity-pack]] C1). A frame cut short
  and followed at once by the next one loses both, because the second
  DLE STX counts as an illegal DLE sequence; the retry recovers (inferred
  from the same receiver).
- **Counter.** The client numbers its packets; after 0xFF comes 0x00
  (src: [[sources/wfx-hplx]] Z1). CONNECT and DISCONNECT carry 0 (src:
  [[sources/wfx-hplx]] Z1). The reply repeats the request's command and
  counter; the client's first packet after CONNECT has counter 1 and each
  further packet one more (src: [[sources/wfx-hplx]] Z2;
  [[sources/hplx-connectivity-pack]] C1). The client advances its counter
  only after a matching reply. The server runs a request whose counter is
  one more than the last; one with **the same counter as the last** is a
  retransmission, which the server answers by resending its previous
  reply unchanged, without running the command again; any other counter
  gets status 0xFF3C (src: [[sources/hplx-connectivity-pack]] C1). So a
  resent read or write is safe.
- **Size.** At most 0x800 (2048) data bytes in a block (src:
  [[sources/wfx-hplx]] Z1; [[sources/j200lx-filer]]); the client asks for
  or sends blocks of any length up to that.

## Data conventions

- **Status and result words.** Every reply's data starts with a 16-bit
  status word: 0 when the server accepted and ran the request, otherwise
  a protocol-level refusal with no further data. All replies except
  CONNECT and DISCONNECT then carry a 16-bit result word: 0 or a
  file-system error code. The command's values follow (src:
  [[sources/hplx-connectivity-pack]] C1). These are the "two status bytes"
  of CONNECT and DISCONNECT and the "four" of all others in the recording
  (src: [[sources/wfx-hplx]] Z2). Replies with no payload of their own
  are six zero bytes: status, result and a zero value word (src:
  [[sources/wfx-hplx]] Z1, Z2; [[sources/hplx-connectivity-pack]] C1).
  Codes under "Errors".
- **Name:** a two-byte length, the characters of a DOS path (`C:\`,
  `C:\*.*`, `C:\ABCD.XYZ`), then a two-byte parameter word not counted in
  the length (src: [[sources/wfx-hplx]] Z1, Z2). The word is the file
  attribute for 03 and 05, the open mode for 0B, the search attribute for
  12, the new attribute for 1A, and unused otherwise; HP's client sends 0
  in it except for 1A (src: [[sources/hplx-connectivity-pack]] C1).
- **File handle.** The two bytes in front of the data of 01, 0C, 0F, 13,
  15, 17 and 18 (the "two zero bytes" of the published texts) are a
  handle: a slot number 0 to 7. The reply to 03, 05 and 0B returns the
  slot in the word after the result. The server has 8 slots, all free at
  CONNECT; the first file opened in a session gets 0, hence the zeros in
  the recording (src: [[sources/hplx-connectivity-pack]] C1).
- **DOS time and date** (two bytes each, time first): hours in bits 15-11,
  minutes 10-5, seconds divided by two in 4-0; years since 1980 in bits
  15-9, month 8-5, day 4-0 (src: [[sources/wfx-hplx]] Z1, corrected; see
  Contradictions; confirmed in [[sources/hplx-connectivity-pack]] C2,
  where 17 and 18 move one 32-bit value with the time in its low word).
  The recorded bytes 32 6E 8E 31 decode to 13:49:36 on 14 December 2004,
  which fits a session recorded at the turn of 2005.

## Commands

Codes are hex. Grouped by purpose (src: [[sources/wfx-hplx]] Z1, checked
against the recording Z2 where it shows the command;
[[sources/j200lx-filer]] for the 15 commands it lists; request data,
reply layouts and meanings checked against the server in
[[sources/hplx-connectivity-pack]] C1). HP's client sends no data at all
with 07, 09, 14, 16 and 1B, and the server ignores anything sent with
them (src: [[sources/hplx-connectivity-pack]] C1).

**Session.** 40 connect and 41 disconnect: no request data, the reply is
the status word alone. Details under "Connect and disconnect".

**Drives.**

- 1B asks for the drive letters. The reply: status, result, a count that
  includes a terminating zero, the letters (one byte each, upper case, no
  separators), the zero, then four zeros; for A, C and D: count 4, `41 43
  44 00`, `00 00 00 00`. Letters only: no read-only, removable or empty
  flags (src: [[sources/hplx-connectivity-pack]] C1). HP's PC server lists
  every drive DOS knows, without checking for a medium (src:
  [[sources/hplx-connectivity-pack]] C2); if the palmtop does the same, an
  empty card slot is listed and fails only when it is listed itself, with
  215 "drive not ready" (inferred).
- 07 asks for the current drive; reply status, result, the letter, a
  zero.
- 11 selects a drive: request the letter and a zero.
- 14 asks for the volume label and free space; reply status, result, a
  length (11), the 11-byte label, then the free space in bytes as four
  bytes. In the recording the label is blank and the free space drops by
  512 bytes when a 41-byte file is written and returns when it is deleted
  (src: [[sources/wfx-hplx]] Z2).

**Directories.**

- 06 asks for a drive's current directory: request the letter and a
  zero; reply status, result, the directory as a counted string (`C:\`),
  four zeros.
- 10 changes directory, 0A makes a directory, 0E removes an empty
  directory: request a name.
- 08 tests a name: request a name; reply status, result, then 0 (nothing
  there), 1 (a file) or 2 (a directory, named without a trailing
  backslash), then a zero. In the recording `C:\` answers 2.

**Listing.**

- 12 starts a listing: request a name with a DOS pattern (`C:\*.*`). It
  finds nothing yet; the first 09 does the first search, so an error such
  as a missing directory may show only there (inferred from the PC
  server's search, [[sources/hplx-connectivity-pack]] C2).
- 09 fetches the next entry: no request data; reply status, result, a
  length (0x16), the 22-byte entry, then a four-byte "found" value: 1 for
  a valid entry, 0 when there are no more (src: [[sources/wfx-hplx]] Z1,
  Z2; [[sources/hplx-connectivity-pack]] C1).
- The entry is the DOS find record: the attribute byte (0x10 directory,
  0x20 archive and so on), the DOS time, the DOS date, the size as four
  bytes, then the 8.3 name in a 13-byte field ended by a zero; the bytes
  after the zero are not cleared (spaces and stray letters in the
  recording) (src: [[sources/wfx-hplx]] Z1, Z2).
- The reply with "found" 0 ends the listing; its entry bytes are left over
  from the previous entry and are not a new file (src:
  [[sources/wfx-hplx]] Z2; [[sources/hplx-connectivity-pack]] C1).
- What is listed: HP's PC server always searches for normal files and
  directories, whatever attribute the request gives. Normal, read-only
  and archive files and subdirectories are listed; hidden and system
  files and the volume label are not; `.` and `..` are not filtered out,
  so a subdirectory's listing holds them as DOS returns them (src:
  [[sources/hplx-connectivity-pack]] C2; inferred for the palmtop). The
  recorded root listing of C: has no `.` or `..` entries, as in DOS.
- 16 drops the most recent listing context. The server keeps a ring of
  10 listing contexts: 12 moves to the next one and stores the pattern,
  09 reads from the current one, 16 steps back to the previous one. Its
  reply is always status 0, result 0 and a zero word, so it can be left
  out: the ring simply wraps after 10 listings (src:
  [[sources/hplx-connectivity-pack]] C1). Zampach found the same by trial
  (src: [[sources/wfx-hplx]] Z1). It is not a "prepare to read" step.

**Reading a file.**

- 0B opens a file for reading: request a name (word: open mode, 0); the
  reply gives the handle.
- 0C reads a block: request the handle and the wanted length (two bytes,
  0x800 in the recording); reply status, result, the length sent and the
  data. HP's client reads until it gets a block of length 0; a short
  block does not end its loop, so one empty read always follows (src:
  [[sources/wfx-hplx]] Z1, Z2; [[sources/hplx-connectivity-pack]] C1).

**Writing a file.**

- 03 creates a file and fails with 217 if it exists; 05 creates or
  overwrites (src: [[sources/wfx-hplx]] Z1;
  [[sources/hplx-connectivity-pack]] C1, C2). Request a name (word: file
  attribute); the reply gives the handle.
- 15 writes a block: request the handle, the length and the data; reply
  status and result. The word after the result has no defined value in
  HP's PC server, so it is not a "number of bytes written"; HP's client
  checks only the result (src: [[sources/hplx-connectivity-pack]] C1).

**The open file.** All with request data starting with the handle, and
only while a file is open through 03, 05 or 0B (src: [[sources/wfx-hplx]]
Z1; [[sources/hplx-connectivity-pack]] C1):

- 17 reads its time and date (reply status, result, time, date); 18 sets
  them.
- 13 reads the file position (four bytes); 0F sets it (request two more
  zero bytes, then the four-byte position). HP's client uses these two only in
  its file viewer, never for a copy (src:
  [[sources/hplx-connectivity-pack]] C1).
- 01 closes it.

**Other file operations.**

- 0D renames or moves: request the old name and its word, then the new
  name and its word; only within one drive (src: [[sources/wfx-hplx]] Z1;
  Z3 limitations; [[sources/hplx-connectivity-pack]] C1).
- 04 deletes a file: request a name.
- 19 reads a file's attribute byte (request a name; reply status, result,
  the attribute, a zero); 1A sets it (request a counted name, then the
  attribute and a zero as its word).

**Not commands.** 02 is not implemented: the server sends no reply at all,
although it has already advanced its counter. Codes above 1B, and 40 or
41 with a non-zero counter, get status 0xFF3E and no data (src:
[[sources/hplx-connectivity-pack]] C1).

### Recorded flows

The 200LX-to-200LX session (src: [[sources/wfx-hplx]] Z2), HP's client
against HP's server:

- Listing: select drive C:, change to `C:\`, ask the current directory,
  start a listing of `C:\*.*`, then next-entry requests until "found" is
  0.
- Copy to the server: create (03) `c:\abcd.xyz` in lower case, write one
  41-byte block, set the date, close. The next listing shows `ABCD.XYZ`
  with the archive attribute and the date that was set: the client keeps
  dates on a copy, and the server stores names in upper case.
- Copy from the server: open (0B), then, before 16, a listing whose first
  entry returned is the opened file (not the directory's first entry),
  then 16, two reads of 0x800 (41 bytes, then empty), read the date (17),
  close. HP's PC client does a size lookup at this point (12 on the
  file's own name, one 09, then 16); the recorded step is the same lookup
  (inference from [[sources/hplx-connectivity-pack]] C1).
- The test of `C:\` (08) went out three times with three counters; the
  recorder did not know why.

### HP's client sequences

What HP's PC client sends for each action (src:
[[sources/hplx-connectivity-pack]] C1). Every command from 01 to 1B except
02, plus 40 and 41, appears somewhere.

| Action | Commands |
| --- | --- |
| Open a remote window | 07, 06, then "change directory" |
| Change directory | 11, 10, 11, 06 (reads back the canonical path) |
| Drive information | 11, 14 |
| Drive list | 1B (not sent to a revision-1 server; C: is assumed) |
| List a directory | 12 with path and pattern (default `*.*`), 09 until "found" is 0; no 16 |
| Get a file | 0B (mode 0); 12 on the file's name, one 09 (size for the progress display), 16; 0C with 0x800 until an empty block; 17; 01 |
| Put a file | 03 (attribute 0), on 217 ask, then 05; 15 in blocks up to 0x800; 18 with the local date; 01. On failure the partial file is deleted with 04 |
| Rename | 08 on the new name (refused if it exists), then 0D |
| Delete | 0E for a directory, 04 for a file; a tree is walked first with 12, 09, 16 |
| Make directory | 0A |
| Disconnect | 41 |

## Connect and disconnect

- **Connect** (src: [[sources/hplx-connectivity-pack]] C1): 40 with
  counter 0, no data, revision 2. CONNECT goes through the ordinary
  retry rule (three attempts with 2 s each for the first byte); if all
  fail, the client tries the whole round once more, so up to six
  transmissions over about 12 s. If the first round's reply carries
  status 0xFF3B, the client switches to revision 1 and tries again. Status
  0 means connected; the counter then moves to 1.
- **No preamble.** Nothing has to precede the first CONNECT: no NUL bytes
  and no handshake, since the receiver hunts for SYN (src:
  [[sources/hplx-connectivity-pack]] C1).
- **The server** answers a valid CONNECT while idle (counter 0, direction
  0x01) with status 0 and its revision (2 by default) and starts a session
  with all 8 file slots free and the counter at 0. A CONNECT inside a
  session resets it the same way (files left open are not closed) and
  takes over the client's revision byte: 1 means revision 1, anything
  else 2. While no session is open, any other frame is answered with a
  CONNECT reply (command 40, counter 0) carrying 0xFF3B, whatever its
  command (src: [[sources/hplx-connectivity-pack]] C1). A client that
  sends an ordinary request to a server that has silently left the
  session thus gets a reply with the wrong counter; HP's client reports
  that as a broken link and disconnects.
- **Disconnect:** 41 with counter 0 and no data, sent once. HP's client
  does not wait for the reply or check it; it pauses about 1 s. The server
  answers with status 0 and goes back to normal Filer use, still in server
  mode and ready for the next CONNECT (src:
  [[sources/hplx-connectivity-pack]] C1). After a failed connect HP's
  client still sends one DISCONNECT.
- **Other session ends on the server:** Ctrl+Break on the server, all
  file slots in use when an open or create arrives (0xFF3D), or a frame
  without the leading SOH. Nothing is sent in these cases, and the server
  closes all its open files (src: [[sources/hplx-connectivity-pack]] C1).
- **No keep-alive.** The client sends nothing while idle, and the server
  waits for the next frame without a time limit (src:
  [[sources/hplx-connectivity-pack]] C1). Whether the palmtop's auto-off
  ends a long idle session is open.

## Errors

### Protocol-level codes

In the status word, or produced by HP's client itself (src:
[[sources/hplx-connectivity-pack]] C1):

| Code | Signed | From | Meaning |
| --- | --- | --- | --- |
| 0xFF3B | -197 | server | Not a valid CONNECT while no session is open (wrong command, counter not 0, or wrong direction byte); sent in a CONNECT reply (command 40, counter 0) |
| 0xFF3C | -196 | server | Counter neither the last one nor the next one; not run; the reply carries the server's last good counter |
| 0xFF3D | -195 | server | All 8 file slots in use at an open or create; the server leaves the session without replying |
| 0xFF3E | -194 | server | Unknown command; status only |
| 0xFF38 | -200 | client | The transmit queue made no progress for 5 s |
| 0xFF39 | -199 | client | No reply, or no complete valid frame, in time, after all attempts |
| 0xFF3A | -198 | client | The reply's counter differs from the request's, or its direction byte is not 0x81; no retry |

HP's client shows all of these as "Remote connection broken", sends
DISCONNECT and closes the session (src:
[[sources/hplx-connectivity-pack]] C1). For ordinary commands it reads
only the result word; the status word only after CONNECT.

### File-system codes

In the result word. They are the System Manager's file error codes; on
the PC the communications kernel produces them, mostly from DOS errors
(src: [[sources/hplx-connectivity-pack]] C2; inferred for the palmtop).
The meanings come from matching the DOS errors behind each code with the
order of the system's error texts (inferred; every DOS error lands on a
fitting text).

| Code | Hex | Meaning | Typical cause (PC server) |
| --- | --- | --- | --- |
| 200 | 0xC8 | invalid file or path name | a control character, a space, any of `"/[]\|<>+=;,`, or a colon not after the drive letter (`\`, `*`, `?` allowed) |
| 201 | 0xC9 | cannot find system directory | |
| 202 | 0xCA | too many alarms | |
| 203 | 0xCB | disk full | a write wrote fewer bytes than asked |
| 204 | 0xCC | print queue error | |
| 205 | 0xCD | sharing or network error | |
| 206 | 0xCE | I/O device error | |
| 207 | 0xCF | internal software error | catch-all |
| 208 | 0xD0 | file does not exist | |
| 209 | 0xD1 | directory (path) does not exist, or invalid drive | |
| 210 | 0xD2 | too many open files | |
| 211 | 0xD3 | access denied ("file creation error") | read-only file, non-empty directory, a file created over an existing directory |
| 212 | 0xD4 | cannot delete the current directory | |
| 213 | 0xD5 | cannot move a file to another drive | |
| 214 | 0xD6 | disk write-protected | |
| 215 | 0xD7 | drive not ready | |
| 216 | 0xD8 | printer out of paper | |
| 217 | 0xD9 | file already exists | create (03) on an existing name |
| 218 | 0xDA | cannot create directory | |
| 219 | 0xDB | critical-error handler failure | |
| 220 | 0xDC | too many redirections | |
| 221 | 0xDD | duplicate redirection | |
| 222 | 0xDE | insufficient memory | |

- DOS's "no more files" is not an error on the wire; it becomes the
  "found" value 0 of 09 (src: [[sources/hplx-connectivity-pack]] C2).
- BSD Filer's 0xD1 in "the third status byte" after a failed directory
  change is the low byte of 209.
- A write to the ROM drive D: should give 214 or 211, depending on the
  palmtop's disk driver; PC code cannot tell which.
- HP's client reactions (src: [[sources/hplx-connectivity-pack]] C1):
  217 after 03 asks whether to overwrite and repeats with 05; 211 after 03
  tests the name with 08 and, for a directory, refuses to overwrite it
  with a file; 214 after 0D clears the read-only bit (19, then 1A); any
  other non-zero result shows the system text for the code and re-reads
  the window. A non-zero result during a copy stops it, closes both files
  and deletes the partial destination.

## Timeouts and retries

HP's client (src: [[sources/hplx-connectivity-pack]] C1; times in PC
clock ticks of 1/18.2 s):

- Up to **three attempts** per request. Each one empties both serial
  buffers, sends the frame and waits for the transmit queue to drain (no
  limit while it moves; 5 s without progress fails with 0xFF38, no
  retry).
- First byte of the reply: within **2.0 s (36 ticks) for CONNECT, 5.9 s
  (108 ticks) for every other command**.
- Then the whole frame within a window that depends on the speed:

  | Speed | Window |
  | --- | --- |
  | 19200 and faster | 2.0 s (36 ticks) |
  | 9600 | 5.0 s (91 ticks) |
  | 4800 | 5.9 s (108 ticks) |
  | 2400 | 11.9 s (216 ticks) |
  | 1200 | 24.7 s (450 ticks) |
  | 300 | 79 s (1440 ticks) |

- No reply or no valid frame (a CRC error included) is a failed attempt;
  the next attempt resends the identical frame with the same counter.
  After the third failure the client sends DISCONNECT and drops the
  session.
- The reply's command and revision bytes are not checked; its counter and
  direction byte are (0xFF3A on a mismatch).
- There is no NAK or ACK: bad frames are dropped silently and only the
  sender's timeout recovers. The server has no time limits of its own.

Other clients: LX Tools before 1.1b gave up after a timeout; 1.1b
retransmits, because errors appeared at high speeds (src:
[[sources/lxtools]] L1). BSD Filer's own notes say its handling of
transmission errors is incomplete and advise 4800 or 2400 baud if errors
occur (src: [[sources/bsd-filer]] B1).

## Existing implementations

- Clients: LX Tools for Unix (GPL) (src: [[sources/lxtools]]); WFX-HPLX
  for Total Commander (GPL) (src: [[sources/wfx-hplx]]); j200lx in Java
  (src: [[sources/j200lx-filer]]); HP's Connectivity Pack (proprietary)
  (src: [[sources/palmtop-paper]] P4).
- Servers: HP's desktop Filer in the Connectivity Pack, with the palmtop
  as client (src: [[sources/palmtop-paper]] P4); BSD Filer for Unix
  (v0.3: Linux and Solaris too, `-root` and `-speed` options) and its DOS
  port LXFS4D, freeware for non-commercial use (src: [[sources/bsd-filer]]).
  BSD Filer's limits per its readme: only files below its start
  directory; Unix names that break DOS rules or contain upper-case letters
  are hidden; delete only renames to `.BAK`; copies to Unix lose the
  timestamp (v0.2) (src: [[sources/bsd-filer]] B1).

## Contradictions

Each settled by HP's code ([[sources/hplx-connectivity-pack]] C1, C2):

- j200lx shows the reply's byte after the counter as a status (0x81);
  Zampach's text and the recording show 0x81 as a fixed direction marker
  in every reply (src: [[sources/j200lx-filer]]; [[sources/wfx-hplx]] Z1,
  Z2). **Settled:** a direction byte; the status is the word after the
  header. The byte after it, read as STX by every text, is the revision.
- CONNECT's reply: no data in j200lx, two zero bytes in Zampach's text and
  the recording. **Settled:** the status word, two bytes.
- The listing reply: two zero bytes before the entry length in both
  texts, four in the recording. **Settled:** four, status and result.
- Rename: j200lx sends the two names with no zero pair between them;
  Zampach's text puts a zero pair after each. **Settled:** a word after
  each name, as Zampach has it.
- Ending a read or write: j200lx sends one zero byte with code 01 ("data
  end"); Zampach and the recording send two (close). **Settled:** 01 is
  close, and the two bytes are the handle.
- Time and date: j200lx reads them as one 32-bit word with six bits of
  seconds; Zampach and DOS have two 16-bit words with five bits of
  seconds counted in twos. **Settled:** DOS packing, time in the low word.
- Zampach's text has two slips: it takes the day from the time word, and
  weights a position's top byte by 0x100000 instead of 0x1000000.
  Positions and sizes are plain 32-bit little-endian numbers.

Raised in [[questions/filer-protocol-open-points]].

## Open

- Whether the palmtop's own server matches HP's PC Filer server in every
  detail: the error code for a write to D:, the drive list with an empty
  card slot, and whether hidden and system files are listed.
- The highest reliable speed with 2048-byte blocks, given no flow control
  and an 8250 UART ([[questions/hplx-serial-speed]]).
- Whether the palmtop stays on (auto-off) while serving, and what a
  connect does to a palmtop that is off (see [[hardware/hp100lx-200lx]]
  "Filer server mode").
- Why the recorded session sent the test of `C:\` (08) three times with
  three counters.
