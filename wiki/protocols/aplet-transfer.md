---
title: "Aplet transfer: the 38G, 39G and 40G with a PC \"disk drive\""
type: protocol
status: draft
sources: ["[[sources/hpgcomm]]", "[[sources/kermit-protocol-manual]]", "[[sources/hp38g-ug]]", "[[sources/hp39g40g-ug]]", "[[sources/connectivity-kit]]"]
tags: [kermit, xmodem, transfer, satx, aplets]
models: [38g, 39g, 40g]
---

# Aplet transfer: the 38G, 39G and 40G with a PC "disk drive"

The 38G, 39G and 40G start every PC transfer themselves: SEND and RECEIVE
offer "a disk drive (or a computer)" beside another calculator (src:
[[sources/hp38g-ug]] 1-26, 1-27; [[sources/hp39g40g-ug]] 16-5). The PC
plays that disk drive. The link is Kermit ([[protocols/kermit]],
[[sources/kermit-protocol-manual]]) with the **calculator as the client**
and the PC as a server that keeps files in one folder, the reverse of the
48 and 49 ([[protocols/server-commands]]). The 39G and 40G can also take
an aplet by XModem ("HP39/40 (Wire)"), see the last section.

Unless a line cites a source, the facts here were observed on the
clean-room saturnus emulator on 2026-10-11, with the 38G ROM A1.67
(`38G_A167.ROM`) and the 39G/40G ROM (`rom.39g`, 2000-11-19; the 40G runs
the same ROM, so it is expected to behave the same, not observed
separately), a small Kermit server and XModem sender on the other end of
the emulated serial port, 9600 baud, 8 bits, no parity.

## Menus on the calculator

- 38G: LIB (Aplet Library), then SEND or RECV on the menu keys; "SEND
  TO..." offers "HP 38G" and "Disk drive...".
- 39G: APLET (Aplet Library), SEND or RECV; the choices are "HP39 (IR)",
  "HP39/40 (Wire)" and "Disk drive...".
- After "Disk drive...", SEND asks "Send now" or "Pick location...". Send
  now sends at once; Pick location shows the directory's entries ("ITEMS
  IN CURRENT DIR", each with its kind, such as APLET) first.
- The LIST catalog (39G: SHIFT 7) has SEND and RECV too, with the same
  choices. HPGComm's help names the Aplet Library, List, Matrix, Program
  and Notepad views as the ones that transfer (src: [[sources/hpgcomm]]
  help); only aplets and a list were tried here.

## Packets

Every operation is a series of short Kermit transactions, each opened by
the calculator:

- **I (initialize)** with `~* @-#Y3`: MAXL 94, TIME 10 s, no padding, EOL
  CR, QCTL `#`, QBIN `Y` (it will 8-bit quote if asked), CHKT `3`. It is
  followed, after the server's ACK, by one **R** (get a file) packet with
  a type-1 check. The I's check type does not carry over: the R and the
  file that answers it use what that file's S exchange agrees.
- **S (send-init)** with the same `~* @-#Y3`, when the calculator sends
  a file: then F (the file name), D packets, Z, B, as in any Kermit send.
- When the PC answers an R with S, F, D, Z and B, the calculator's ACK to
  the S is `~& @-# 1` (TIME 6, no 8-bit quoting) and it echoes the file
  name in its ACK to F.
- A server that answers with CHKT `1` gets type-1 checks; with `3` the
  rest of the transaction uses the 3-byte CRC. Both work.
- Both ROMs sent their I and their S **twice in a row** before the first
  ACK, and went on normally whether the second copy was ACKed again or
  NAKed. A server must treat a repeated first packet as a duplicate.
- Generic (G) packets were never seen. HPGComm refuses them (src:
  [[sources/hpgcomm]] source).
- When the server answers an R with an E packet (file missing), the
  calculator replies with its own E, `Transfer Failed` followed by CR LF,
  and ends the transaction. What it shows next depends on the file (below).

## The directory files

The disk drive holds two plain-ASCII files the calculator reads and writes
itself:

- **`HP38DIR.CUR`** (39G and 40G: `HP39DIR.CUR`), the pointer to the
  current directory. After initialisation it is
  `HP38AscA A 11 HP38DIR.0004 Root` (31 bytes).
- **`HP38DIR.000`** (`HP39DIR.000`), the current directory's list.
  Empty, it is `HP38AscA ` (9 bytes, the last one a space).

The format, read from these files:

- An 8-byte header, `HP38Asc` or `HP39Asc` and a kind letter (`A` for a
  directory file), then a space.
- Then one entry per file, with nothing between entries: a kind letter, a
  space, the length of the file name in decimal, a space, the file name,
  the length of the title in decimal, a space, the title. The lengths are
  in characters, as many digits as needed (`10 Parametric`).
- In `HP38DIR.CUR`, the single entry has kind `A`, names the directory
  file and carries its title `Root`.
