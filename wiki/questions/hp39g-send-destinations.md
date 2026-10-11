---
title: "Does the 39G's SEND offer two destinations or three?"
type: question
status: open
tags: [serial, transfer]
models: [39g, 40g]
sources: ["[[sources/hp39g40g-ug]]"]
---

# Does the 39G's SEND offer two destinations or three?

The HP 39G/40G User's Guide says SEND and RECV each offer two
destinations: another HP 39G, or a disk drive on a PC (src:
[[sources/hp39g40g-ug]] 16-5). On the clean-room saturnus emulator with
the 39G/40G ROM (`rom.39g`, 2000-11-19), observed 2026-10-11, the Aplet
Library's SEND and RECV and the List catalog's SEND list three: "HP39
(IR)", "HP39/40 (Wire)" and "Disk drive…" ([[protocols/aplet-transfer]]).

Possible readings, none confirmed:

- The guide describes an earlier ROM, before the wire transfer.
- The guide counts the infrared and the wire calculator transfers as one
  destination, "another HP 39G".
- The 40G (no IR port, same page of the guide) shows a different list;
  it runs the same ROM ([[questions/hp39g-40g-model-detection]]) and was
  not run here.

How to settle: a real 39G or 40G, or the 40G strap set on the emulator
([[hardware/hp39g-40g]], "Model strap"), and the guide's version against
the ROM's.
