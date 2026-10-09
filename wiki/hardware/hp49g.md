---
title: "HP 49G"
type: hardware
models: [49g]
status: draft
sources: ["[[sources/saturn-tutorial]]", "[[sources/intel-28f160s5]]", "[[sources/hp49-memmap-smith]]", "[[sources/memory49-sousa]]", "[[sources/keyb49-sylvester]]", "[[sources/buf49-flipse]]", "[[sources/serial49-sansonovski]]", "[[sources/hp48-faq]]"]
tags: [hp49g, model, flash]
---

# HP 49G

Yorke-based like the 48G/GX, with flash instead of mask ROM.

## Memory

| Item | Value |
| --- | --- |
| Flash | one Intel 28F160S5, 2 MB, 16 banks of 128 KB (src: [[sources/memory49-sousa]] Giesselink quote; [[sources/hp49-memmap-smith]]) |
| Flash use | bank 0 first 64 KB: write-protected boot sector; banks 0(second half), 8-15: user flash = port 2; banks 1-7: OS (Smith) |
| RAM | 512 KB: 256 KB IRAM (HOME, port 0) and 256 KB ERAM (port 1) (Smith) |

### Address map (normal operation)

| Range | Contents |
| --- | --- |
| #00000-#3FFFF | one of flash banks 0-3 (bank 0 at reset, then a bank in 1-3 after boot) |
| #40000-#7FFFF | any flash bank, or half of ERAM (port 1) when needed |
| #80000-#FFFFF | IRAM (HOME and port 0) |
| #00100-#0013F | I/O registers, overlaying flash |

(src: [[sources/hp49-memmap-smith]])

- The flash ignores the top address line, so banks also mirror
  into #80000-#FFFFF when RAM is unconfigured (Smith).
- Banks are selected by reading the CE1 bank-switcher window, as on the
  48GX: `base + #20*n` for the low view (n = 0-3), `base + 2*n` for the high
  view (n = 0-15) (src: [[sources/memory49-sousa]]). See
  [[hardware/memory-controller]].
- Internal serial number at flash bank 0 offset #00108, boot version string
  at #00214 (Smith).
- Writing flash needs NCE3 plus the #11C bit 3 enable (see Controllers);
  Sousa only warned against "toggling the IR line" (src:
  [[sources/memory49-sousa]]). The chip command set is on
  [[sources/intel-28f160s5]]; how the 49G turns nibble writes into byte
  cycles is still open: [[questions/hp49g-flash-write]].

## System RAM

HOME at #80711, current directory at #8071B, saved D1 at #806F8 as on the
48GX; flags in two words each, system at #80F02, user at #80F22 (ROM 2.10,
found in saturnus): [[hardware/hp48-system-ram]].

## Contradictions

- Bank latch bits: Sousa puts the low-window bank (banks 0-3) in address bits
  5-6 (`base + #20*n`) and the high-window bank in bits 1-4 (`base + 2*n`)
  (src: [[sources/memory49-sousa]]). Giesselink puts the low-window bank in
  A1-A2 and the high-window bank in A3-A6, with a worked example (src:
  [[sources/saturn-tutorial]] p. 163-164). The ROMs boot only with
  Sousa's order (2026-10-05 experiment): [[questions/hp49g-bank-latch-bits]].
- RAM controllers: Sousa's table (CE0, CE3 ...) does not match Giesselink's
  NCE2/CE2/NCE3 assignment; Giesselink's is the one with tested examples.
  Answered in [[questions/hp49g-ram-controllers]].

## Controllers (Giesselink)

The 49G uses the same Yorke as the 48G series (src: [[sources/saturn-tutorial]]
p. 162). Its controllers are wired differently:

| Controller | 49G use | Default |
| --- | --- | --- |
| NCE1 | flash, 2 MB, read only through this controller | always |
| HDW | I/O registers | #00100 |
| NCE2 | RAM 256 KB: HOME and port 0 | #80000-#FFFFF |
| CE1 | bank switcher, 6-bit latch | not configured |
| CE2 | RAM 128 KB, half of port 1 | not configured |
| NCE3 | RAM 128 KB, other half of port 1; also the flash write path | not configured |

