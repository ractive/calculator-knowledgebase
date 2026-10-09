---
title: "Memory controller: daisy-chain CONFIG, UNCNFG, RESET, C=ID"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/saturn-tutorial]]", "[[sources/teuwen-gx-hardware]]", "[[sources/hp49-memmap-smith]]", "[[sources/memory49-sousa]]", "[[sources/voyage-48gx]]"]
tags: [saturn, memory, config, bank-switching]
---

# Memory controller

The Saturn maps its 1 M-nibble address space onto chip-select "modules" that
are configured one after the other in a fixed daisy-chain order.

## Modules on the HP48

| Device | 48S/SX role | 48G/GX role |
| --- | --- | --- |
| ROM | ROM | ROM |
| HDW | I/O RAM (hardware registers) | I/O RAM |
| RAM | RAM | RAM |
| CE1 | port 1 | bank select (latch for port 2 banks) |
| CE2 | port 2 | port 1 |
| NCE3 | unused | port 2 |

(src: [[sources/mastracci-saturn-guide]] 2.4). The G/GX role shift is why SX
code that addresses ports by controller breaks on the GX; see
[[hardware/hp48gx]].

## Instructions

- **RESET** (80A): unconfigures all modules and places RAM at its default
  address (src: [[sources/mastracci-saturn-guide]] 2.4, 3.4).
- **CONFIG** (805): configures the next unconfigured module with C(A). Most
  modules take two CONFIGs: first a size, given as #100000 minus the size in
  nibbles, then the base address. Sizes are multiples of #100 and at
  least #1000 (4 K nibbles, 2 KB); addresses are multiples of #100. HDW takes only
  one CONFIG (the address); its size is fixed (2.4).
- **UNCNFG** (804): unconfigures the module whose base address is in C(A)
  (2.4).
- **C=ID** (806): returns in C(A) the ID of the next module to configure. Bits
  from MSB: the top three nibbles of the last configuration address, then one
  bit that is 1 if the next CONFIG takes an address and 0 if it takes a size,
  then a 7-bit module ID. #00000 when all modules are configured (2.4).

### Module IDs (C=ID low bits)

| ID | Module |
| --- | --- |
| #01 | NCE3 |
| #03 | RAM |
| #05 | CE1 |
| #07 | CE2 |
| #19 | HDW |

(src: [[sources/mastracci-saturn-guide]] 2.4)

### Order and priority

- Configuration order: ROM (always configured), HDW, RAM, CE1, CE2, NCE3
  (src: [[sources/mastracci-saturn-guide]] 2.4).
- When windows overlap, priority is HDW > RAM > CE2 > CE1 > NCE3 > ROM (2.4).
  An emulator must resolve every read and write through this priority.

## Bank switching on the 48GX

- CE1 drives an external 74HC174 latch, normally configured at #7F000.
  Reading any address in the CE1 window latches low address bits into it: the
  bank select uses `D0=#7F000+#40+2*n` then a byte read, n = 0..31. Address
  bits from MSB: BEN (bit 6 of the latch), five bank bits, one unused bit from
  the nibble-to-byte conversion (src: [[sources/mastracci-saturn-guide]] 4.4).
- Upper ROM (NMA18 / AR18) and port 2 (NCE3) share Yorke pin 85; DA19, bit 3
  of #129, selects which one is active. Upper ROM and port 2 are mutually
  exclusive. Enabling port 2: set DA19 for port 2, then read the bank
  manager with BEN high. Disabling: clear BEN, then set DA19 back for ROM
  (4.4). Mastracci gives "set" as port 2; that polarity is wrong, DA19 = 0
  selects port 2 (see the Giesselink section below and
  [[questions/da19-polarity]]).
- Port 2 on the GX is up to 32 banks of 128 KB, 4 MB total (4.3).
- The latch is a 74HC174 clocked by CE1: it stores byte-address bits A0-A4 as
  port-2 address lines A17-A21 and A5 as BEN (src:
  [[sources/teuwen-gx-hardware]] 4). This matches Mastracci's nibble
  offset #40 + 2n: nibble bit 0 is dropped, nibble bits 1-5 are the bank and nibble
  bit 6 is BEN.

## Flash banking on the 49G

