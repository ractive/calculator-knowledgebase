---
title: "Does C=IN really only work from an even address?"
type: question
status: answered
tags: [saturn, cpu, keyboard]
---

# Does C=IN really only work from an even address?

Mastracci says C=IN (803) "can only be executed from an even-nibble address"
and that ROM code calls it through the =CINRTN entry point (src:
[[sources/mastracci-saturn-guide]] 3.4, 6.3). A=IN (802) has no such note.

- What goes wrong on an odd address: wrong value, or a hang?
- Does any ROM path depend on the failure mode, so an emulator must
  reproduce it?

Check: [[sources/saturn-tutorial]], [[sources/voyage-48gx]],
[[emulators/emu48]] CHANGES.

## Contradiction

Sylvester says the opposite: C=IN "must be executed on an odd memory address
due to a bug" (src: [[sources/keyb49-sylvester]] 1.1). Both route the call
through CINRTN. Parity is therefore disputed.

The tutorial sides with Mastracci: "You cannot use A=IN or C=IN instructions
unless they are located on an even address", and gives AINRTN/CINRTN at #0115A
/ #01160 (48) and #0020A / #00212 (49) (src: [[sources/saturn-tutorial]]
p. 94). Two sources against one; the failure mode is still undocumented.

## Answer (working)

Even. Mastracci, the tutorial and Voyage ("A=IN et C=IN fonctionnent
incorrectement lorsqu'elles se trouvent sur des adresses impaires", src:
[[sources/voyage-48gx]] p. 102) agree; Sylvester is the lone dissent. The
failure mode itself is undocumented; an emulator can ignore the restriction.

Emu48 reproduces the bug: A=IN and C=IN only work at even addresses, except
when executed from the I/O register window, where they also work at odd ones
(src: [[emulators/emu48]] CHANGES SP1, SP36).
