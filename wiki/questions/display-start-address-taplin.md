---
title: "What is Taplin's usual display address #F097C?"
type: question
status: open
tags: [saturn, display, 48sx]
---

# What is Taplin's usual display address #F097C?

Taplin says the display base register is usually #F097C, or #F09BC with the
equation card, and to "type addresses in backwards" (src:
[[sources/hdwreg-taplin]]). Neither #F097C nor its nibble reversal #C790F is in
the 48SX RAM window at #70000. Low priority; the base is ROM-chosen and an
emulator need not know it.