The 49G reuses the CE1 bank-switch latch to drive the upper address lines of
its 2 MB flash (src: [[sources/memory49-sousa]], Giesselink quote). Two views
exist: banks 0-3 at #00000-#3FFFF and banks 0-15 at #40000-#7FFFF. The ROMs
use latch bits A5-A6 for the first and A1-A4 for the second (Sousa's order;
Giesselink's text gives A1-A2 and A3-A6, which does not boot:
[[questions/hp49g-bank-latch-bits]]). On the 49G the latch can also be written, and CE1 is
unconfigured except while switching (src: [[sources/saturn-tutorial]]
p. 163-164). The A19/NCE3 pin is permanently NCE3. Details on
[[hardware/hp49g]].

## Controller model (Giesselink)

The tutorial's chapter 66, written by the Emu48 author, is the most precise
description and supersedes Mastracci where they differ.

- Six controllers in the Clarke (S/SX) and Yorke (G/GX/49G): NCE1 (ROM), HDW
  (I/O registers, 64 nibbles), NCE2 (RAM), CE1, CE2, NCE3. Mastracci's "RAM"
  is NCE2 (src: [[sources/saturn-tutorial]] p. 151).
- Daisy chain: CPU, HDW, NCE2, CE1, CE2, NCE3, NCE1. CONFIG goes to the first
  controller that is not fully configured (p. 151).
- Access priority when windows overlap: HDW, NCE2, CE2, CE1, NCE3, NCE1. An
  address that no controller claims is answered by NCE1 (ROM) (p. 151).
- After CPU reset or RESET all controllers except NCE1 are unconfigured;
  NCE1 has no configuration and is always present (p. 99, 151).
- Configuration order: HDW address; then size and address for NCE2, CE1,
  CE2, NCE3. HDW size is fixed at 64 nibbles and its base must be a multiple
  of #40. Other sizes and addresses are multiples of #1000 (2 KB) because the
  controllers only see A19-A12. All controllers should be configured, unused
  ones at the minimum 2 KB (p. 151-152).
- The size value is a mask, not a length: a 1 bit means "compare this
  address line", 0 means "ignore". Size #100000 - n gives a contiguous
  window; a configured size larger than the chip mirrors it, e.g. a 128 KB
  card given size #40000 at #00000 appears at #00000 and #80000 (p. 152).
- The effective base is address AND size-mask, so a window snaps down to a
  multiple of its size; chips see the CPU address lines directly, so a
  device can only start on a boundary of its own size (p. 152-153).
- UNCNFG takes an address inside the window of the highest-priority device
  at that address and unconfigures that device. When two devices share an
  address they are unconfigured in priority order, not in reverse
  configuration order (p. 152-153).
- C=ID returns #00000 when everything is configured; otherwise C(B) says what
  the next CONFIG means and the rest of C(A) holds the last size or address
  used for that device (clear C(B) to reuse it; for HDW clear the low 6
  bits) (p. 154):

| C(B) | Next CONFIG is |
| --- | --- |
| #01 | size of NCE3 |
| #03 | size of NCE2 |
| #05 | size of CE1 |
| #07 | size of CE2 |
| #19 | address of HDW |
| #F2 | address of NCE3 |
| #F4 | address of NCE2 |
| #F6 | address of CE1 |
| #F8 | address of CE2 |

(src: [[sources/saturn-tutorial]] p. 154; Mastracci's ID list (2.4) is the
size half of this table.)

### Voyage on the controllers (48G/GX)

- RESET returns all five managers (HDW, RAM, CE1, CE2, NCE3) to the
  unconfigured state; ROM has no manager and always starts at #00000 (src:
  [[sources/voyage-48gx]] p. 87, 130).
- Every manager exists and must be configured whether or not its module is
  fitted; unused ones go to #7E000 with the minimum size #1000. The 48G has
  the same hardware and managers as the GX, minus the card connectors (p. 87-88,
  184).
- A module's start address must be a multiple of its configured size; a
  module smaller than its window repeats, e.g. 32 KB declared as 128 KB
  at #C0000 shows at #C0000, #D0000, #E0000, #F0000 (p. 87-88).
- C=ID while HDW is next returns the last HDW address plus #19,
  normally #00119; the size and address codes for the other managers match the
  tutorial's table (p. 131).
