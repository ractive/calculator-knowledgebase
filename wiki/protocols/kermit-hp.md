---
title: "Kermit as implemented by HP calculators"
type: protocol
status: draft
sources: ["[[sources/hp48g-ug]]", "[[sources/hp48-sikug]]", "[[sources/hp49g-um]]", "[[sources/connectivity-kit]]", "[[sources/hp48-kermit-hints]]", "[[sources/io-guide]]", "[[sources/hp48-faq]]", "[[sources/serial49-sansonovski]]", "[[sources/buf49-flipse]]"]
tags: [kermit, hp48, transfer, hptx]
---

# HP calculator Kermit

The HP-specific behaviour on top of the generic protocol in
[[protocols/kermit]]. Settings live in IOPAR ([[protocols/iopar]]); the server
is on [[protocols/server-commands]].

## From HP's I/O guide (48SX)

- Use block check type 3 (CRC) over IR, where errors are more frequent (src:
  [[sources/io-guide]] 4).
- In server mode the HP 48 sends "I" packets to negotiate parameters such as
  the block check type (4).
- The HP 48 Kermit does not use XON/XOFF; packets are small enough (4).
- The peer's input buffer must hold at least 14 bytes to receive the HP's
  "S" or "I" packet, and must fit the largest "F" (file name) packet (4).
- Kermit functions, XMIT and SRECV open the port automatically (2.4.1).

## From the FAQ

- Transfer binary objects in binary mode on both ends; a host doing text
  conversion corrupts them (src: [[sources/hp48-faq]] 6.11). See
  [[protocols/hp-object-format]].
- HP's Kermit is written in System RPL and appends each packet to the data
  received so far by copying, so large transfers slow down progressively; no
  sliding windows (6.13).
- Start the Kermit receiver before the sender; for XModem start the sender
  first (6.15).
- The HP48 with its cable is a DCE; connecting it to a modem needs a
  null-modem adapter (12.3).
- G/GX: right-shift right-arrow starts the Kermit server (4.19).

## Host-side electrical notes

- Early HP49G units have a weak TX driver; host programs should assert DTR
  and RTS so port-powered buffers work (src: [[sources/buf49-flipse]] p. 1;
  [[sources/serial49-sansonovski]] p. 2-3). See [[hardware/uart]].

## From the Columbia hints page

- Defaults: 9600 bps (also the maximum), 8 data bits, no parity, 1 stop bit,
  no flow control; the HP uses no modem signals, so the host must not wait
  for carrier (src: [[sources/hp48-kermit-hints]], Communications settings).
- No long packets, no sliding windows; negotiation handles this
  automatically (Protocol settings).
- Block check types 1, 2 and 3; type 3 by default on most models (Protocol
  settings).
- The HP does not accept unprefixed control characters: the host must prefix
  all of them (Kermit 95 needs `SET CONTROL PREFIX ALL`) (Protocol settings).
- ASCII mode is not plain text: on send the HP decompiles the object to text,
  applies the translation mode and inserts CR before LF; on receive it
  translates backslash codes and compiles each packet as it arrives, so a
  syntax error aborts the transfer at once. Arbitrary text files cannot be
  received in ASCII mode (Programs versus data).
- Binary receive collects the whole file as a string (slowing down as it
  grows, copying on every packet); only at the end does the HP check for an
  `HPHP48-x` prefix and a valid object, and otherwise keeps the string. So
  any file can be received, as a string (Programs versus data). See
  [[protocols/hp-object-format]].
- Because of the slowdown, a host may need a longer timeout (e.g. 20 s) and a
  pause before each packet (e.g. 100 ms) (Programs versus data).
- Most users recommend running the HP as server and the host as client; the HP as
  client has unreliable REMOTE commands (Protocol settings).

## From the HP manuals

- The HP 48's default protocol is Kermit; the TRANSFER form chooses wire or
  IR, Kermit or XModem, ASCII or binary, translation, block check, baud and
  parity (src: [[sources/hp48g-ug]] 27-8, 27-9). Settings live in IOPAR and
  flags -33 to -39 ([[protocols/iopar]]).
- Receiving file names: illegal characters abort the transfer with an error
  sent to the computer; a name matching a built-in command or (with flag -36
  clear) an existing variable gets a `.1` style extension (src:
  [[sources/hp48g-ug]] 27-11). hptx should send valid RPL names.
- ARCHIVE `:IO:name` backs up HOME to the host over Kermit, always in binary;
  a ticking clock can corrupt the backup (src: [[sources/hp48g-ug]] 27-12).
  RESTORE of that file replaces all user memory.
- HP 48 to HP 48 over IR works with one side choosing "Get from HP 48" and
  the other "Send to HP 48" (src: [[sources/hp48g-ug]] 27-1); the 49G does
  the same over its cable, and a 49G-48 exchange must use ASCII format on
  both sides with matching settings (src: [[sources/hp49g-um]] A-1 to A-3).
- HP's own PC software (Sparcom Link48, 1994) runs everything with the HP in
  server mode at 9600 with block check 3 and supports 2400 baud (for IR)
  (src: [[sources/hp48-sikug]] 1-3, 1-4, 2-2).
- HP's 1999 Connectivity Kit: only baud and checksum must match; a
  calculator-end adaptor is used for the 48 and must not be used with the 49G
  or 38G (src: [[sources/connectivity-kit]]).
