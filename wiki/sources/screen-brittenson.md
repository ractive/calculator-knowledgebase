---
title: "Brittenson, HP48SX screen addresses"
type: source
authors: ["Jan Brittenson"]
year: 1990
raw: "raw/saturn-hardware/hp48-hw-notes/screen/SCREEN"
status: digested
tags: [display]
models: [48sx]
---

# Brittenson, HP48SX screen addresses

A 1990 comp.sys.handhelds reply. Only the first 10 lines are about the
calculator: RAM pointers (not hardware registers) to the system GROBs on the
48SX ROM of that time: #70551 menu GROB (131x8), #70556 stack GROB
(131x56), #7055B current display GROB, #70560 graph GROB (at least 131x64), #70565 a
second graph GROB (?). The rest is about the SAD disassembler.

ROM-version-specific; useful only to confirm the 131x56 + 131x8 split of the
screen (see [[hardware/display]]).