- To reach ROM hidden under RAM the OS either moves RAM to #C0000
  (UNCNFG #80000, two CONFIGs) or temporarily shrinks it, and updates #11F
  accordingly (p. 130, 185, 199).
- Interrupts must be off while a module is unconfigured, because the handler
  checks for unconfigured managers and forces a system halt (p. 185). Duchesne
  says only the S handler does this: [[questions/config-check-in-handler]].
- The bank switcher window is #1000 nibbles but only the first 128 are
  decoded: #7F000-#7F03F (BEN = 0) and #7F040-#7F07F (BEN = 1). The ROM never
  uses the BEN = 0 half, which Voyage guesses would bank port 1 (p. 205-206).

## Default maps

| Model | Map after the ROM configures memory |
| --- | --- |
| 48S/SX | ROM #00000-#6FFFF visible; I/O #00100-#0013F; RAM 32 KB #70000-#7FFFF (covers ROM); slot 1 #80000-#BFFFF; slot 2 #C0000-#FFFFF; NCE3 unused, 2 KB at #D0000 covered |
| 48G | I/O #00100; ROM #00140-#7DFFF; empty slots 1 and 2 at #7E000 (2 KB); bank switcher #7F000 (2 KB); RAM 32 KB #80000-#8FFFF; ROM #90000-#FFFFF |
| 48GX, no cards | as 48G but RAM 128 KB #80000-#BFFFF; ROM #C0000-#FFFFF uncovered |
| 48GX, card in slot 1 | slot 1 card at #C0000 (32 or 128 KB), empty slot 2 at #7E000 |
| 48GX, cards in both | slot 1 and slot 2 both at #C0000, slot 1 covering slot 2 (CE2 > NCE3) |
| 38G | I/O #00100; RAM 32 KB on NCE2 at #F0000; CE1 bank switcher #7F000; CE2 and NCE3 parked at #7E000 (ROM A1.67 in saturnus, [[questions/hp38g-memory-controllers]]) |
| 39G/40G | I/O #00100; RAM 256 KB on NCE2 at #80000; CE1 configured only around a bank switch (4 KB at #7E000); CE2, NCE3 never configured ([[hardware/hp39g-40g]]) |
| 49G | see [[hardware/hp49g]] "Controllers (Giesselink)" |
| 42S (Lewis) | fixed map, no CONFIG: [[hardware/lewis]] |

(src: [[sources/saturn-tutorial]] p. 154-158, for the 48 rows). A full GX bring-up sequence:
HDW #00100; NCE2 size #C0000, address #80000; CE1 size #FF000,
address #7F000; CE2 and NCE3 size #C0000, address #C0000 (p. 154-155).

## 48GX bank switcher in detail

- HP added address line A19 for the 512 KB ROM and multiplexed it with NCE3
  on one Yorke pin; DA19 (#129 bit 3, write side of LINECOUNT) selects it.
  The tutorial's code treats DA19 = 1 as A19 (upper ROM) and 0 as NCE3
  (port 2) (src: [[sources/saturn-tutorial]] p. 158-159); Mastracci (4.2) and
  Voyage (p. 202) say the opposite. Emu48 confirms the tutorial: DA19 = 0
  disables upper ROM and mirrors the lower 256 KB at #80000 (src:
  [[emulators/emu48]] CHANGES SP9). Answered:
  [[questions/da19-polarity]].
- #129 reads back something else (the row counter), so the OS keeps the last
  written LINECOUNT byte in RAM ("LINECOUNTg", #8069A on the G series) and
  code must modify that copy, never read-modify-write #128-#129 (p. 158-159).
- The latch stores nibble-address bits A1-A6 as A17-A21 and BEN; BEN = 0
  always disables port 2, BEN = 1 lets NCE3 select it (p. 158).
- Order matters: to get upper ROM, read #7F000 (BEN = 0) then set DA19; to get
  port 2, clear DA19 then read #7F040 + 2n (p. 159).
- **Off-by-one quirk** (Giesselink's experiments, contradicting HP's
  explanation): a byte read at #7F044 actually reads three nibbles and
  latches #7F046, i.e. the next bank. Bank 31 (#7F07E) therefore
  latches #7F080 with BEN = 0, which is why a 4 MB card always gives "Invalid Card
  Data". Data still round-trips because reads and writes use the same skew;
  physically, bank 0 lives in the card's second 128 KB (p. 159-160).
- Write protection: on the SX the Clarke gates CE1/CE2 writes with the card's
  write-protect input. On the GX, NCE3 has no such gate; slot 2's
  write-protect line goes to CE1's input instead, so it write-protects the
  bank switcher. A nibble write to a protected latch still performs the read
  half of read-modify-write and latches that address; a byte write at an even
  address does nothing (p. 160-161).

An emulator that wants the 4 MB-card behaviour must model the extra nibble
read; one that does not will show all 32 banks working.

## Emu48 findings

Unmapped reads return an open-bus value; windows align to their size; the
bank latch only clocks when CE1 actually owns the address; addresses wrap
at #FFFFF; SHUTDN and reset clear the bank flip-flop (src: [[emulators/emu48]]
Memory controller, SHUTDN). This is the 48GX's latch; the 49G's survives
SHUTDN, since its ROM sleeps while running from a switched bank
([[hardware/hp49g]] "Bring-up findings"). The 39G/40G are taken to do the
same (saturnus, inferred).

## Takeover tricks that depend on this

ROM vectors at #0000F; a program can move RAM to #00000 (after copying it)
or configure a card port at 0 to install its own interrupt handler (src:
[[sources/mastracci-saturn-guide]] 6.8). An emulator that hard-wires the map
breaks these programs.

## Contradictions

- Priority of CE1 versus CE2: Voyage lists the bus order I/O RAM, internal
  RAM, bank switcher (CE1), port 1 (CE2), port 2 (NCE3), ROM and says priority
  follows that order (p. 86-87, 205); Giesselink says priority differs from
  the daisy chain and puts CE2 above CE1 (tutorial p. 151), as does Mastracci
  (2.4). [[questions/bus-priority-ce1-ce2]].

- Granularity: Mastracci says sizes and addresses are multiples of #100 with
  a minimum of #1000 (2.4); Giesselink says multiples of #1000 because only
  A19-A12 reach the controllers (tutorial p. 151-152). Follow Giesselink.
- ID list: Mastracci gives only the five size/HDW codes (2.4); the tutorial
  adds the #F2-#F8 address codes (p. 154). Not a conflict, an omission.

## Checked against saturnng (2026-10-05)

Black-box runs of saturnng 6.1.1 (48SX, ROM J, both slots empty, its
`--debug-implementation` instruction log and `--debug-bus` CONFIG log) next
to the clean-room saturnus emulator. No saturnng source was read. This is
emulator against emulator, not hardware.

- Cold boot to "Try To Recover Memory?": both execute the same instruction
  sequence, about 20,300 instructions once saturnng's repeated `C=IN` log
  lines are collapsed. The only differences are iteration counts of the
  loop at #0127D that polls #00138 (timing). saturnng logs the CONFIG
  values #00100, #F0000 and #70000, then for the slots #C0000, #80000, the
  values #C0000, #C0000, #FF000 and #D0000: exactly the 48S/SX default map.
- Open-bus value of empty CE1, CE2 and NCE3: **not observable with the stock
  ROM**. On the boot, key entry, OFF and ON paths no data read is answered
  by CE1, CE2 or NCE3, and no read above #7FFFF goes unclaimed (every
  DAT0/DAT1 read checked against the controller selection in saturnus). The
  ROM learns that the slots are empty from #10F ([[hardware/card-ports]]),
  not by reading the windows.
