---
title: "Saturn CPU: registers, flags, instruction classes"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/saturn-tutorial]]", "[[sources/keyb49-sylvester]]", "[[sources/voyage-48gx]]"]
tags: [saturn, cpu, registers, instruction-set]
---

# Saturn CPU

The 4-bit (nibble) CPU at the core of every calculator in this wiki. Companion
pages: [[hardware/memory-controller]] (CONFIG and address space),
[[hardware/interrupts]], [[hardware/io-ram]].

## Data path and address space

- 4-bit data path, 20-bit addresses, 1 M nibbles (512 KB) logical address
  space; registers up to 64 bits (src: [[sources/mastracci-saturn-guide]] 2.1).
- Memory is addressed in nibbles. All addresses in this wiki are nibble
  addresses unless stated.
- Multi-nibble values are little-endian: the nibble at the lowest address goes
  to register nibble 0 (src: [[sources/saturn-tutorial]] p. 36). Immediate
  operands in opcodes are stored the same way, e.g. `D1= 00120` encodes as
  1F02100 (p. 76).
- Clock: derived from a 32 kHz crystal multiplied up; about 2 MHz on the
  48S/SX, about 4 MHz on the 48G/GX and 49G (src: [[sources/saturn-tutorial]]
  p. 43).

## Chip versions

| Chip | Used in | Companion IC |
| --- | --- | --- |
| 1LF2 | HP71B (early) | - |
| 1LK7 | HP71B (later), HP18C, HP28C | - |
| 1LT8 | HP17B, 19B, 27S, 28S | Lewis |
| 1LT8 | HP48SX, HP48S | Clarke |
| 1LT8 | HP48GX, HP48G, HP38G | Yorke |

(src: [[sources/mastracci-saturn-guide]] 1.5). On the 48S/SX the CPU is inside
the Clarke IC; on the 48G/GX inside the Yorke IC (2.1, 2.5).

## Registers

Nineteen registers (src: [[sources/mastracci-saturn-guide]] 2.2):

| Register | Width | Notes |
| --- | --- | --- |
| A, B, C, D | 64 bits (16 nibbles) | working registers, field-addressable |
| R0-R4 | 64 bits | scratch, little more than copy/exchange |
| D0, D1 | 20 bits | data pointers for memory access |
| RSTK | 8 levels x 20 bits | return stack; also used by RSTK=C / C=RSTK |
| P | 4 bits | pointer, selects a nibble for P and WP fields |
| PC | 20 bits | incremented after each instruction fetch |
| IN | 16 bits | read-only input port (keyboard columns) |
| OUT | 12 bits | write-only output port (keyboard rows, speaker) |
| CARRY | 1 bit | arithmetic overflow/borrow; true result of tests |
| ST | 16 bits | program status flags; low 12 accessible as a group |
| HST | 4 bits | hardware status: XM, SB, SR, MP (bits 0-3) |

- Because one RSTK level is needed by the interrupt system, code must leave a
  level free (2.2).
- OUT is write-only, so the CPU cannot save it on interrupt; the ROM keeps a
  shadow of OUT in RAM (2.3).
- The top four ST bits (12-15) are used by the operating system for interrupt
  state, the lower 12 by programs; C=ST, ST=C, CSTEX and CLRST touch only the
  lower 12 (2.2, 3.4). See [[hardware/interrupts]] for bits 12-15.

### RSTK behaviour

Eight 5-nibble levels, LIFO. A pop refills the bottom with zero; pushing a
ninth value drops the oldest, so after overflow the last eight pushes are
still readable followed by zeros (src: [[sources/saturn-tutorial]] p. 41).
The ROM interrupt handler uses two levels, sometimes three, so programs get
five (p. 41, 88).

### HST bits (LSB to MSB)

| Bit | Name | Set by |
| --- | --- | --- |
| 0 | XM, external module missing | RTNSXM; since RTNSXM encodes as 00, also a jump into nonexistent memory that reads as 0 |
| 1 | SB, sticky bit | a non-zero bit shifted off the right of a working register |
| 2 | SR, service request | a pending service request seen by SREQ? |
| 3 | MP, module pulled | *NINTX line pulled low, whether or not an interrupt runs |

(src: [[sources/sasm-reference]] 2.6 for the order; meanings also in
[[sources/mastracci-saturn-guide]] 2.2, which lists SR and SB the other way
round.)

