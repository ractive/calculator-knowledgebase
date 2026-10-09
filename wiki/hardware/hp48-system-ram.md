---
title: "System RAM: HOME, current directory, data stack and flags (48SX, 48GX, 49G)"
type: hardware
models: [48sx, 48gx, 49g]
status: draft
sources: ["[[sources/hp48-internals-address-list]]", "[[sources/rplman]]", "[[sources/hpregint-fr]]"]
tags: [ram, rpl, directories, stack, flags, memory]
---

# System RAM: HOME, current directory, data stack and flags

Where the ROM keeps the pointers a host needs to read the user's memory
without the Kermit server: the HOME directory, the current (context)
directory, the data stack and the flag words, and the layouts behind them.
Used by saturnus's memory view (saturnus iteration 12a,
`saturnus-objects::ram`). The 38G, 39G and 40G keep aplets, not a HOME
tree, and are not covered.

## Pointers per model

Each pointer entry is a 5-nibble address, low nibble first. Flag words are
16 nibbles, flag -1 (or 1) in bit 0 of the first nibble.

| | 48SX (ROM J) | 48GX (ROM R) | 49G (ROM 2.10) |
| --- | --- | --- | --- |
| HOME directory object | #70592 | #80711 | #80711 |
| End of HOME (first nibble after it) | #70597 | #80716 | #80716 |
| Current directory object | #7059C | #8071B | #8071B |
| Saved D1: level 1's stack entry | #70579 | #806F8 | #806F8 |
| End of the stack (after its 0 marker) | #7057E | #806FD | #806FD |
| System flags | #706C5 (-1..-64) | #80843 (-1..-64) | #80F02 (-1..-64), #80F12 (-65..-128) |
| User flags | #706D5 (1..64) | #80853 (1..64) | #80F22 (1..64), #80F32 (65..128) |

- 48SX: every address is in the 1991 address list for ROM E
  ([[sources/hp48-internals-address-list]]: `homedir`, `end_homedir`,
  `cur_dir`, `TOS`, `EOS`, `System_flags`, `User_flags`) and holds on
  ROM J (observed in saturnus, see below).
- 48GX and 49G: no document found; found in saturnus by scanning the first
  4 KB of RAM for 5-nibble values that point at a directory object
  (prolog #02A96) after `'DA' CRDIR DA`, by diffing RAM across
  `-40 CF 7 CF -21 CF 21 CF` and `-40 SF 7 SF -21 SF 21 SF`, and by
  following the stack pointer to a known string on level 1. The pointers
  sit at the 48SX address plus #1017F, the offset Duchesne gives for other
  system RAM between the S and the G ([[sources/hpregint-fr]]: #7045C
  and #805DB, #704C3 and #80642); the 48GX flags sit at plus #1017E, so the
  offset is not uniform (observed).
- Saved D1 confirmed by tracing the ROM's wake-up after a key on the 48SX
  and the 49G: at #067F1-#067F4 it points D0 at #70579 (#806F8 on the 49G),
  reads the 5-nibble value and loads it into D1; #70574 (#806F3) is read
  just before into C (saved B, the return stack pointer). Observed in
  saturnus, same code address on both ROMs.
- 49G `RCLF` returns four binary integers in the order system -1..-64,
  user 1..64, system -65..-128, user 65..128; they equal the four RAM words
  above (observed, ROM 2.10).

## Directories

RPLMAN calls a directory a "ramrompair" (rrp): a body holding a linked
list of global variables and an attached library id; the current
("context") directory and the "stopsign" are system RAM locations; HOME
("sysramrompair") is never unrooted and "contains additional structure
that ordinary directories don't have (such as multiple library
attachments and alternate message and command hash tables)"; a hidden,
null-named directory at the beginning of HOME holds the user key
definitions and alarms (src: [[sources/rplman]] 19.2-19.3, p. 90-92,
SDK copy `raw/saturn-hardware/hp48-sdk-1993/RPLMAN.TXT` lines 6141-6250).

Layout, checked on the three models in saturnus by walking HOME's
records to exactly the end-of-HOME pointer and comparing every variable's
name, type, size and checksum with the Kermit server's `G D` listing:

- Ordinary directory: prolog #02A96, 3 nibbles attached library (#7FF =
  none), 5 nibbles offset from that field to the newest variable's name
  length field (0 = empty), then the records. As in a transfer file
  ([[protocols/hp-object-format]]).
