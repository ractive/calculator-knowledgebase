---
title: "HP 38G"
type: hardware
models: [38g]
status: draft
sources:
  - "[[sources/hpj-38g]]"
  - "[[sources/hp38g-ug]]"
  - "[[sources/finseth-hp38g]]"
  - "[[sources/giesselink-emu48-25-years]]"
  - "[[sources/hpcalc-rom-listings]]"
  - "[[sources/kml20]]"
  - "[[sources/mastracci-saturn-guide]]"
  - "[[sources/saturn-tutorial]]"
  - "[[sources/connectivity-kit]]"
  - "[[sources/emu48-manual]]"
tags: [38g, model]
---

# HP 38G

A 48G with a different case, keyboard overlay and ROM, and its RAM moved to
the top of the address space. Code name "Elsie" (src:
[[sources/finseth-hp38g]]).

## Summary

| Item | Value |
| --- | --- |
| Released | 1995 (date conflict, see Contradictions) |
| CPU | Saturn 1LT8 in a Yorke, HP part 00048-80063, the 48G/GX part (src: [[sources/mastracci-saturn-guide]] 1.5; [[sources/finseth-hp38g]]) |
| Clock | 4 MHz (src: [[sources/finseth-hp38g]]); the HP Journal says "the same CPU" as the 48G (src: [[sources/hpj-38g]] art. 6 p. 1) |
| ROM | 512 KB, one-time-programmable, labelled "ELSIE OTP Rev 1.67" (src: [[sources/hp38g-ug]] 9-8; [[sources/hpj-38g]] art. 6 p. 1; [[sources/finseth-hp38g]]) |
| RAM | 32 KB at #F0000-#FFFFF, about 22 KB free for the user (src: [[sources/finseth-hp38g]], Mueller; [[sources/hp38g-ug]] 9-8) |
| Display | 131 x 64, same controller as the 48 (src: [[sources/hpj-38g]] art. 6 p. 1; [[sources/saturn-tutorial]] p. 165) |
| Keyboard | 47 keys on the 48 matrix, two 48 positions empty (below) |
| Serial | RS-232 link, "4-wire serial"; 10-pin connector that also carries the overhead-display output (src: [[sources/hpj-38g]] art. 6 p. 1; [[sources/finseth-hp38g]]) |
| IR | two-way IR, printer and calculator to calculator (src: [[sources/hpj-38g]] art. 6 p. 1) |
| Card ports | none on the case (inference: neither the HP Journal nor the user's guide mentions cards); Mueller mentions a card connector on the board (src: [[sources/finseth-hp38g]]) |
| Power | 3 AAA cells, 4.5 V, 60 mA max (src: [[sources/hp38g-ug]] 9-7) |
| Emu48 model | `A`; `6` for the 64 KB RAM variant with Detlef Müller's ROM (src: [[sources/kml20]] Global; [[sources/giesselink-emu48-25-years]]) |

## Memory map

- **RAM at #F0000-#FFFFF.** Detlef Mueller: "the build-in 32k memory is
  mapped to address F0000-FFFFF, a bigger chip would just do nothing and
  cause trouble if the RAM is mapped away temporarily to access the
  underlaying ROM" (src: [[sources/finseth-hp38g]]). 32 KB is #10000
  nibbles, so the window is exactly #F0000-#FFFFF.
- The display ghost registers sit at #F062C-#F0639 on the 38G
  against #8068D on the 48G (src: [[sources/saturn-tutorial]] p. 166;
  [[hardware/display]]), which agrees with RAM at #F0000.
- The 512 KB ROM is #100000 nibbles and fills the whole 20-bit address
  space, so the RAM covers the ROM's top 64 K nibbles, and the OS must
  unconfigure or move the RAM to read that part of the ROM (inference from
  Mueller's remark; compare the 48SX, whose RAM at #70000 also covers ROM,
  [[hardware/memory-controller]] "Default maps").
- Gießelink: the 38G is 48G hardware "with one important difference not
  allowing to run the HP-38G ROM image on an unmodified Emu48" (src:
  [[sources/giesselink-emu48-25-years]]). The page does not name it; the RAM
  address is the likely candidate (inference, unverified).
- **Not found:** which controller selects the RAM (NCE2 as on the 48G is the
  natural guess), whether CE1, CE2 and NCE3 are wired to anything, how the
  ROM uses DA19 (#129 bit 3), and what #11F reads.
  Filed as [[questions/hp38g-memory-controllers]]. An emulator can let the
  ROM's own CONFIG sequence show the answer: hang 32 KB of RAM on NCE2,
  the ROM on NCE1, nothing on CE1/CE2/NCE3, and log where the ROM puts each
  controller.

## Keyboard

The 38G keeps the 48's matrix (src: [[sources/kml20]] OutIn codes
HP48SX/HP48GX/HP38G). The HP 38G case is the 48 case with two keys
removed (src: [[sources/hpj-38g]] art. 6 p. 1); the keyboard figure shows
the gaps in the second row, at the 48's VAR and NXT positions (src:
[[sources/hp38g-ug]] inside cover).