The tutorial agrees, with mask encodings XM=0 821, SB=0 822, SR=0 824, MP=0
828 (src: [[sources/saturn-tutorial]] p. 43, 96). See
[[questions/hst-bit-order]] (answered).

HST bits are only set by events; test by clearing first. CLRHST (82F) clears
all four; HS=0 n (82n) clears by mask; ?HS=0 n (83n yy) tests by mask (3.4).

## Field selectors

| Field | Nibbles | Meaning |
| --- | --- | --- |
| P | P | nibble selected by P |
| WP | P..0 | word through pointer |
| XS | 2 | exponent sign |
| X | 2..0 | exponent with sign |
| S | 15 | mantissa sign |
| M | 14..3 | mantissa |
| B | 1..0 | byte |
| A | 4..0 | address (20 bits) |
| W | 15..0 | whole register |

(src: [[sources/mastracci-saturn-guide]] 3.1)

## Arithmetic mode

Only the register arithmetic instructions (R=R+1, R=R-1, R=R+R', R=R-R' and
the like) depend on the mode; there is no instruction to read the mode, the
usual test is to load 9 and add 1 on the P field and look at carry (src:
[[sources/voyage-48gx]] p. 132).

SETHEX (04) and SETDEC (05) switch between hexadecimal and BCD arithmetic; the
mode changes carry generation and arithmetic results (src:
[[sources/mastracci-saturn-guide]] 3.4).

## Instruction encoding and semantics

The full opcode list with cycle counts is in [[sources/saturn-tutorial]]
ch. 33-52 (p. 47-100). Facts an emulator needs beyond the table:

- Field-selector nibble: opcodes with a field use one of two 3-bit codes,
  "a" (P=0, WP=1, XS=2, X=3, S=4, M=5, B=6, W=7) or "b" (the same plus 8);
  the A field usually has its own short opcode (p. 50).
- Register-pair restrictions on the original Saturn: D only pairs with C; B
  with A or C; LA/LC only load A or C; only A and C exchange with R0-R4, D0,
  D1 and memory (p. 55, 72, 76).
- LA/LC load n nibbles starting at nibble P and wrap from nibble 15 to 0; they
  never touch carry (p. 48-49).
- Carry: arithmetic sets it on wrap within the field; tests set it when true
  and clear it when false; GOC/GONC do not change it (p. 41-42, 85, 89).
- Add/subtract constant (818 group) encodes c-1 in the last nibble, c = 1-16
  (p. 60).
- Add/subtract constant and DEC mode: the tutorial describes a DEC-mode
  bug on the S, XS, WP and P fields (p. 59-60). SASM lists these forms as
  always hexadecimal (see "Facts settled" below); a hex add that overruns
  from a single-nibble field circularly through the register reproduces
  the tutorial's examples. Open points: [[questions/dec-mode-constant-bug]].
- D0/D1 increment/decrement (n = 1-16), P=P+1/P=P-1 and C+P+1 always work in
  hex, whatever the mode, and set carry on wrap (p. 76, 93).
- D0=/D1= with 2 or 4 nibbles replace only the low 2 or 4 nibbles of the
  pointer; D0=AS etc. copy 4 nibbles (p. 75-77).
- Nibble shifts (ASL/ASR ...) do not touch carry but set SB if a non-zero
  nibble is shifted out; bit shift right (xSRB) sets SB if a 1 bit falls off
  (p. 69-70). The rotate instructions (xSLC/xSRC) are said to set SB in a
  case the text describes ambiguously (p. 70).
- Relative branch ranges: GOC/GONC and test GOYES use a signed 2-nibble
  offset; GOTO and GOSUB 3 nibbles; GOLONG and GOSUBL 4; GOVLNG and GOSBVL
  absolute 5 (p. 85-89). An offset of 00 after a test means RTNYES (p. 93).
- Return variants: RTN 01, RTNSC 02, RTNCC 03, RTNSXM 00, RTI 0F, RTNC 400,
  RTNNC 500 (p. 89).
