---
title: "MAKEROM: HP's library generator manual (HP 48 SDK)"
type: source
authors: ["Hewlett-Packard"]
year: 1993
raw: "raw/saturn-hardware/hp48-sdk-1993/MAKEROM.TXT"
status: skimmed
tags: [rpl, library, xlib]
models: [48sx, 48gx]
---

# MAKEROM: HP's library generator manual

The manual of MAKEROM, the SDK tool that turns compiled RPL modules into
a library object, from the 1993 repackaging of HP's 48 tools (plain text,
page numbers in the file). Read for the parts of a library (saturnus
iteration 12c, 2026-10-05): pages 1-2 and 10-12.

- A library holds its name, a **hash table** with the names of its user
  words, a **link table** in routine-number order with the execution
  address of every routine, an optional **message table** for the error
  handler, a configuration routine run at every system halt, and a
  checksum (p. 1).
- A ROM pointer (XLIB name, prolog DOROMP) has a 6-nibble body: the
  library (rom) id and the routine number. Calling it makes the system
  look the library up and transfer to the routine's address (p. 1).
- Routines declared `NULLNAME` get a link table entry but no hash table
  entry (p. 2, p. 12): unnamed commands exist.

The manual does not give the tables' binary layout; that is on
[[protocols/rpl-libraries]], observed in the ROMs.