- Open-bus probe (2026-10-05, saturnng, emulator against emulator, **not
  hardware**): a hand-assembled Code object sent over Kermit to the 48SX
  (ROM J, `CARDS=0`, both slots empty) read 16 nibbles each with `D1=(5)`
  and `C=DAT1 W` at #80000 (empty CE1), #C0000 (empty CE2) and #D0000
  (NCE3's 2 KB, covered by empty CE2) into a string. saturnng returned
  `0000000000000000` for all three; a control read of #00000 returned
  `2369B108DADF1008`, matching the ROM image, so the reads are real. So
  saturnng models open bus as 0, which is also saturnus's value. The real
  48SX value still needs hardware (unverified).
- CE2-above-CE1 priority: **not observable with the stock ROM**. CE1
  (#80000-#BFFFF) and CE2 (#C0000-#FFFFF) never overlap on the 48SX; the
  only overlap is NCE3 (2 KB at #D0000) under CE2, where all sources agree
  CE2 wins. Settling it needs a program that configures CE1 and CE2 at the
  same base with different cards in both slots. The saturnng container
  cannot load a 48SX port-2 file (its port 2 is fixed at 4 MB), so this
  needs hardware or another oracle. [[questions/bus-priority-ce1-ce2]]
  stays open.

## Related

- [[hardware/io-ram]] for the HDW window contents.
- [[hardware/hp48sx]], [[hardware/hp48gx]], [[hardware/hp49g]] for the
  per-model memory maps.
