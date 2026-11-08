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
tags: [filer, protocol, serial, server, crc]
models: [100lx, 200lx]
---

# The Filer protocol

The link that the palmtops' built-in Filer uses to work on the files of
another machine, over the cable or IR ([[hardware/hp100lx-200lx]]). HP
never published it; everything here comes from third-party descriptions
and a recorded session between two 200LX machines. Z1 to Z3 are the items
of [[sources/wfx-hplx]], B1 to B3 those of [[sources/bsd-filer]].

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
- Strict request and reply: each packet the client sends is answered by
  exactly one packet from the server (src: [[sources/wfx-hplx]] Z1;
  [[sources/j200lx-filer]]). Every exchange in the recorded session is one
  request followed by one reply (src: [[sources/wfx-hplx]] Z2).

## Physical layer

- No flow control at all, neither RTS/CTS nor XON/XOFF; three wires (TX to
  RX both ways, ground) are enough; 2400 to 115200 baud (src:
  [[sources/wfx-hplx]] Z1, Z3 history 1.01; [[sources/lxtools]] L1 ignores
  CTS/RTS and carrier detect). Speeds in practice:
  [[questions/hplx-serial-speed]].
- Remote Settings offer no parity or word-length choice (src:
  [[sources/palmtop-paper]] P4); the packets carry every byte value, so 8
  data bits. No parity and one stop bit are assumed (unverified).

## Packet framing

Both directions use the same frame, in the style of IBM's binary
synchronous (BSC) links; BSD Filer's author lists two BSC texts as his
references (src: [[sources/bsd-filer]] B1). In order (src:
[[sources/wfx-hplx]] Z1; [[sources/j200lx-filer]]; the header order as
seen in Z2):

1. Lead-in, not covered by the CRC: three SYN (0x16), then DLE STX (0x10
   0x02).
2. Header: SOH (0x01); the command code; the packet counter; a direction
   byte, 0x01 in requests and 0x81 in replies; STX (0x02).
3. The command's data, if any. Numbers are little-endian.
4. DLE ETX (0x10 0x03).
5. The CRC, two bytes, low byte first.

- **DLE doubling.** In the part the CRC covers, every 0x10 byte is sent
  twice (src: [[sources/wfx-hplx]] Z1). That part includes the header, so
  the command code 0x10 (change directory) and a counter value of 0x10 are
  doubled too (inference from the rule). Whether the CRC sees one copy or
  both, and whether a 0x10 in the CRC bytes is doubled, the texts do not
  say; one copy and no doubling would match BSC practice (unverified).
- **CRC.** CRC-16 updated per byte with the reflected polynomial 0xA001
  (x^16 + x^15 + x^2 + 1, processed low bit first) (src:
  [[sources/wfx-hplx]] Z1; [[sources/j200lx-filer]]). Covered: SOH,
  command, counter, direction byte, STX, the data and the ETX. Not
  covered: the three SYN, the leading DLE STX, the DLE in front of ETX,
  and the CRC itself (src: [[sources/wfx-hplx]] Z1, the excluded bytes
  are marked there). Start value: neither text states it; with 0, no
  final XOR and the reflected update this is the parameter set known as
  CRC-16/ARC, check value 0xBB3D over the ASCII digits "123456789" (start
  value 0 unverified: no recorded packet with its CRC has been checked;
  see [[questions/filer-protocol-open-points]]).
- **Counter.** The client numbers its packets; after 0xFF comes 0x00
  (src: [[sources/wfx-hplx]] Z1). CONNECT and DISCONNECT carry 0 (src:
  [[sources/wfx-hplx]] Z1). The reply repeats the request's command and
  counter; in the recording the client's first packet after CONNECT has
  counter 1 and each further packet one more (src: [[sources/wfx-hplx]]
  Z2).
- **Size.** At most 0x800 (2048) data bytes in a block (src:
  [[sources/wfx-hplx]] Z1; [[sources/j200lx-filer]]); the client asks for
  or sends blocks of any length up to that.

## Data conventions