(src: [[sources/saturn-tutorial]] p. 163-164)

- The shared A19/NCE3 pin is always NCE3 on the 49G, never A19, so no module
  can exceed 256 KB (p. 162-163).
- NCE1 shows flash banks at #00000-#3FFFF (one of banks 0-3) and #40000-#7FFFF
  (one of banks 0-15), mirrored at #80000-#FFFFF; as the lowest-priority
  controller it is covered by RAM there (p. 163).
- Bank switcher latch: nibble-address bits A1-A2 select the bank
  for #00000-#3FFFF, A3-A6 the bank for #40000-#7FFFF (as the tutorial
  states it; the ROMs use the opposite order, see Bring-up findings
  below). Unlike the 48GX, a write
  to the latch works as expected (the card-detect input is wired as
  read/write), but the read off-by-one quirk is the same. The OS configures
  CE1 only while switching (example: size #FF000 at #3F000, write, UNCNFG)
  and keeps the last value at #860B8 (CurROMBank2) (p. 163-165).
- Flash write access: configure NCE3 as 128 KB at #40000 (with CE1 and CE2
  moved out of the way) and set bit 3 of #11C, called LCR / LED control
  register; the flash bank chosen by the upper four latch bits is then
  writable at #40000-#7FFFF using the chip's own command set, which the
  tutorial does not describe (p. 164-165). This is the "IR line" Sousa hinted
  at: #11C bit 3 is the IR LED enable on the 48
  ([[hardware/uart]]).

## Flash and RAM use by ROM version

| ROM | Bank 0 | System banks | User banks | RAM port 0 | RAM port 1 |
| --- | --- | --- | --- | --- | --- |
| 1.05-1.18 | half boot, half user | next 7 | last 8 | 2 banks | 2 banks |
| 1.19 beta 6 | half boot, half system | next 7 | last 8 | 2 banks | 2 banks |
| 1.20+ (49G+ only) | half boot, half system | next 8 | last 7 | 2 banks | 1 bank |

(src: [[sources/saturn-tutorial]] p. 161-162). Flash chip: Intel
TE28F160-S5 (p. 161). The boot half of bank 0 is write-protected in hardware
(p. 161). In `.flash` update files the banks are named Part0 (boot), FS
(second half of bank 0), System, Part1, Part2 ... (no Part5) (p. 162).

## Keyboard

8x8 matrix plus ON at IN bit 15, different layout from the 48 (src:
[[sources/keyb49-sylvester]]); table on [[hardware/keyboard]].

## Serial port

Early units (serial ID below 94xxxxxx) have a weak TX driver that some PCs
cannot read; see [[hardware/uart]] (src: [[sources/buf49-flipse]];
[[sources/serial49-sansonovski]]).

The 49G has no IR port; it ships with a 49-to-49 cable and an adapter for the
48. Serial supports Kermit (binary and ASCII) and XModem (128-byte blocks with
checksum, 1K, 1K with CRC) at up to 9600 bps; the FAQ adds "15360 bps
internally, but no PCs support that speed" (src: [[sources/hp48-faq]] 2.14),
which matches baud code 7 on [[hardware/uart]].

## Flash chip and ROM images (2026-10-05)

- The 28F160S5 erases in 32 blocks of 64 KB; a 49G bank of 128 KB is two
  erase blocks, and the write-protected 64 KB boot sector is exactly erase
  block 0 (src: [[sources/intel-28f160s5]] Figure 4, p. 10). The chip runs
  in x8 mode on the 49G (unverified; a byte-wide chip is implied by the
  2 MB image packed two nibbles per byte).
- Commands, status register and identifier codes: one-byte commands, such
  as #40 program, #20 then #D0 block erase, #70 read status, #FF read array;
  status bit 7 ready, bits 5 and 4 errors, cleared by #50; manufacturer
  code #B0, device code #D0 (src: [[sources/intel-28f160s5]] Tables 3, 12,
  15). The hardware boot-sector protection would fit a set lock-bit with
  WP# held low, since lock-bits only bind while WP# is low (Table 13), but
  no source says how the 49G does it (unverified).
