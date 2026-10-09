---
title: "Revised HP48 Internals Address List (1991, with Paul Dale and Rick Grevelle)"
type: source
authors: ["HP 48 user community", "Paul Dale", "Rick Grevelle"]
year: 1991
raw: "raw/saturn-hardware/hp48-hw-notes/mlstarterkit/hardware/internals/byaddress"
status: skimmed
tags: [hp48, rom, ram, entry-points]
---

# Revised HP48 Internals Address List

A community list of HP 48S/SX ROM entry points and system RAM locations,
dated April 2, 1991, in two sortings: `byfunction` and `byaddress` (same
directory, `READ.ME` explains). Paul Dale's additions were found on ROM
revision E. Only the RAM part (lines 6761-6839 of `byaddress`, "(RAM)"
entries) was read, for the system pointers of saturnus iteration 12a.

- RAM base #70000; the RPL save area (lines 6775-6800):
  `DynamicStart` #7056A, `HeapStart` #7056F, `FramePtr` (saved B, return
  stack) #70574, `TOS` (saved D1, the data stack pointer) #70579, `EOS`
  (bottom of the stack, which grows down) #7057E, local variables at
  address #70583, loop context at #70588, `homedir` #70592,
  `end_homedir` #70597, `cur_dir` (current directory) #7059C, `tmpdir`
  at #705A1, alarm list #705AB, `TOH` (saved D0, the RPL instruction
  pointer) #705B0.
- Free memory (saved D) #7066E, last error #70673, stack size #7069F.
- `System_flags` #706C5 and `User_flags` #706D5 (line 6815-6818).
- Display: menu GROB pointer #70551, stack GROB pointers #70556/#7055B,
  PICT pointer #70565; `Keybuf` #704EA.

All the pointers used for the memory view were confirmed on ROM J in
saturnus: [[hardware/hp48-system-ram]]. The list covers the S/SX only.
