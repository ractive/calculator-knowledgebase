---
title: "Is #100 bits 0-2 a horizontal or vertical display offset?"
type: question
status: answered
tags: [saturn, display]
---

# Is #100 bits 0-2 a horizontal or vertical display offset?

Mastracci calls #100 bits 0-2 "the eight-pixel vertical offset of the
display" (src: [[sources/mastracci-saturn-guide]] 4.2). A 3-bit value fits a
pixel shift within one byte of a row, i.e. horizontal scrolling. Confirm from
[[sources/voyage-48gx]] or [[sources/screen-brittenson]].

## Answer

Horizontal. #100 bits 0-2 shift the main display area left by up to 7 pixels;
larger scrolls move the start address by 2 nibbles per 8 pixels (src:
[[sources/saturn-tutorial]] p. 167). Taplin's "useful for smooth scrolling"
fits (src: [[sources/hdwreg-taplin]]).
