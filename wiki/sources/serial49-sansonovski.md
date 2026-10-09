---
title: "Neves, do Lago and Sansonovski, Circuit to correct the HP49G serial port signal"
type: source
authors: ["Carlos Antonio Neves", "Claudimir Lucio do Lago", "Tacio Philip Sansonovski"]
year: 2000
raw: "raw/saturn-hardware/hp49-38-39/serial49/serial49.pdf"
status: digested
tags: [hp49g, serial, uart]
---

# Neves, do Lago and Sansonovski, Circuit to correct the HP49G serial port signal

Three pages in Portuguese (hpclub do Brasil, 2000-02-06). Facts in English:

- HP49G units before serial ID 94xxxxxx have a design error in the TX signal:
  it fails with most Pentium-or-older PCs but works with Pentium II or later
  (p. 1).
- The faulty TX level falls in a ramp instead of a clean edge (Figure 1); an
  LF356 op-amp comparator with a zener reference restores it (p. 1-2).
- A variant powered from the PC's RTS and TX works only with Kermit, not
  XModem: XModem sends more data PC-to-calculator, leaving too little time to
  recharge the supply capacitors. RTS is about 0 V until the PC opens the port
  (p. 2-3).
- XModem is faster than Kermit on the 49G (p. 3).

Feeds [[hardware/hp49g]] and [[protocols/xmodem-hp]].
