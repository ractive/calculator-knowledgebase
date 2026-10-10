---
title: "Which matrix position is left shift and which right shift?"
type: question
status: answered
tags: [saturn, keyboard]
models: [48sx, 48gx]
---

# Which matrix position is left shift and which right shift?

Mastracci's scan table puts left shift at OUT #002 / IN #20 and right shift at
OUT #004 / IN #20; his connector table (from Teuwen) puts shift-left on
OUT #004 and shift-right on OUT #002 (src: [[sources/mastracci-saturn-guide]]
4.9, 5.8). See [[hardware/keyboard]]. Resolve with
[[sources/keyboard-ervin]], [[sources/kml20]] OutIn codes and
[[sources/voyage-48gx]].

## Evidence so far

- Ervin's Figure 1 labels the OUT #004 / IN #20 key "yel" and OUT #002 /
  IN #20 "blu" (src: [[sources/keyboard-ervin]] 3). On the 48SX the left
  shift is the orange key ([[hardware/keyboard]], "Shift colours and
  printed labels"), so left shift = #004, agreeing with
  5.8 / [[sources/teuwen-gx-hardware]] 9 and not with the Mastracci 4.9 table.
- Ervin also gives the KeyState order "... MTH, 4, 5, 6, x, blu, A, 1 ..." with
  "yel" next to MTH (src: [[sources/keyboard-ervin]] Figure 2), consistent
  with yel on the #004 row.

## Answer

Left shift is OUT #004 / IN #20, right shift OUT #002 / IN #20 on the HP48
(src: [[sources/saturn-tutorial]] p. 136; [[sources/keyboard-ervin]] Figure 1;
[[sources/teuwen-gx-hardware]] 9). Mastracci's 4.9 table has them swapped.
Also check against [[sources/kml20]] OutIn codes when ingested.
