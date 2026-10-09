---
title: "HP object and transfer file formats (binary HPHP48-x, ASCII %%HP)"
type: protocol
status: draft
sources: ["[[sources/hp48-faq]]", "[[sources/rplman]]", "[[sources/hp48-kermit-hints]]", "[[sources/conn4x-ymodem-pas]]", "[[sources/checksum-crc]]", "[[sources/saturn-tutorial]]"]
tags: [object-format, binary, ascii, transfer, hptx]
---

# HP object and transfer file formats

What a host receives when the calculator sends a variable, and what it must
send back. Transport (Kermit, XModem) is on [[protocols/kermit-hp]] and
[[protocols/xmodem-hp]].

## Objects in memory

- Every object starts with a 5-nibble prolog identifying its type; RPLMAN
  (src: [[sources/rplman]]) is HP's reference for all prologs (src:
  [[sources/hp48-faq]] 8.18). Examples: GROB #02B1E (FAQ 8.18),
  string #02A2C (src: [[sources/saturn-tutorial]] p. 81), library #02B40
  (src: [[sources/checksum-crc]], written "04B20" in memory order).
- Every multi-nibble field is stored low nibble first, so the GROB
  prolog #02B1E appears in memory as E1B20 (FAQ 8.18).
- On a byte-oriented host, nibbles pack two per byte, low nibble first: the
  first nibble in memory is the low half of the first byte, so #02B1E
  followed by more data dumps as `1E 2B x0 ...`, where x is the next field's
  first nibble (FAQ 8.18).

### GROB layout (worked example of the general rule)

prolog #02B1E (5), length (5), height (5), width (5), body (src:
[[sources/hp48-faq]] 8.18).

- The length counts everything after the prolog: itself, height, width and
  body. Total object size = length + 5 nibbles.
- Height comes before width.
- Each row is padded to a whole number of bytes (an even number of nibbles).
  A 131-pixel row takes 34 nibbles; a 131x64 GROB has a 2176-nibble body,
  2196 nibbles total, length field #0088F.
- Within a nibble the least significant bit is the leftmost pixel.

## Object bodies

After the prolog (src: [[sources/rplman]] ch. 3, p. 18-26, unless noted;
lengths counted in nibbles, every field low nibble first):