The table below puts each 38G key at the matrix position of the 48 key in
the same place. This is an **inference**: the KML document gives the codes
only by 48 key position and says the table applies to the 38G; no source
lists 38G key names against codes. The reset chords support it: ON plus the
third menu key resets and ON plus the first and last menu keys clears
memory (src: [[sources/hp38g-ug]] 9-8), the same positions as the 48's
ON-C and ON-A-F.

OUT is the mask written by OUT, IN the mask read by A=IN/C=IN; ON is IN
#8000 for any OUT.

| Row | 38G key (alpha letter) | 48 key at that place | OUT | IN |
| --- | --- | --- | --- | --- |
| 1 | menu key 1 | A (softkey 1) | #002 | #10 |
| 1 | menu key 2 | B | #100 | #10 |
| 1 | menu key 3 | C | #100 | #08 |
| 1 | menu key 4 | D | #100 | #04 |
| 1 | menu key 5 | E | #100 | #02 |
| 1 | menu key 6 | F | #100 | #01 |
| 2 | PLOT | MTH | #004 | #10 |
| 2 | SYMB | PRG | #080 | #10 |
| 2 | NUM | CST | #080 | #08 |
| 2 | (no key) | VAR | #080 | #04 |
| 2 | up | up | #080 | #02 |
| 2 | (no key) | NXT | #080 | #01 |
| 3 | LIB | ' (quote) | #001 | #10 |
| 3 | VAR | STO | #040 | #10 |
| 3 | MATH | EVAL | #040 | #08 |
| 3 | left | left | #040 | #04 |
| 3 | down | down | #040 | #02 |
| 3 | right | right | #040 | #01 |
| 4 | HOME (A) | SIN | #008 | #10 |
| 4 | SIN (B) | COS | #020 | #10 |
| 4 | COS (C) | TAN | #020 | #08 |
| 4 | TAN (D) | square root | #020 | #04 |
| 4 | X,T,θ (E) | y^x | #020 | #02 |
| 4 | square root (F) | 1/x | #020 | #01 |
| 5 | ENTER | ENTER | #010 | #10 |
| 5 | ( (G) | +/- | #010 | #08 |
| 5 | ) (H) | EEX | #010 | #04 |
| 5 | negate (I) | DEL | #010 | #02 |
| 5 | x^y (J) | backspace | #010 | #01 |
| 6 | A...Z (alpha shift) | alpha | #008 | #20 |
| 6 | 7 (K), 8 (L), 9 (M), divide (N) | 7, 8, 9, divide | #008 | #08, #04, #02, #01 |
| 7 | shift | left shift | #004 | #20 |
| 7 | 4 (O), 5 (P), 6 (Q), multiply (R) | 4, 5, 6, multiply | #004 | #08, #04, #02, #01 |
| 8 | DEL | right shift | #002 | #20 |
| 8 | 1 (S), 2 (T), 3 (U), minus (V) | 1, 2, 3, minus | #002 | #08, #04, #02, #01 |
| 9 | ON | ON | any | #8000 |
| 9 | 0 (W), point (X), comma (Y), plus (Z) | 0, point, SPC, plus | #001 | #08, #04, #02, #01 |

(Codes: [[sources/kml20]] OutIn codes HP48SX, converted from "OUT bit, IN
value" to masks; 38G key names and alpha letters: [[sources/hp38g-ug]]
inside cover and 1-2.) The 48 key names follow the KML's letter order (A-F
softkeys, G-L the second row, and so on); see [[hardware/keyboard]] for
the 48 table from the primary sources.

## Display

Same controller and 131 x 64 LCD as the 48 (src: [[sources/hpj-38g]] art.
6 p. 1). Contrast in Emu48: reset value 14, keyboard range 9-24, the 48GX
values (src: [[sources/kml20]] LCD). ON plus a second key changes it (src:
[[sources/hp38g-ug]] 1-6). Power-on contrast 14, observed on ROM A after a
cold start (observed in saturnus 2026-10-07; saturnus renders it at about 90 % darkness).

## Serial, IR and PC link

- RS-232 and two-way IR as on the 48G (src: [[sources/hpj-38g]] art. 6
  p. 1). The 10-pin connector also carries the overhead-display signals
  (src: [[sources/finseth-hp38g]]); the 48's calculator-end cable adaptor
  must not be used with a 38G (src: [[sources/connectivity-kit]]).
