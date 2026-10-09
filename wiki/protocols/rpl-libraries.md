---
title: "RPL libraries in ROM: headers, hash and link tables, command names (48SX, 48GX, 49G)"
type: rom-behaviour
status: draft
sources: ["[[sources/rplman]]", "[[sources/hp48-sdk-makerom]]"]
tags: [rpl, library, xlib, decompiler, rom]
models:
  - 48sx
  - 48gx
  - 49g
---

# RPL libraries in ROM

How the ROM finds a command's name, which is what a decompiler needs to
turn the 5-nibble ROM pointers inside a program into text. The documents
give the principle; the layouts below were observed in the 48SX ROM J,
48GX ROM R and 49G ROM (2009 image, `rom.49g`) in saturnus on 2026-10-05
by reading the ROM images and comparing with the names the calculator
itself prints. Nibble order as on [[protocols/hp-object-format]]: fields
low nibble first; "self-relative offset" means the target is the
field's own address plus the 5-nibble value, modulo #100000.

## What the documents say

- A command is a procedure object in a library together with its name;
  the parser compiles a pointer to it when the library is in the
  calculator's permanent ROM, otherwise an XLIB name (library number and
  command number, 12 bits each) (src: [[sources/rplman]] p. 13, p. 19).
- Built-in command objects are **preceded in ROM by a 6-nibble field that
  is the body of an XLIB name**. The decompiler, meeting an object
  pointer, looks for that field ahead of the object; if it is valid, it
  uses it to find the command's name, otherwise it decompiles the object
  itself (src: [[sources/rplman]] p. 13). Example (observed): on the
  48SX ROM J, DUP's object is at address #1FB87 and the six nibbles
  before it are `200D01`, library 2, command #10D = 269.
- A library holds a hash table of names, a link table of execution
  addresses in command-number order, a message table and a configuration
  routine; unnamed (`NULLNAME`) routines have a link entry but no name
  (src: [[sources/hp48-sdk-makerom]] p. 1-2).
- Commands are programs whose first object is one of the dispatchers CK0,
  CK1&Dispatch ... CK5&Dispatch, the number being the argument count (src:
  [[sources/rplman]] p. 13).

## Library header (observed)

Every library, built-in or not, starts with a 23-nibble header:

| Nibbles | Field |
| --- | --- |
| 3 | library number |
| 5 | self-relative offset to the hash table (0: none) |
| 5 | self-relative offset to the message table (0: none) |
| 5 | self-relative offset to the link table (0: none) |
| 5 | self-relative offset to the configuration object |

Seen: library 2 at #189EA and library #700 at #22DE7 on both 48s;
library #0AB (the 48GX's added commands) at #C0007 on the 48GX; on the
49G library 2 at #38DC3 and #700 at #37E62 in the low window (flash bank
1), and the other built-in libraries in banks 3-8.