- CLRST, C=ST, ST=C, CSTEX act on ST bits 0-11 only (p. 95).
- A=IN and C=IN only work at an even address; the ROM provides AINRTN and
  CINRTN for this (48: #0115A, #01160; 49: #0020A, #00212) (p. 94).
- Not on the 48/49G: multiply, divide, modulo, XOR, multi-bit shifts,
  user-defined fields F1-F7 and the 80Bxx system calls exist only on the
  ARM-based 49G+/48GII "Saturn+" (p. 40, 62-71, 101-102). A 48/49G emulator
  should treat them as undefined.

- Emu48 makes `r=r+CON`/`r=r-CON` always hexadecimal and lets single-nibble
  fields overrun (src: [[emulators/emu48]] CHANGES SP1, SP10), which differs
  from the tutorial's "DEC-mode bug" description:
  [[questions/dec-mode-constant-bug]].
- Rotates (xSLC/xSRC) update SB too (src: [[emulators/emu48]] CHANGES SP35).

### Timing

Cycle counts in the tutorial come from the Meta Kernel documentation and are
approximate. Some depend on whether the instruction sits at an odd or even
address: fractional counts round down at even and up at odd addresses (src:
[[sources/saturn-tutorial]] p. 44). Display refresh steals RAM cycles and
slows the CPU (p. 165); see [[hardware/display]].

#### Two cycle tables (2026-10-05)

- The SASM manual's counts (src: [[sources/sasm-reference]] 8) and the
  tutorial's Meta Kernel counts differ. The tutorial gives its counts for
  "the HP 48G, whose processor runs at about 4 MHz" (p. 44). They are
  higher, roughly 0.5 cycle per opcode nibble. Examples, SASM vs Meta
  Kernel: A=A+B A 7 vs 8; field forms 3+d vs 4.5+n; LC 3+n vs 3+1.5n;
  GOC/GONC 10/3 vs 12.5/4.5; GOTO 11 vs 14; GOVLNG 14 vs 18.5; RTN 9 vs
  11; ?A=B A 18/11 vs 21.5/13.5; A=DAT0 A 18 vs 23.5 plus 3.5 by the
  parity of the address read; DAT0=A A 17 vs 19.5 (ch. 33-52).
- The tutorial's rule for a second count after a comma ("23.5,3.5") is
  to add its floor when reading from an even address and its ceiling
  from an odd one (p. 44).
- Emu48 says the S/SX and G series counts differ ([[emulators/emu48]]
  SP1). The benchmark in [[questions/instruction-speed-vs-hardware]]
  supports SASM for the 48SX and the Meta Kernel counts for the Yorke.
  With those tables, the real machines are still 19-34% slower than the
  counts at 2 / 4 MHz plus the display stall. The cause is unknown.
- saturnus applies the gap as a calibration factor per model: 48SX 1.267,
  48GX 1.335, 49G 1.205; the 38G is taken as a 48GX and the 39G/40G as a
  49G (inferred, no benchmark). The factor scales instruction times only,
  not SHUTDN time, since the timers run on the crystal. The 42S runs at
  1 MHz with the SASM counts, factor 1 and no display stall (unverified;
  see [[questions/lewis-clock-and-rate]]) (saturnus decision log,
  iterations 7 and 15).
- The 48SX's CPU clock is multiplied from the 32 kHz crystal ("8-MHz CPU
  clock", src: [[sources/hpj-48sx]] p. 30; the usual 2 MHz is a quarter
  of that, inferred). The G series runs at a "4-MHz bus rate" (src:
  [[sources/hpj-48gx]] PDF p. 4).

## Chip-interface instructions

Encodings as given by Mastracci 3.4; the full opcode map is in
[[sources/saturn-tutorial]] and the HP SASM manual.

