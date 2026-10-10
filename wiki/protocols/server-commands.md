---
title: "HP Kermit server mode and its commands"
type: protocol
status: draft
sources: ["[[sources/hp48g-ug]]", "[[sources/hp48-sikug]]", "[[sources/hp48-kermit-hints]]", "[[sources/hp48-faq]]", "[[sources/io-guide]]", "[[sources/kermit-protocol-manual]]"]
tags: [kermit, server, satx]
models: [48sx, 48gx]
---

# HP Kermit server commands

Generic server semantics are on [[protocols/kermit]]. This page lists what
the HP 48 server actually answers.

## Entering and leaving

- G/GX: right-shift right-arrow, or the SERVER command (src:
  [[sources/hp48-faq]] 4.19).
- The client ends server mode with FINISH (generic F); generic L does the
  same (src: [[sources/hp48-kermit-hints]], protocol notes).

## What HP says the server answers

"If the HP 48 is a server, you can send Kermit commands to it (although it
only responds to GET (KGET), SEND, REMOTE DIR, REMOTE HOST, FINISH, and
LOGOUT)" (src: [[sources/hp48g-ug]] 27-13). In packet terms: R, S, G D, C,
G F and G L. HP's own PC software used exactly these: file list on connect,
send/get, archive/restore, and Remote Command, which sends a command line
and shows the resulting stack (src: [[sources/hp48-sikug]] 2-1 to 3-2).

## Commands the HP 48 server answers (Horn)

| Packet | HP 48 server response |
| --- | --- |
| S | receive file(s) (normal SEND from the host) |
| R name | send the named variable (normal GET); see note below |
| C text | executes OBJ\-> on the text (i.e. runs it as a command line), then returns the whole stack as one string, or "Empty Stack" |
| G L, G F | leave server mode |
| G D | "directory": one string listing the current VARS with name, size in bytes (including name), type in English, checksum as a decimal real |
| I | parameter exchange (the HP itself sends I packets to set the block check, src: [[sources/io-guide]] 4); Horn calls the responses to I, S and E sent with PKT "useless" |
| other G subcommands | not recognised |
| other types | not recognised |

(src: [[sources/hp48-kermit-hints]], HP-48 Kermit protocol notes, from Joe
Horn)

- Changing the server's directory: REMOTE CD does not work; send a host
  command that evaluates the directory name, e.g. `REMOTE HOST { dir } EVAL`
  (same source).
- "name" "R" PKT sent from another HP 48 delivers the object "in PC format as
  a string"; use KGET instead. For a PC client, R is the normal GET (same
  source).
- No long packets, no sliding windows (src: [[sources/hp48-kermit-hints]],
  Protocol settings).

## The HP 48 as a client

The HP can drive another Kermit server with KGET, SEND, FINISH and with PKT,
which sends one packet of a given type and data and returns the reply as a
string; an error packet is shown and its text kept for KERRM (src:
[[sources/hp48g-ug]] 27-13). Horn's list of what MS-DOS
Kermit answers (I with an init string, R, C, GI, GC, GF/GL, GD, GU, GW, GM,
GH; GE and GT need length-encoded operands) is in the source; satx is the
server side's peer, so this matters only if satx implements a server (src:
[[sources/hp48-kermit-hints]]).

## Observed on the saturnng emulator (2026-10-05)

From driving ROM J (48SX), ROM R (48GX) and ROM 2.15 (49G) in the
`satx` emulator container with a Kermit client, not from a document:

- The C reply is the stack display, line by line `N: value` from the
  highest level down, right-aligned at the display width, or `Empty Stack`.
  Results stay on the calculator's stack. The 49G truncates long values at
  the display width (a 20-element list shows as `{1,2,3,4,5,6,7,8,9,10,`)
  and in algebraic mode shows lists with commas (`{9600.,0.,0.,0.,3.,1.}`)
  and names without quotes.
- A failed command is not an E packet: the reply starts with
  `Error: <message>` followed by the stack, and the command's arguments stay
  on the stack (a syntax error leaves the command line as a string).
- Evaluating an undefined name pushes it (the 48SX has no VERSION command:
  `VERSION` returns `'VERSION'`); `1 0 /` is `Infinite Result` on the 48
  models and returns `∞` on the 49G.
- The C text is compiled as typed: ASCII trigraphs like `\->` are a syntax
  error; the HP character byte (0x8D for `→`) works.
- A C packet whose data is over 77 encoded bytes does not fit one packet at
  the default MAXL with block check 1.