- **Hash table**: either the table itself, a hex string (prolog #02A4E),
  or, for libraries 2 and #700, an indirection to it: a system binary
  (#02911) holding the table's address on the 48SX, an extended pointer
  (#02BAA: address, then a second field naming the code that maps the
  flash bank) on the 48GX and 49G. On the 49G the address is in the
  window from #40000 to #7FFFF of some flash bank; the bank is the one whose
  nibbles at that offset hold a valid hash table (in `rom.49g`: bank 2
  for library 2, the table at bank offset #20E2).
- **Link table**: a hex string; after its length field, one 5-nibble
  self-relative offset per command number to the command's object.
  Library 2 has 385 entries on both 48s and 401 on the 49G; #700 has 29
  (48) and 35 (49G).
- Every link target examined in libraries 2 and #700 is preceded by the
  6-nibble XLIB body (library, command) that RPLMAN describes; in the 49G's
  flash libraries many are not (the prefix is only needed for objects a
  user program can point at).

## Hash table layout (observed)

The hex string's body:

1. 16 fields of 5 nibbles, one per name length 1-16: self-relative
   offset to the first name of that length, 0 when there is none.
2. 5 nibbles: self-relative offset to the **number table** (below).
3. The names, grouped by length: each is a 2-nibble length, the
   characters (HP character set, one byte each), and the 3-nibble command
   number.
4. The number table, to the end of the string: one 5-nibble field per
   command number from 0, holding the distance **back** from the field to
   that command's name entry (field address minus value); 0 for a command
   without a name. It can be shorter than the link table: commands past
   its end have no name.

Several numbers can share a name: on the 48SX library 2, commands 68 and
69 both point at the entry `+` (whose own number field says 68), and the
calculator prints `+` for both. The structure words are library #700:
`IF THEN ELSE END WHILE REPEAT DO UNTIL START FOR NEXT STEP IFERR CASE`,
`→`, `«`, `»` (two each for `→`, `»`; three each for THEN and END), the
quote `'` (twice: opening and closing), and `HALT`, `PROMPT`, `DIR`.

Counts in the three ROMs (libraries found by scanning the image for
headers whose hash and link tables are valid):

| ROM | Libraries | Names |
| --- | --- | --- |
| 48SX J | 2, #700 | 382 + 28 = 410 |
| 48GX R | 2, #700, #0AB and a few without names | 539 |
| 49G (2009) | 2, 9, #700, #0AB, #0DD, #0DE, #0E3, #0E5, #0F1, #0FF, #100, #101, #314 and ~40 without names | 852 |

## How a program shows a command

- A user program `« 1 2 + »` is, after its prolog, the pointers to the
  commands `«` (library #700), the ROM reals 1 and 2, `+`, `»`, then SEMI.
  The calculator's text is the elements' texts joined by spaces.
- `→ a b « a b + »` is inline: `→`, the local names, `«` ... `»` (the
  second `»` of #700), then the program's own `»`.
- A quoted name `'X'` in a program is the opening quote command, the
  name, the closing quote command; shown without spaces.
- 'F(A,B)' (a user function): the arguments, an algebraic holding just
  `F`, a system binary with the argument count (a ROM pointer to the
  constant), and a ROM pointer to the code that applies it.

- A program literal inside a program (`« « 1 » EVAL »`) is an unnamed
  command of library #700 (number 15 on the 48SX) followed by the
  embedded program; the calculator shows nothing for that command. A
  CASE clause is an embedded program without `«` and `»`, shown as its
  elements only.
- `'X|(X=2)'` (where): the expression and each name and value as
  algebraics or numbers, a system binary with the count of all of them
  (3 here), then `|`.
- A 49G symbolic matrix (prolog #02686) is a composite of its elements; a
  matrix is a composite of such rows.

## Algebraics (observed)

An algebraic is its expression in postfix: operands, then the command.
The command's argument count comes from its dispatcher (below); `+ - * /
^`, the comparisons, `AND OR XOR`, `=` and `|` are infix, NEG and `√` are
prefix (`-A`, `√X`), `NOT` is prefix and parenthesises a compound
operand without a space (`NOT(A AND B)`), `!` is postfix, `∂` with two
operands shows `∂X(f)`, `Σ` with four shows `Σ(K=0,M,K)`, anything else
`F(a,b)`. Binding, tightest first: `√`, `^`, NEG, `* /`, `+ -`,
comparisons, `NOT`, `AND`, `OR XOR`, `=`, `|`. Parentheses appear where
the tree needs them (left operand of lower binding; right operand of
equal or lower binding), except that a signed right operand needs none
(`A^-B`, `√-X`). A where-expression is written bare as a left operand
(`X|(X=2)+1`, `X|(X=2)^2`) but parenthesised as a right operand, under a
prefix and as a function argument's whole (`1+(X|(X=2))`, `-(X|(X=2))`).
With flag -53 set, every compound operand is
parenthesised. Checked against the ROMs' own text for some 2000 objects
(saturnus iteration 12c).

## Unit operators (observed)

A unit object (prolog #02ADA) is the number, then the unit expression in
postfix: unit names as strings, prefixes as character objects, powers as
reals, and the operators as ROM pointers to five consecutive empty lists
(10 nibbles apart), in the order `*`, `/`, `^`, prefix, end. The end
marker is the last element of every unit. 48SX J and 48GX R: #10B5E to
#10B86; 49G: #2D74F to #2D777. `1_km` is the real, the character `k`, the
string `m`, the prefix marker, the end marker. The ROM's own unit table
holds about 200 unit objects (48GX, 49G), all ending in the end marker.

## Dispatchers and argument counts (observed)

The first object of most commands of library 2 is one of a few ROM
pointers; four of them are evenly spaced (48SX and 48GX: #18ECE, #18EDF,
#18EF0, #18F01; 49G: #26300, #26305, #2630A, #2630F) and hold the
one-argument (SIN, ASR), two-argument (`+`, `=`), three-argument
(IFTE, SUB) and four-argument (∫, Σ) commands: CK1&Dispatch to
CK4&Dispatch. The most common other first object (48: #18A1E, 49G:
#262B0) starts the commands without arguments (TIME, HOME): CK0.

## Built-in menus (observed 2026-10-05 and 2026-10-06)

Observed in the 48SX ROM J, 48GX ROM R and 49G (`rom.49g`) images in
saturnus (iterations 12c and 13b), by reading `MENU`'s code and the
objects it leads to, and by `n MENU` and key presses on the emulator.

- **Where the definitions are.** `MENU`'s own code names them, within a
  few calls: on the 48SX it pushes a list (at address #3B234 on ROM J)
  whose element n is menu n's definition (an embedded object, or a
  5-nibble pointer where it lies elsewhere); on the 48GX and 49G it pushes
  the system binary #A9, the number of a library without command names
  whose command n is menu n (XLIB 169 n): 118 commands on the 48GX, 437
  on the 49G. Menu 0 is the last menu; `n MENU` past the last number
  selects nothing new.
- **Definitions.** A list of keys (48SX), an array of XLIB names, one per
  key (prolog of the elements #02E92; 48GX and 49G), a program that pushes
  the list (MTH, PRG), or an XLIB name of another library's command that
  is one of these (48GX: library #A7 for MTH, PRG, UNITS). A program
  without a list builds the menu at run time (VAR, CST, LIBRARY).
- **Keys.** A command (the label is its name), or a `{ label action }`
  list: the label is a string, a program that draws one, or a command; the
  action is a command, a program, or, for a key with shifted variants, a
  list of actions. On the 48GX and 49G such keys are the unnamed commands
  of library #A8 (48GX: 130), XLIB #A8 0 being the blank key; on the 49G
  also entries of #A9 past the menus (its 437 commands hold menu keys and
  unit and help strings too). A key that leads to a submenu has an action
  that holds the submenu: on the 48SX a pointer to its definition, on the
  48GX and 49G a program around XLIB #A9 n. Unit menus are lists of unit
  name strings.
- **Which menus the keys open.** The keyboard's menu keys (MTH, PRG, VAR,
  CST, and shifted ones such as PRINT, MODES, TIME on the 48SX) set the
  current menu ([[hardware/hp48-system-ram]], "Menu"). On the 48GX many
  shifted keys open an input form or a choose box instead (TIME, MODES,
  SOLVE, STAT, I/O): their menus have no key of their own. On the 49G
  the keys open choose boxes unless flag -117 is set; with it set they
  open soft menus (MTH, PRG, SYMB, CONVERT, ARITH, CALC, ...).
- **Coverage.** Commands offered in some menu: 48SX 334 of 397, 48GX 402
  of 517, 49G 566 of 830. The rest are keyboard functions (`+`, `SIN`,
  `STO`), plot and statistics parameters set through input forms, and on
  the 49G most of the CAS's and the development libraries' commands.

## Related

- [[protocols/hp-object-format]] for the object bodies.
- [[hardware/hp48-system-ram]]: HOME lists the attached libraries with a
  13-nibble entry each: library number, the address of its hash table
  (or of the indirection to it), the address of its message table.