- Real (#02933): 16 nibbles: 3-nibble exponent in tens complement
  (-500 < e < 500), 12 BCD mantissa digits from the least significant up
  (the most significant digit, which carries the implied decimal point, is
  last), then a sign nibble, 0 positive, 9 negative. So 8.72653549837E-3
  is `79973894535627800` after the prolog: exponent `799` = #997 = -3,
  digits 7 3 8 9 4 5 3 5 6 2 7 8 read backwards, sign 0 (checked on the
  48SX, saturnus 2026-10-05).
- Complex (#02977): two real bodies, real part first.
- String (#02A2C): 5-nibble length counting itself and the characters, then
  one byte (two nibbles, low first) per character in the HP character set.
- Binary integer (#02A4E): a hex string, 5-nibble length counting itself,
  then the value's nibbles, least significant first (16 for a user binary
  integer). The base (`h`, `d`, `o`, `b`) is a display mode, not part of
  the object (the 48SX sends `#FFh` as `# 255d` in ASCII in decimal mode).
- List (#02A74), algebraic (#02AB8), program (#02D9D), unit (#02ADA):
  elements, then SEMI (#0312B). A unit holds the number, the unit names as
  strings and the unit operators as ROM pointers, in postfix order.
- Global name (#02E48), local name (#02E6D): 2-nibble character count,
  then the characters.
- Tagged (#02AFC): 2-nibble tag length, the tag's characters, then the
  object.
- Array (#029E8): 5-nibble length (counting itself), 5-nibble element
  prolog, 5-nibble dimension count, 5 nibbles per dimension, then the
  elements' bodies without prologs. `[[ 1 2 ][ 3 4 ]]` is length #00059,
  type #02933, 2 dimensions of 2 and 2, four 16-nibble real bodies (48SX,
  saturnus 2026-10-05).
- 49G integer (#02614): 5-nibble length counting itself, then the decimal
  digits from the least significant up, one per nibble, then a sign
  nibble, 0 positive, 9 negative. 5 is `7000050` after the prolog,
  -1234567890123456789 is length #00019 then `98765432109876543219`; 0 is
  length 6 and a single 0 nibble (observed on the 49G ROM of 2009 (2.15) in
  saturnus, 2026-10-05; no document read).
- Built-in objects are shared: inside a list, program or unit a small
  constant is often a 5-nibble pointer into ROM instead of an embedded
  object. On the 48SX `{ 1 2. ... { 5 } }` holds #2A2C9, #2A2DE and #2A31D
  for 1, 2 and 5; on the 49G the integer 1 in a list is #273B6. Reading
  the pointed-to object from ROM decodes them (saturnus 2026-10-05).

## Binary sends to the calculator (saturnus, 2026-10-05)

- The ROM letter of the `HPHP48-x` / `HPHP49-x` header is not checked:
  `HPHP48-Z` is accepted by the 48SX ROM J and `HPHP49-Z` by the 49G.
- The 48SX stores the file as a string if one byte follows the object
  beyond the spare half byte; the 49G (ROM of 2009) accepts the same file as
  the object. Send exactly ceil(nibbles / 2) bytes after the header.
- A 48 header sent to the 49G (and a 49 header to the 48SX) makes a string.

## Binary transfer file: "HPHP48-x"

- A binary transfer file is the 8-byte ASCII string `HPHP48-x` followed by
  the object's nibbles packed low-nibble-first into bytes; x is a ROM
  revision letter, e.g. `HPHP48-E` (src: [[sources/hp48-faq]] 8.18). The ROM
  returns its own header string from `#30794h SYSEVAL` (3.4).
- The header bytes have the top bit clear ("8 byte ascii string with msb
  off", 8.18).
- If a binary file is transferred in text mode, CR/LF translation corrupts
  it; the calculator then shows a string starting "HPHP48-". FIXIT (Horn,
  Heiskanen) and HP's OBJFIX recover it; OBJFIX handles the case where extra
  bytes were appended at the end (src: [[sources/hp48-faq]] 6.11, 9.2). So
  receivers tolerate trailing garbage after the object, and length is taken
  from the object, not the file.
- On receive in binary mode the HP keeps the whole file as a string; at the
  end, if it starts with `HPHP48-x` and the rest is a valid object, the
  object is returned, otherwise the string (src: [[sources/hp48-kermit-hints]],
  Programs versus data).
- The HP49G uses `HPHP49-` plus a letter, e.g. `HPHP49-R`; the 8-byte header
  is followed by the prolog, length and data (src:
  [[sources/conn4x-ymodem-pas]] comment in its YModem sender).
- A host can find the true end of the object by walking it from nibble 16
  (just after the header) and truncate the file to ceil(nibbles / 2) bytes;
  Conn4x's object-fixing step does this to strip XModem padding (src:
  [[sources/conn4x-ymodem-pas]], FixObj.pas).

## ASCII transfer file: "%%HP:" header

- ASCII transfers begin with a header of the form `%%HP: T(3)A(D)F(.);` that
  records three settings: T translation mode, A angle mode (D, R, G), F
  fraction mark (. or ,) (src: [[sources/hp48-faq]] 6.12; the FAQ text spells
  it "%HPHP:", which is a typo, see [[questions/ascii-header-spelling]]).
- Translation modes (set with TRANSIO): 0 none; 1 LF to CR LF on send and
  back on receive; 2 also translate characters 128-159 to `\nnn` escapes;
  3 translate 128-255 (6.12).
- A file without a header is read with the calculator's current settings;
  a mismatched fraction mark turns `3,4` into a two-number System RPL
  program (6.12).
- The \->ASC / ASC\-> programs are a separate, older text encoding of binary
  objects with a checksum (7.6). Not a calculator built-in.
- An ASCII transfer of a string has two layers. The 49G's string syntax
  escapes `\"` and `\\`, and translation 2 or 3 then doubles each
  backslash, so one 49G backslash becomes four in T(3) text. The 48 has no
  escapes in strings and writes a string holding `"` as `C$ n` (hptx,
  2026-10-05).
- In ASCII mode a fresh 48SX did not keep bytes 0-26 of a 256-byte file
  through a round trip; all 256 survive in binary mode (hptx).

### ASCII transfer and stack display (saturnus, 2026-10-05)

The text of an ASCII transfer is the ROM's decompiler output, but not
exactly the one-line text the stack (and the Kermit server's reply to a
host command) shows. Observed on the 48SX ROM J, 48GX ROM R and 49G (2009
ROM) by comparing both for about 1100 objects (saturnus iteration 12c):

- The transfer breaks lines to keep them short and indents structures
  (`IF`, `THEN`, nested `«`) by two spaces; between tokens a break stands
  for one space, inside an algebraic it breaks anywhere and stands for
  nothing.
- A unit object is written quoted (`'1_m/s^2'`) so it reads back; the
  stack shows `1_m/s^2`.
- The transfer always uses the standard number format and the whole
  binary integer; the stack shows FIX, SCI and ENG, the fraction mark and
  the word size (`# FFFFh` at 16 bits for #FFFFFh).
- Stack display details the transfer cannot show: in FIX, a real that is a
  whole level has its integer digits grouped (`1,234.500`; `1.234,500`
  with the comma mark); the 49G groups reals inside lists, programs and
  arrays too, never inside complex numbers, algebraics or units. A tagged
  object that is a whole level shows as `T: 5`; inside a list the 48 shows
  `:T: 5` and the 49G `T: 5`. `STO` strips a tag, so a tagged variable
  cannot be transferred.
- The 49G's server reply carries only the first 20 characters of each
  level; the 48's carries the whole text.
- Inside a unit object the number and the powers follow the display mode
  unless they are integers: `2_m^2` and `1_cm^3` stay so in FIX and ENG,
  `1.5_m^2.5` shows `1.50E0_m^2.50E0` in 2 SCI (all three models).
- The 49G shows a real with an integer value with its point (`5.`, an
  exact integer is `5`), but not inside a unit (`1_m`); a name inside a
  symbolic matrix is quoted (`[ 'X' 'Y' ]`). `^` groups to the right on
  the 49G (`A^B^C` is `A^(B^C)`) and to the left on the 48.

## Checksum

The BYTES command returns a 16-bit CRC of the object (src:
[[sources/hp48-faq]] 4.1), the same CRC the hardware computes
([[hardware/crc]]). A library's stored CRC excludes its 5-nibble prolog (src:
[[sources/checksum-crc]]).

## Observed on the saturnng emulator (2026-10-05)

From binary GETs (flag -35 set) on ROM J (48SX), ROM R (48GX) and ROM 2.15
(49G), not from a document:

- Headers sent: `HPHP48-J` (48SX), `HPHP48-R` (48GX), `HPHP49-C` (49G).
  The files carry no padding beyond the spare half byte of an odd nibble
  count, which was 0 in every file.
- The ASCII header is spelled `%%HP: T(1)A(D)F(.);` followed by CR LF
  (49G: `A(R)`), answering [[questions/ascii-header-spelling]].
- With translation mode 3 the 48SX writes characters 128-159 as two-letter
  trigraphs (`\<)` `\x-` `\.V` `\v/` `\.S` `\GS` `\|>` `\pi` `\.d` `\<=`
  `\>=` `\=/` `\Ga` `\->` `\<-` `\|v` `\|^` `\Gg` `\Gd` `\Ge` `\Gn` `\Gh`
  `\Gl` `\Gr` `\Gs` `\Gt` `\Gw` `\GD` `\PI` `\GW` `\[]` `\oo`), eight of
  160-255 as mnemonics (171 `\<<`, 176 `\^o`, 181 `\Gm`, 187 `\>>`, 215 `\.x`,
  216 `\O/`, 223 `\Gb`, 247 `\:-`) and the rest as `\nnn` in decimal.
- Directory (prolog #02A96) layout, checked by walking every recorded file
  to its exact size: prolog, 3 nibbles attached library (#7FF none), 5
  nibbles offset from that field to the last variable's name length (0 =
  empty), then per variable: 5 nibbles back-offset, 2 nibbles name length
  n, 2n nibbles name, n again (absent when n = 0: HOME's hidden entry has
  an empty name and holds a directory with `UserKeys`, `UserKeys.CRC`,
  `Alarms`), then the object.
- Inside lists, tagged objects and directories an element is either an
  object with a known prolog or a 5-nibble pointer into ROM: the real 5 in
  `{ :T:5 }` is the pointer #2A31D on the 48GX.
- On the 49G an integer typed as `3` is a 49G Integer (`G D` type
  `Integer`), not a real.
- `LCD\->` returns a 131x64 GROB (length field #0088F) on all three models.

## Character set

Bytes 0-127 are ASCII and 160-255 ISO 8859-1. 128-159 are, in order:
∡ x̄ ∇ √ ∫ Σ ▶ π ∂ ≤ ≥ ≠ α → ← ↓ ↑ γ δ ε η θ λ ρ σ τ ω Δ Π Ω ■ ∞ (checked by
hptx against a 48SX string holding every character from 128 to 255;
saturnus `charset.rs`). A host command takes these bytes, not the trigraphs
([[protocols/server-commands]]).

## Open

- Padding when the object has an odd number of nibbles:
  [[questions/binary-odd-nibble-padding]].
- Header and file format of 38G and 39G/40G aplets
  ([[questions/hp38g-39g-transfer-protocol]]).
