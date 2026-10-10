---
title: "Which wire protocol do the 38G, 39G and 40G use with a PC?"
type: question
status: open
tags: [serial, kermit]
models: [38g, 39g, 40g]
---

# Which wire protocol do the 38G, 39G and 40G use with a PC?

All three start every PC transfer from the calculator: SEND chooses
"another HP 38G" or "a disk drive (or a computer)", and RECEIVE lists the
aplets in the PC's current directory (src: [[sources/hp38g-ug]] 1-26,
1-27; [[sources/hp39g40g-ug]] 16-5; [[sources/connectivity-kit]]). No
source read names the protocol, the baud rate or the framing. The 38G has a
10-pin connector, and the 48's calculator-end adaptor must not be used
with it (src: [[sources/finseth-hp38g]]; [[sources/connectivity-kit]]).

Answered by observation (below): Kermit, with the calculator as the
client of a server on the PC (the reverse of the 48 kit). What stays open
is the directory file and the aplet format.

How to settle: boot a ROM in the emulator, choose SEND to a disk drive,
and decode the bytes on the emulated UART; or read the connectivity kit
(GPL, so facts only) or the "aplet disk drive" documentation. Matters for
hptx, which would need a server mode. Context: [[protocols/kermit-hp]],
[[hardware/hp38g]], [[hardware/hp39g-40g]].

## Progress from saturnus (2026-10-05)

Observed on the clean-room saturnus emulator (38G ROM A1.67 and the 39G
ROM), with a minimal Kermit server on the other end of the emulated
UART. The framing and packet types are Kermit's
([[sources/kermit-protocol-manual]] 4, 5, 6.3-6.4); no oracle.

- **Kermit, calculator as client.** SEND to a disk drive (38G: LIB, SEND,
  second entry; 39G: APLET, SEND, third entry) first sends an **I**
  (initialize) packet at 9600 baud:
  `SOH "+" " " "I" "~* @-#Y3" "Y" CR`, that is MAXL 94, TIME 10 s, no
  padding, EOL CR, QCTL `#`, QBIN `Y` (8-bit quoting if the other side
  asks), CHKT `3` (asks for the 3-byte CRC), type-1 check `Y`.
- **Then a GET.** After the server's ACK it sends an **R** packet asking
  for `HP38DIR.CUR` (39G: `HP39DIR.CUR`), the directory file of the
  "disk drive". So the calculator reads the PC's directory before it
  sends, as RECEIVE does.
- **The calculator receives normally.** Served as an empty file (S, F,
  Z, B), it ACKs each packet; its S reply is `~& @-# 1` (TIME 6, no
  8-bit quoting, type-1 check accepted). It then shows an error box saying
  the disk drive is not prepared for the transfer, and sends nothing more.
  The directory file must have content in a format still unknown.
- **Next step.** Find the format of `HP38DIR.CUR` (the connectivity kit
  or the aplet disk drive would write it; GPL tools are facts-only), serve
  a valid one, and watch the S/F/D packets of the aplet itself. hptx needs
  a Kermit server mode that answers I and R for this.