| Mnemonic | Opcode | Effect |
| --- | --- | --- |
| RTNSXM | 00 | return, set XM |
| SETHEX / SETDEC | 04 / 05 | arithmetic mode |
| RSTK=C | 06 | push C(A) |
| C=RSTK | 07 | pop into C(A) |
| CLRST | 08 | clear ST bits 0-11 |
| C=ST | 09 | C(X) = ST bits 0-11 |
| ST=C | 0A | ST bits 0-11 = C(X) |
| CSTEX | 0B | exchange ST bits 0-11 with C(X) |
| RTI | 0F | return from interrupt, re-enable interrupt detection |
| OUT=CS | 800 | OUT low nibble = C(0) |
| OUT=C | 801 | OUT = C(X) |
| A=IN | 802 | A(3:0) = IN |
| C=IN | 803 | C(3:0) = IN; only works from an even nibble address (ROM entry =CINRTN provides one) |
| UNCNFG | 804 | unconfigure module at C(A) |
| CONFIG | 805 | configure next module with C(A) |
| C=ID | 806 | C(A) = ID of next module to configure |
| SHUTDN | 807 | stop until wake-up |
| INTON | 8080 | enable maskable interrupts (4.9 ties this to the 1 ms keyboard scan) |
| RSI | 80810 | reset interrupt detection |
| BUSCB | 8083 | bus command B (obsolete on HP48) |
| PC=(A) | 808C | indirect jump through address at A(A) |
| BUSCD | 808D | bus command D |
| PC=(C) | 808E | indirect jump through address at C(A) |
| INTOFF | 808F | disable maskable interrupts / keyboard scan |
| RESET | 80A | unconfigure all modules |
| BUSCC | 80B | bus command C |
| SREQ? | 80E | C(0) = service request lines; sets SR if any |
| HS=0 n | 82n | clear HST bits by mask |
| ?HS=0 n | 83n yy | test HST by mask, branch offset yy |
| ST=0 n / ST=1 n | 84n / 85n | clear / set ST bit n |
| ?ST=0 n / ?ST=1 n | 86n yy / 87n yy | test ST bit n |
| NOP3 / NOP4 / NOP5 | 420 / 6300 / 64000 | 3-, 4- and 5-nibble no-ops |

SREQ? bit meanings: bit 0 display driver (timer), bit 1 HP-IL mailbox, bit 2
card reader, bit 3 unused (src: [[sources/mastracci-saturn-guide]] 3.4).

## Facts settled while building saturnus (2026-10-04)

Read from HP's own assembler manual (src: [[sources/sasm-reference]], plain
text `raw/saturn-hardware/hp48-sdk-1993/SASM.TXT`, sections 2.7, 6.5-6.6 and
8) while writing the CPU core; they override the tutorial where the two
differ.

- Always hexadecimal, whatever SETDEC says (2.7): P=P+1, P=P-1, C+P+1,
  D0=D0+/- n, D1=D1+/- n and all `r=r+CON` / `r=r-CON` forms. This settles
  the "always hex" half of [[questions/dec-mode-constant-bug]].
- `A=-A-1` (one's / nine's complement) always clears carry (8). The
  tutorial's "carry set when the field is zero" is wrong. `A=-A` sets carry
  if the field was non-zero (8).
- Sticky bit: ASL and ASLC leave SB alone; ASR and ASRC set SB when the
  nibble leaving the low end is non-zero; ASRB sets SB when the lost bit is
  1 (8). The tutorial's "either direction" (p. 69) is wrong.
- Rotates ASLC/ASRC and ASRB operate on all 16 nibbles; only `ASRB.F fs`
  takes a field (8, 9).
- NOP3 is `820` in SASM (HS=0 with an empty mask); Gariepy, Mastracci and
  the tutorial give `420`, which is GOC to the next instruction. Both are
  no-ops.
- `80B` is BUSCC, three nibbles, on the real Saturn; the tutorial's `80Bxx`
  Saturn+ prefix only exists on the ARM emulation.
- Relative branch bases, from the ranges in 6.5-6.6: GOC, GONC and GOTO
  count from pc+1, GOLONG from pc+2, GOSUB from pc+4 and GOSUBL from pc+6
  (the return address), GOYES from its own offset field (pc+3, or pc+5 for
  the `808x` bit tests).
- ST=0 n is `84n`, ST=1 n is `85n` (8, 9); the prose in 6.11.1 has them
  swapped.
- `D0=AS` / `AD0XS` copy or exchange the low four nibbles only (8).
- RSI: the CPU treats any input line currently high as a new interrupt;
  inside the handler it waits for RTI, otherwise it vectors immediately (8).

## Open points

- The "C=IN only from even address" rule (Mastracci 3.4, tutorial p. 94) is
  contradicted by Sylvester, who says odd (src: [[sources/keyb49-sylvester]]).
  Voyage also says A=IN and C=IN misbehave at odd addresses (src:
  [[sources/voyage-48gx]] p. 102). Software always calls CINRTN, so an
  emulator can ignore parity unless a program depends on the failure. See [[questions/c-equals-in-even-address]].

## Contradictions

- HST bit order: Mastracci 2.2 (XM, SR, SB, MP) versus SASM, Nickel and the
  tutorial (XM, SB, SR, MP). Resolved for the latter:
  [[questions/hst-bit-order]].