- HOME: prolog, 3 nibbles count N of attached libraries, N entries of 13
  nibbles, then the same offset field and records. Read as library id (3
  nibbles) and two 5-nibble addresses per entry (inferred from the
  values: #002 with #22647 and #653D8, #700 with #22DFE and 0 on the
  48SX): the library's hash table (or the indirection to it) and its
  message table, as the library header gives them (observed 2026-10-05,
  [[protocols/rpl-libraries]]).
  N is 2 on the 48SX, 3 on the 48GX and 10 on the 49G after a memory clear
  (observed).
- Record: 5 nibbles offset from this field back to the previous record's
  name length field (0 in the first record), 2 nibbles name length n, n
  characters (2 nibbles each), n again (absent when n = 0), the object (or
  a 5-nibble ROM pointer). Records lie oldest first, so a new variable
  goes at the end; `VARS` and `G D` list newest first (observed: the
  offset chain and the listing order agree).
- HOME's first record is the hidden directory (n = 0, holding `UserKeys`,
  `UserKeys.CRC` and `Alarms` on the 48SX); it does not appear in `VARS`.
- HOME lies at the top of RAM on all three (48SX #7FE9B..#7FFFB with a
  few variables); it grows downwards as variables are added.

## BYTES and G D

- Checksum: the hardware CRC ([[hardware/crc]]) over the object's nibbles
  from its prolog; the variable's name is not included. Matches `BYTES`
  and `G D` for reals, strings, lists, programs and directories (observed
  on the 48SX; the three models in the saturnus test).
- Size: the whole record in nibbles divided by 2, i.e. object + 2n + 9
  nibbles (back offset 5, two length fields 4, the name 2n). `'X' BYTES`
  of a real in `X` is 16 (21 + 2 + 9 = 32 nibbles); a 2-character name
  with the 14-nibble string `"HI"` gives 13.5 (observed, 48SX).

## Data stack

- Saved D1 points at level 1's entry; each entry is a 5-nibble pointer to
  the object (in TEMPOB, in a variable, or in ROM: typed `1 2 3` on the
  48SX gives ROM pointers #2A2C9, #2A2DE, #2A2F3); entries continue to a 0
  entry, and the end pointer points just after it. Depth = (end - D1) / 5
  - 1 (observed on all three).
- Valid while the ROM is idle in its outer loop. While the Kermit server
  waits for a packet the saved D1 is the server's own stack (on the 49G a
  system binary on top of the user's levels; DEPTH there reads 0 for the
  keyboard's levels, see below).
- 49G, algebraic mode (flag -95 set, the default after a memory clear):
  after three entries on the keyboard the saved stack held six entries,
  each result followed by a tagged object with an empty tag around the
  compiled entry (`:: « 33 »`), inferred to be the algebraic history; a
  server started then reported an empty stack. A server entered from
  algebraic mode leaves, after FINISH, its stack packed into one list
  followed by the tagged command line `SERVER`. In RPN mode the saved
  stack and the server's stack agree level for level (saturnus test).

## Flags

- System flags -5..-10 hold the binary word size minus 1 (64: all set;
  `32 STWS`: 31), -11 and -12 the base: both clear DEC, -11 set OCT,
  -12 set BIN, both set HEX (observed, 48SX ROM J, `HEX`, `DEC`, `OCT`,
  `BIN`, `32 STWS`).
- 48SX after a memory clear: system word `0F30000000000000` in memory
  order (word size 64, DEC).

## Open

- Other ROM revisions (48SX A-E, 48GX K-P, 49G 1.19-6 and 2.15) are not
  checked; the 1991 list says the 48SX addresses held on ROM E.
- Ports and libraries (the end-of-HOME pointer is followed by 5 zero
  nibbles at the top of RAM; the 1991 list guesses "for ATTACH").
- Whether the stopsign pointer is next to the context pointer (#705A1
  on the 48SX equals HOME after a memory clear, `tmpdir` in the 1991
  list).

## Menu

The menu the soft keys show (observed 2026-10-06 in saturnus by diffing
RAM after menu keys and `n MENU`, iteration 13b): a 5-nibble pointer at
#7061E on the 48SX to the menu's definition itself, at #8079D on the
48GX and #807ED on the 49G to an XLIB name of the menu library's entry
(XLIB 169 n, n the menu number); the previous menu's pointer follows
(48SX #70623, 49G #807F2). The menu number of the last `n MENU` is a
pointer to a real at #705BA (48SX) and #80739 (48GX); key presses do not
update those. An input form shows its own menu (on the 48GX an entry of
library #B3), not one of the numbered menus. See
[[protocols/rpl-libraries]], "Built-in menus".