- The saturnus emulator assumes a nibble write at an even address is held
  and the next nibble write at the odd address of the same byte completes
  one byte cycle, low nibble first (emulator choice, unverified on
  hardware; [[questions/hp49g-flash-write]]).
- ROM images on hpcalc.org, inspected byte by byte (observation, not a
  source document):
  - `hp4950emurom.zip` ("ROM for Emulators 2.15", SHA-256 of `rom.49g`
    b01c13e24a692f35e6087106d58ec205b4696d5b5e35d57f8f94015f8bb1f1ca): its
    readme calls it the "HP 49gII/49g+/50g ROM 2.15", it contains "HP49
    and HP50G Port by JY Avenard", its first 64 KB differ in nearly every
    byte from the two images below, and there is no "Boot Version" string
    at #00214. Yet saturnng boots it with `MODEL=49g` to "Try To Recover
    Memory?" and runs its Kermit server (hptx README, emulator against
    emulator), so it does run on an emulated Saturn 49G. It is the image
    the saturnng oracle uses. Whether it runs on a real 49G is unverified.
  - `hp4950v210.zip` member `rom.49g` (ROM 2.10, 2 MB packed) and
    `beta1196.zip` member `rom.49g` (ROM 1.19-6, 4 MB unpacked, one nibble
    per byte) both hold the original 49G boot sector: "Boot Version 1.A" at #00214
    and a serial-number field at #00108 of bank 0, as Smith describes (src:
    [[sources/hp49-memmap-smith]]). hpcalc.org describes the 2.10 package
    as including a 49G build and an Emu48 image.

## Bring-up findings (2026-10-05)

From booting ROMs 2.15, 2.10 and 1.19-6 on the saturnus emulator and
comparing ROM 2.15 against saturnng (emulator against emulator, not
hardware):

- **Bank latch bit order**: A1-A4 of the latching access pick the bank
  at #40000-#7FFFF, A5-A6 the bank at #00000-#3FFFF (Sousa's assignment);
  Giesselink's opposite order does not boot. Evidence on
  [[questions/hp49g-bank-latch-bits]]. Reads and writes in the CE1 window
  both latch.
- **SHUTDN keeps the latch**: the OS executes SHUTDN while running from a
  switched low-view bank (e.g. at #017E7 in 2.15's boot, latch #12) and
  continues there after waking, so SHUTDN must not clear the 49G's latch.
  The 48GX's flip-flop is cleared by SHUTDN (src: [[emulators/emu48]]
  SP23); the 49G's latch evidently is not.
- **Boot timing**: the screen stays blank for about 0.6 s of emulated time
  while the boot code scans the banks with SHUTDN timer waits; then "Try To
  Recover Memory?" on the top line, YES on the first menu key and NO on
  the sixth (F); NO gives a "Memory Clear" box with OK, OK
  gives the stack in algebraic mode ("ALG").
- **Flash writes**: storing to port 2 programs the chip with write to
  buffer (#E8 ... #D0), see [[questions/hp49g-flash-write]].
- **Alpha letters**: the softkeys F1-F6 carry A-F; APPS, MODE, TOOL, VAR,
  STO, NXT, HIST, CAT, EQW, SYMB, y^x, square root, SIN, COS, TAN, EEX,
  +/-, X, 1/x and divide carry G to Z in that order (typed with ALPHA
  locked on the emulator; the saturnng TUI's letter keys agree). Left
  shift on the point key types `:` (tag delimiters).
- **ROM 2.15 boots as a 49G**: despite its 49g+/50g label (see the ROM
  images section above), it boots, takes keys, runs its Kermit server and
  writes its user flash on the emulated 49G, as it does on saturnng.
- **Power-on contrast**: 14 (range 9-24), observed on ROM 1.19-6 and 2.10
  after a cold start (2.10 sets 16 first, then 14 within a second;
  observed in saturnus 2026-10-07; saturnus renders it at about 90 % darkness).

System flag meanings: [[hardware/system-flags-49g]].