- `ARCHIVE` to `:IO:name` fails in server mode with `Port Not Available`
  (also after CLOSEIO). `ARCHIVE` to `:0:name` works on all three models,
  including the 48SX; `:0:name RCL` returns the backed-up HOME as a
  directory object; purging the port object while the recalled directory is
  still on the stack fails with `Object In Use`. Storing a directory into
  `:0:name` makes a backup object that `RESTORE` accepts: the 48GX
  warm-started with the restored HOME and left server mode; the port object
  stayed.
- A command packet that arrives right after the client's final ACK of the
  previous transaction is lost: the server answers only after its own
  timeout (about 6 s) has sent a NAK and the client resent. A pause of
  100 ms between transactions avoided it on all three models. The idle
  server NAKs packet 0 about every 5 s (observed in saturnus, 2026-10-08).
- A C packet resent after a lost ACK or a NAK runs again: the server runs
  a command when it arrives and cannot tell a resend from a new one (seen
  once on the emulated 49G under load). A C the server rejects is NAKed
  about 11 ms after it arrives and does not run (48SX in saturnus) (satx
  calculator quirks, 2026-10-05).
- The idle server's NAKs pile up in the host's input between commands, and
  one is waiting when a host connects; discard input before each command
  (satx).
- The `G D` reply on the 48GX and 49G begins with a line holding the
  current path and the free bytes (`{ HOME } 127847`); the 48SX has none.
  Lines end in CR LF; the 49G writes sizes and checksums with a trailing
  point (`IOPAR 29.5 List 10777.`) (satx traces `48sx-dir`, `48gx-dir`,
  `49g-dir`).
- A command whose client died mid-way is still finished; its reply arrives
  as the answer to the next client's first command (satx).
- The 48SX loses keys typed within about 2 s after FINISH (satx; saturnus
  waits 2.5 s).
- On the 49G in RPN mode, typing `SERVER` leaves a tagged `SERVER` and
  `NOVAL` on the stack after the server ends; ALG mode and the 48GX leave
  nothing. Alpha mode can still be on after the server ends (satx,
  saturnng 49G 2.15).
- In server mode `LCD→` returns the "Awaiting Server Cmd." banner, never
  the stack (satx).
- `PURGE` of a missing `:0:` port object raises no error (satx).
- `'SIN' CRDIR` is `Invalid Syntax`, as for `STO` (saturnus decision log,
  "iteration 29: create a directory").
- `STO` strips the tag of a tagged object (`:T:5 'X' STO` stores 5).
- Flag -35 selects the transfer format for GET and SEND: set binary, clear
  ASCII; a fresh calculator of all three models is in ASCII mode.
- Pressing ON while the server evaluates a C command aborts the evaluation
  and ends server mode: no reply is sent, the stack shows again with
  whatever the evaluation had pushed (the 48SX kept a partial sum, the 49G
  a tagged `SERVER` object), and the next command needs `SERVER` again
  (48SX ROM J and the 49G ROM of 2009 in saturnus, 2026-10-05). If ON
  hits while the 48SX is still compiling the C text (about the first
  second), the text comes back on level 1 as a string, with HP
  characters such as Σ for the trigraph `\GS`; the 48GX left nothing in
  the same case (saturnus, 2026-10-05).
- On the 49G, ON pressed during a packet exchange (not during evaluation
  of a C command) ends only that transaction; the server goes on serving,
  and further presses of ON are needed to leave it (ROM 2.10; saturnus
  decision log, "49G writes in algebraic mode", 2026-10-09).
- The C text is parsed as an RPN command line on the 48SX and also on
  the 49G in ALG mode: `SIN(0.5)` without quotes is `Invalid Syntax` (the
  text is left on level 1 as a string); `'SIN(0.5)' EVAL` works. A fresh
  48SX is in degrees, the 49G in radians.
- A server entered from algebraic mode on the 49G leaves the stack packed
  in one list after FINISH, followed by the tagged command line `SERVER`
  ([[hardware/hp48-system-ram]]). So -95 cannot be cleared by a host
  command the way -35 can: clear it before typing `SERVER` (`CF(-95)` in
  ALG mode) and set it again after FINISH (`-95 SF` in RPN mode). Inside
  the server the host commands are RPN either way and the replies are the
  same (ROM 2.10; saturnus decision log, "49G writes in algebraic mode",
  2026-10-09).

## Compiling text through the server (saturnus, 2026-10-08)

Observed in saturnus on ROM J (48SX), ROM R (48GX) and ROM 2.10 (49G,
RPN mode) while building its object editor, not from a document:

- A string stored by a binary SEND and compiled by the C command
  `'S' RCL 'S' PURGE STR→` gives the calculator's own parse error in the
  reply (`Error: Invalid Syntax`, the string back on level 1), so text
  of any length (3500 characters measured) can be compiled without
  fitting it into one C packet.
- Text wrapped in `{ }` before `STR→` is compiled but not run: the list
  holds a command as a command object, and `DUP SIZE` says how many
  objects the text held. `1 GET` then `'name' STO` stores the one object;
  the checksum of a variable is unchanged when its own decompiled text
  (every digit of a real, binary integers at 64 bits, tags as `:T:obj`)
  is stored back this way, for reals, complex numbers, strings,
  algebraics, tags inside lists (a tagged object itself loses its tag at
  `STO`), units, binaries, lists, arrays and programs
  (also in FIX 3).
- The reply shows numbers in the display mode (`SIZE` is `1.000` in
  FIX 3), so a host that reads a count from it reads the digits, not the
  text.
- How `STR→` splits text into tokens, the same on all three ROMs (a
  string `{` newline, the text, newline `}` compiled, 2026-10-08):
  - `@` inside a word ends the word and starts a comment, to the next `@`
    or the line's end: `X@ 1 2` gives `{ X }`, `X@Y 1` gives `{ X }`,
    `1@ 2` gives `{ 1 }`; `1 @c@ 2` and `1 @c` newline `2` give `{ 1 2 }`.
    `{ X@ } 'P' 9 {` gives `{ { X } }`: the comment hides the rest of the
    line, and the list left open at the end of the text is closed.
  - `"` inside a word ends the word and starts a string: `A"B" C` gives
    `{ A "B" C }`; `A"B C` leaves the string open, and it runs to the end
    of the text, taking the newline and the `}` with it
    (`{ A "B C` newline `}" }`). `"a"b` gives `{ "a" b }`.
  - `{` inside a word starts a list (`X{ 1` gives `{ X { 1 } }`), but
    `}` and `«` inside a word do not end it: `X} 1` and `X« 1` are
    `Invalid Syntax`. `'` splits (`X'Y' 1` gives `{ X Y 1 }`), and so does
    a tag (`X:T:5` gives `{ X :T: 5 }`).
- `'SQ' STO` is `Invalid Syntax` on the 48SX: SQ is a command, so a
  command's name in quotes is not a variable name (as for `'SIN'`).

## XModem from the server (saturnng, 2026-10-05)

Measured on the saturnng emulator by satx, 2026-10-05 (HP 49G ROM 2.15, HP
48GX ROM R); not yet confirmed on hardware. Evidence: satx
`crates/xmodem-proto/traces/49g-server-xrecv.trace` and
`48gx-server-xsend.trace`.

- XRECV and XSEND cannot be started through the server. A C packet
  `'NAME' XRECV` (or XSEND, also with `CLEAR CLOSEIO` first) is answered at
  once with `Error: Port Not Available` on both models, like `ARCHIVE` to
  `:IO:` above. The calculator stays in server mode ("Awaiting Server Cmd.",
  idle NAKs continue, an I packet is answered) and the name stays on the
  stack. Start XModem from the keyboard after FINISH; see
  [[protocols/xmodem-hp]].
- If the host does not complete the reply to a C command, the server resends
  its S packet about every 2 s, then sends `E Timeout` and returns to idle
  NAKs.
- Emulator caveat (a saturnng observation, not calculator behaviour):
  pressing ON to leave the server while a Kermit exchange was settling left
  the 49G answering "Port Not Available" even to SERVER. The state survived
  CLOSEIO, clearing flag -33 and a container restart; only a fresh container
  fixed it.

## Packets the server sends (saturnng, satx traces, 2026-10-04)

- Its S packet carries `~* @-#Y3` on the 48SX J, 48GX R and 49G 2.15:
  MAXL 94, TIME 10, no padding, EOL CR, QCTL `#`, QBIN `Y`, CHKT 3, no
  REPT field.
- Its ACK to an I packet carries `~* @-# 3` (no 8-bit quoting).
- The 49G sends a NAK before its S.
- A GET of a missing name is answered with an E packet, `Undefined Name`.
- Command packets (I, S, R, C, G) must use block check 1 even after an I
  exchange agreed on 3; a C packet with a type-3 check is NAKed.

## Open

- The text of the HP's other E messages.
- Whether the 49G server supports more generic commands; the 49G uses XSERV
  for its richer server ([[protocols/xserv]]).