- **Name:** a two-byte length, the characters of a DOS path (`C:\`,
  `C:\*.*`, `C:\ABCD.XYZ`), then two zero bytes not counted in the length
  (src: [[sources/wfx-hplx]] Z1, Z2).
- **Status:** every reply's data starts with zero bytes: two for CONNECT
  and DISCONNECT, four for all others in the recording (src:
  [[sources/wfx-hplx]] Z2). Their meaning when not zero is not documented.
  Replies with no payload of their own are six zero bytes (src:
  [[sources/wfx-hplx]] Z1, Z2).
- **DOS time and date** (two bytes each, time first): hours in bits 15-11,
  minutes 10-5, seconds divided by two in 4-0; years since 1980 in bits
  15-9, month 8-5, day 4-0 (src: [[sources/wfx-hplx]] Z1, corrected; see
  Contradictions). The recorded bytes 32 6E 8E 31 decode to 13:49:36 on
  14 December 2004, which fits a session recorded at the turn of 2005.

## Commands

Codes are hex; "status" means the leading zero bytes above. Grouped by
purpose (src: [[sources/wfx-hplx]] Z1, checked against the recording Z2
where it shows the command; [[sources/j200lx-filer]] for the 15 commands
it lists).

**Session.** 40 connect and 41 disconnect: no request data, the reply is
two status bytes.

**Drives.**

- 1B asks for the drive letters: request two zero bytes; reply status,
  then the letters of the defined drives as a counted string, then four
  zeros.
- 07 asks for the current drive: request two zero bytes; reply status,
  the letter, a zero.
- 11 selects a drive: request the letter and a zero.
- 14 asks for the volume label and free space: request two zero bytes;
  reply status, a length (11), the 11-byte label, then the free space in
  bytes as four bytes. In the recording the label is blank and the free
  space drops by 512 bytes when a 41-byte file is written and returns
  when it is deleted.

**Directories.**

- 06 asks for a drive's current directory: request the letter and a
  zero; reply status, the directory as a counted string (`C:\`), four
  zeros.
- 10 changes directory, 0A makes a directory, 0E removes an empty
  directory: request a name.
- 08 tests a name: request a name; reply status, then 0 (nothing there),
  1 (a file) or 2 (a directory, named without a trailing backslash), then
  a zero. In the recording `C:\` answers 2.

**Listing.**

- 12 starts a listing: request a name with a DOS pattern (`C:\*.*`).
- 09 fetches the next entry: no request data; reply status, a length
  (0x16), the 22-byte entry, a "more" byte (1 while entries remain), then
  three zeros.
- The entry: the DOS attribute byte (0x10 directory, 0x20 archive and so
  on), the DOS time, the DOS date, the size as four bytes, then the 8.3
  name in a 13-byte field ended by a zero; the bytes after the zero are
  not cleared (spaces and stray letters in the recording) (src:
  [[sources/wfx-hplx]] Z1, Z2).
- The reply with the "more" byte 0 ends the listing and repeats the
  previous entry, which is not a new file (src: [[sources/wfx-hplx]] Z2).
  The recorded root listing of C: has no `.` or `..` entries.

**Reading a file.**

- 0B opens a file for reading: request a name.
- 16 prepares the transfer: no data; Zampach found it can be left out
  (src: [[sources/wfx-hplx]] Z1).
- 0C reads a block: request two zero bytes and the wanted length (two
  bytes, 0x800 in the recording); reply status, the length sent and the
  data. A block shorter than asked for is the last; HP's own client then
  asks once more and gets an empty block (src: [[sources/wfx-hplx]] Z1,
  Z2).

**Writing a file.**

- 03 creates a file and fails if it exists; 05 creates or overwrites
  (src: [[sources/wfx-hplx]] Z1). Request a name.
- 15 writes a block: request two zero bytes, the length and the data;
  reply status and the number of bytes written.

**The open file.** All with request data starting with two zero bytes,
and only while a file is open through 03, 05 or 0B (src:
[[sources/wfx-hplx]] Z1):

- 17 reads its time and date (reply status, time, date); 18 sets them.
- 13 reads the file position (four bytes); 0F sets it (request two more
  zero bytes, then the four-byte position).
- 01 closes it.

**Other file operations.**

- 0D renames or moves: request the old name, then the new name; only
  within one drive (src: [[sources/wfx-hplx]] Z1; Z3 limitations).
- 04 deletes a file: request a name.
- 19 reads a file's attribute byte (request a name; reply status, the
  attribute, a zero); 1A sets it (request a counted name, the attribute
  and a zero, no zero pair after the name).

Code 02 seems not to exist (src: [[sources/wfx-hplx]] Z1).

### Recorded flows

The 200LX-to-200LX session (src: [[sources/wfx-hplx]] Z2), HP's client
against HP's server:

- Listing: select drive C:, change to `C:\`, ask the current directory,
  start a listing of `C:\*.*`, then next-entry requests until "more" is 0.
- Copy to the server: create (03) `c:\abcd.xyz` in lower case, write one
  41-byte block, set the date, close. The next listing shows `ABCD.XYZ`
  with the archive attribute and the date that was set: the client keeps
  dates on a copy, and the server stores names in upper case.
- Copy from the server: open (0B), then, before 16, a listing of
  `C:\*.*` whose first entry returned is the opened file (not the
  directory's first entry), then 16, two reads of 0x800 (41 bytes, then
  empty), read the date (17), close.
- The test of `C:\` (08) went out three times with three counters; the
  recorder did not know why.

## Errors and timeouts

- No source documents an error reply from HP's server. j200lx calls the
  reply's direction byte a status, "the result of the operation", but only
  shows 0x81 (src: [[sources/j200lx-filer]]); the leading zero bytes are
  the other candidate.
- No timeouts or retry rules are documented. LX Tools before 1.1b gave up
  after a timeout; 1.1b retransmits, because errors appeared at high
  speeds (src: [[sources/lxtools]] L1). The recorded session shows a
  request sent three times (above).
- BSD Filer's own notes say its handling of transmission errors is
  incomplete and advise 4800 or 2400 baud if errors occur (src:
  [[sources/bsd-filer]] B1).

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

- j200lx shows the reply's byte after the counter as a status (0x81);
  Zampach's text and the recording show 0x81 as a fixed direction marker
  in every reply (src: [[sources/j200lx-filer]]; [[sources/wfx-hplx]] Z1,
  Z2).
- CONNECT's reply: no data in j200lx, two zero bytes in Zampach's text and
  the recording.
- The listing reply: two zero bytes before the entry length in both
  texts, four in the recording.
- Rename: j200lx sends the two names with no zero pair between them;
  Zampach's text puts a zero pair after each.
- Ending a read or write: j200lx sends one zero byte with code 01 ("data
  end"); Zampach and the recording send two (close).
- Time and date: j200lx reads them as one 32-bit word with six bits of
  seconds; Zampach and DOS have two 16-bit words with five bits of
  seconds counted in twos.
- Zampach's text has two slips: it takes the day from the time word, and
  weights a position's top byte by 0x100000 instead of 0x1000000.

Raised in [[questions/filer-protocol-open-points]].

## Open

- The meaning of non-zero status bytes and the error replies for a missing
  file, an existing name with 03, a full disk, a non-empty directory and
  a write to the ROM drive D:.
- The CRC start value, whether doubled DLEs count once, and whether DLEs
  in the CRC bytes are doubled.
- Whether anything must precede the first CONNECT, and how long the
  server waits before it drops a half-received packet.
- The highest reliable speed with 2048-byte blocks, given no flow control
  and an 8250 UART ([[questions/hplx-serial-speed]]).
- Retries: whether a request repeated with the same counter is safe, as
  the recorded triple send suggests a client may also move on with a new
  counter.
- What the drive list (1B) holds on a 100LX, with and without a card.
- Whether `.` and `..` appear in a subdirectory listing; whether hidden
  and system files are listed.
- Whether the palmtop stays on (auto-off) while serving, and what a
  connect does to a palmtop that is off.
- Whether 16 is ever required by HP's server.
