---
title: "Horn, Long answers to short questions about bank switching"
type: source
authors: ["Joe Horn"]
year: 1994
raw: "raw/saturn-hardware/hp48-hw-notes/bank.txt"
status: digested
tags: [card-ports, bank-switching]
models: [48sx, 48gx]
---

# Horn, Long answers to short questions about bank switching

User-level explanation of SX versus GX ports (1994). No register details.

- Port 0 is main RAM: 0-256 KB on the GX, 0-288 KB on the SX, sized to its
  contents.
- Port 1 is 32 KB or 128 KB (the card in slot 1), identical on SX and GX; the
  overhead-projector display works in slot 1 on both.
- GX slot 2: a 32 KB card is port 2; a 128 KB or larger card is split into
  128 KB banks, each its own port (port 2 = first bank, port 3 = second ...).
  HP's 1 MB card gives ports 2-9; a 4 MB card ports 2-33. Slot 2 is
  permanently FREEd.
- On the SX, third-party (TDS) 256 KB / 512 KB cards bank-switch under
  program control and only the active bank is visible.
- GX slot 2 replaces the display lines with bank-switching lines.

Feeds [[hardware/card-ports]], [[hardware/hp48gx]], [[hardware/hp48sx]].
