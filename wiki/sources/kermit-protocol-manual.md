---
title: "da Cruz, Kermit Protocol Manual, 6th edition"
type: source
authors: ["Frank da Cruz"]
year: 1986
raw: "raw/protocols/kproto.pdf"
status: digested
tags: [kermit, protocol, transfer, hptx]
---

# da Cruz, Kermit Protocol Manual, 6th edition

The Columbia University protocol manual, 6th edition (June 1986), 75-page
text PDF. Cite by the manual's own page numbers (the "Kermit Protocol Manual
Page N" headers) and section numbers.

## Read

| Section | Pages | Content |
| --- | --- | --- |
| 1.4-1.7 | 4-5 | numbers, control characters, tochar/unchar/ctl |
| 2 | 7-8 | environment requirements, text vs binary files |
| 3.1-3.8 | 9-14 | transaction sequence, timeouts/NAKs/retries, errors, heuristics, file names, flow control, basic state table |
| 4 | 15-17 | packet format, terminator, prefixing, type 1 check |
| 5 | 19-21 | Send-Init fields and defaults |
| 6.1-6.4 | 23-31 | 8th-bit and repeat prefixing, server mode and commands, I packet, block checks 2 and 3, interrupting a transfer |
| 6.5 | 31-35 | attributes packet (skimmed) |
| 7.1 | 41-43 | long packets |
| App. I | 63-64 | packet layout summary |

Skimmed: 6.6 advanced state table (p. 36-39), 7.2 sliding windows (p. 43-52;
HP calculators do not use windows), 8 commands and 9 program writing (p.
53-62).

## Reliability

The protocol definition itself. Implementations, including the HP
calculators, implement subsets; HP specifics are on
[[protocols/kermit-hp]]. Facts are on [[protocols/kermit]].
