---
title: "HP 48 I/O Technical Interfacing Guide"
type: source
authors: ["Hewlett-Packard"]
year: 1990
raw: "raw/hp48-internals/io-guide/io2.txt"
status: digested
tags: [hp48, uart, serial, ir, kermit, hardware]
---

# HP 48 I/O Technical Interfacing Guide

HP's own 1990 guide (dated 1990-06-14) to the HP 48SX serial and IR hardware.
The plain-text `io2.txt` was read in full; `48techni.pdf` (11 pages) and the
troff source in `ioguide-troff/` are the same document. Cite by section
number.

## Content

| Section | Content |
| --- | --- |
| 1 | serial is full-duplex UART with RS-232 levels; IR is half-duplex; received bytes go to a 255-byte buffer under interrupts when the port is open |
| 2 | 1200-9600 baud; parity none/odd/even/mark/space; XON/XOFF; double-buffered RX and TX sharing one baud generator |
| 2.1 | HP 82208A (PC) and 82209A (Mac) cable pinouts |
| 2.2 | frame format; HP sends slightly more than 2 stop bits; line polarity and idle state |
| 2.3 | electrical specs: TX swing +-3.0 V min into 3 kOhm/500 pF; RX thresholds; 2.5% bit-width tolerance |
| 2.4 | receiver model (16x oversampling, half-bit start filter, RBR/RBF/RER, overrun and framing errors, break), transmitter (11.375 bit times per frame, 844 char/s at 9600) |
| 3 | IR: 2400 baud only, 52 us pulse per 0 bit, receiver latch IRE, specs (940 nm, 2 in max), circuits |
| 4 | Kermit: use type 3 CRC on IR; server mode uses "I" packets; no XON/XOFF; peer needs a 14-byte buffer minimum |
| 5 | non-Kermit I/O: 255-byte limit, inter-frame gap rule, timer interrupts cause overruns, garbage FF bytes on IR |

The guide does **not** describe the binary object format.

## Reliability

Authoritative (HP), but written for the 48SX in 1990. It describes the
behaviour of the ROM and UART as a model; G-series and 49G differences are
not covered. Feeds [[hardware/uart]], [[protocols/kermit-hp]].