- Every PC transfer is started from the calculator: SEND offers "another HP
  38G" or "a disk drive (or a computer)", RECEIVE lists the aplets in the
  remote directory (src: [[sources/hp38g-ug]] 1-26, 1-27;
  [[sources/connectivity-kit]]). The calculator is the client of a server
  on the PC, unlike the 48, where the PC drives the calculator's server.
- The wire protocol is not named in any source read; Kermit as on the 48G
  is the likely answer (unverified):
  [[questions/hp38g-39g-transfer-protocol]].

## First boot

- At first power-on the built-in aplets (function, parametric, polar,
  sequence, statistics, solve) are present and empty (src:
  [[sources/hpj-38g]] art. 6 p. 2).
- Warm reset: ON plus the third menu key, or the reset hole; memory clear:
  ON plus the first and last menu keys (src: [[sources/hp38g-ug]] 9-8).
- What the ROM shows on a cold start with blank RAM (a "Memory Clear"
  message as on the 48G, or straight to HOME) is not documented: observe it
  when booting the ROM.

## ROM image

- hpcalc.org offers "HP 38G Revision A ROM", `38grom.zip` (323,277 bytes,
  <https://www.hpcalc.org/details/4775>), containing `38G_A167.ROM`, 524,288
  bytes, i.e. 512 KB packed two nibbles per byte (src:
  [[sources/hpcalc-rom-listings]]). Version A1.67 matches the "Rev 1.67" chip
  label (src: [[sources/finseth-hp38g]]).
- Permission: unlike the 48 ROM pages, the 38G page has no "HP graciously
  began allowing this to be downloaded" statement. The Emu48 manual says the
  38/39/40/48/49 ROMs have been "freely available" since fall 2000 but that
  there is no distribution licence (src: [[sources/hpcalc-rom-listings]];
  [[sources/emu48-manual]] 3). Treat it like the 48/49 ROMs: the user
  fetches it, the project never ships it.
- Uploaded dumps carry the I/O register window over the ROM; Emu48's
  Convert zeroes it (src: [[sources/emu48-manual]] 3). Whether the hpcalc
  image is already clean is unknown; check the bytes at #00100-#0013F
  after download.

## Contradictions

- Release date: "09/??/95" (src: [[sources/mastracci-saturn-guide]] 1.5)
  against 1995-04-06 (src: [[sources/finseth-hp38g]]). Irrelevant to
  emulation; see [[questions/hp38g-release-date]].

## Facts settled while building saturnus (2026-10-05)

Observed by booting ROM A1.67 on the clean-room saturnus emulator. There
is no oracle, so this is not checked against hardware or another emulator.

- **The image.** The hpcalc.org file is packed two nibbles per byte, like
  the 48GX image, and its I/O window (#00100-#0013F) is already zeroed.
  SHA-256:
  `3c9f747f637757d3adc414ed14d7f3636033f34f0a72e6e453ee197987f16be7`.
- **Wiring that boots.** The ROM boots on a 48G-style wiring: Yorke at
  4 MHz, ROM on NCE1 with DA19, 32 KB RAM on NCE2, the CE1 bank latch,
  and nothing on CE2 or NCE3. The CONFIG sequence is on
  [[questions/hp38g-memory-controllers]]; the ROM itself places the RAM
  at #F0000.
- **Cold start.** With blank RAM the ROM shows a "Memory Clear" box with
  an OK softkey (menu key 6). OK leads to the HOME history view. There is
  no "Try To Recover Memory?" prompt.
- **Keys.** The algebraic entry `6 * 7 ENTER`, typed with the 48 keys at
  those matrix positions, shows `6*7` and 42 in the history. This confirms
  the 7, 6, multiply and ENTER rows of the keyboard table above. RPN-style
  `6 ENTER 7 * ENTER` gives "Invalid Syntax".
- **After boot.** At HOME the contrast register holds 14, the Emu48 KML
  value for the 38G. #11F holds #F.

## Facts settled while building saturnus (2026-10-05, keys and link)

Also observed on saturnus with ROM A1.67, no oracle:

- **Letters.** A...Z then a key types that key's letter from the table
  above; all 26 letters A-Z check out. SHIFT then A...Z then a key types
  the lowercase letter. A second A...Z cancels the first instead of
  locking alpha, so there is no ALPHA ALPHA lock as on the 48.
- **Space.** SHIFT then 2 types a space on the edit line, also between
  letters (A...Z HOME, SHIFT 2, A...Z SIN shows "A B"); SPACE is printed
  above the 2 key on the unit (@ractive's photographs of a 38G, which
  also confirm every shifted label and alpha letter of the table above).
  There is no alpha-mode space as on the 39G.
- **PC link.** SEND to a disk drive (LIB, SEND, second entry) speaks
  Kermit with the calculator as the client: see
  [[questions/hp38g-39g-transfer-protocol]].