- The kind letter of an entry is the 8th byte of the file it names: an
  aplet file starts `HP38BinB` (39G: `HP39BinB`) and is listed as `B`; a
  list starts `HP39BinE` and is listed as `E`. The letters of matrices,
  programs and notes were not observed.
- New entries go **first**. After sending Function and then Parametric
  from a 38G:
  `HP38AscA B 12 PARAMETR.00010 ParametricB 12 FUNCTION.0008 Function`.
- HPGComm treats a folder as a calculator directory when it holds both
  files of one model, and knows `HP38DIR.` or `HP39DIR.` followed by a
  digit as directory files (src: [[sources/hpgcomm]] source). Only `.000`
  and `.CUR` were seen; no calculator menu that makes a second directory
  was found.

## File names and file contents

- The calculator names a file from its title: upper case, the first 8
  characters, padded to 8 with `0`, extension `.000`. Observed:
  `FUNCTION.000`, `PARAMETR.000` (Parametric), `XYZ00000.000` (an aplet
  saved as XYZ), `L1000000.000` (list L1). Two titles with the same first
  8 characters were not tried.
- Sending an aplet whose title is already in the directory asks "Replace
  existing aplet XYZ?"; YES sends it under the same file name and rewrites
  the directory with the entry once.
- The file is binary: the 8-byte header (`HP38BinB`, `HP39BinB`,
  `HP39BinE`), one byte with the title's length, the title, then the
  object. Function was 589 bytes from either ROM, Parametric 563 bytes
  from the 38G.

## SEND to the disk drive

1. I, then R for `HP38DIR.CUR`.
2. If the server has no such file it answers E. The calculator sends E
   (`Transfer Failed`) and shows "Disk drive not prepared for transfer.
   Initialize directory?". YES makes it send `HP38DIR.CUR` (S, F, D, Z,
   B), ask for `HP38DIR.000` (E again), send the empty `HP38DIR.000`, and
   carry on. NO ends the operation.
3. With `HP38DIR.CUR` served, it asks for the directory file it names
   (`HP38DIR.000`).
4. It sends the aplet's file in its own S transaction.
5. It sends the updated `HP38DIR.000`, the new entry first.

HPGComm handles a missing file the same way, refusing it and waiting for
the calculator to write the directory (src: [[sources/hpgcomm]] source).
The connectivity kit's guide says the PC's 38G directory may need to be
initialised from the calculator (src: [[sources/connectivity-kit]]).

## RECEIVE from the disk drive

1. I and R for `HP38DIR.CUR`, then I and R for the directory file it
   names.
2. The calculator lists the titles of the kind-`B` entries (in the Aplet
   Library) and "Other...". Other opens "GET FILE", which asks for a file
   name typed on the calculator.
3. I and R for the chosen file name. The server sends it (S, F, D, Z, B).
   An aplet already on the calculator was replaced without a question.
4. A file the server does not have: the server's E, the calculator's E,
   and "File NOTTHERE.000 not found. Get another?".
5. A file that is not an aplet (11 bytes of text served as an aplet):
   transferred, then "Bad Argument Type".

A directory file that lists a file the folder lacks is therefore harmless;
the calculator asks for another.

## XModem (39G and 40G only)

"HP39/40 (Wire)" is XModem ([[protocols/xmodem]]); the 38G does not offer
it.

- **RECV, HP39/40 (Wire)**: the calculator is the receiver. It sends NAK
  (0x15) to ask for 128-byte blocks with the one-byte checksum, about every
  3 s, and after about 30 s without a block it gives up with three CAN
  (0x18) and "Transfer Failed:".
- Sending it the aplet file kept from a Kermit SEND (header `HP39BinB`),
  padded with NUL to whole blocks, then EOT, installs the aplet: an aplet
  deleted on the calculator came back under its title.
- A file that is not a binary aplet (here a directory file) is ACKed
  block by block, then the calculator shows "Transfer Failed:". HPGComm's
  README says the same (src: [[sources/hpgcomm]] README).
- **SEND, HP39/40 (Wire)**: the calculator is the sender. Answering its
  wait with NAK gets 128-byte blocks with the checksum; Parametric came as
  640 bytes, the `HP39BinB` file padded with NUL.
- HPGComm also accepts `D` instead of NAK as the receiver's first byte
  and then sends 1024-byte blocks with a 16-bit CRC computed over nibbles
  (src: [[sources/hpgcomm]] source). The 39G ROM opened with NAK, so the
  `D` variant was not seen.

## Screen captures

HPGComm reads an incoming transfer that starts with ESC (0x1B) as a 38G
screen capture, and one whose second byte is `W` or `G` as a 39G/40G
capture (src: [[sources/hpgcomm]] source). Not observed on the emulator.

## Open

- Matrix, program and note entries in the directory file, and how two
  titles with the same first 8 characters are named.
- The 40G and real hardware: everything above is from the emulator and
  HPGComm.

Context: [[hardware/hp38g]], [[hardware/hp39g-40g]],
[[protocols/kermit-hp]], [[questions/hp38g-39g-transfer-protocol]].
