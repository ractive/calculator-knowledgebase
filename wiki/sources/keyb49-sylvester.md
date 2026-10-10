---
title: "Sylvester, HP 49G Keyboard Hardware Note v0.2"
type: source
authors: ["Igor Andrade Sylvester"]
year: 2003
raw: "raw/saturn-hardware/hp49-38-39/keyb49/keyb49.txt"
status: digested
tags: [keyboard]
models: [49g]
---

# Sylvester, HP 49G Keyboard Hardware Note v0.2

HP49G keyboard connector pins and the full OUT/IN matrix (v0.2, 2003).

- 18-pin connector; OUT lines Y1-Y8 (#001-#080), IN lines A1-A8 (#0001-#0080);
  ON on its own pin, IN #8000 for any OUT.
- Full key table (see [[hardware/keyboard]] for the 49G matrix). Up to 8 keys
  readable per OUT value.
- States that C=IN "must be executed on an odd memory address due to a bug",
  hence the CINRTN ROM routine.
- OUT lines are TTL; to fake a key, connect IN to OUT through about 8 kOhm.

Contradicts [[sources/mastracci-saturn-guide]] 3.4 (even address); see
[[questions/c-equals-in-even-address]].
