---
title: "Flipse, External serial buffer for the HP49"
type: source
authors: ["Marcel Flipse"]
year: 2000
raw: "raw/saturn-hardware/hp49-38-39/buf49/Buf49.PDF"
status: digested
tags: [serial, uart]
models: [49g]
---

# Flipse, External serial buffer for the HP49

Six-page note (2000) with scope photos and a PCB for an external buffer.

- HP49G units with serial ID below 94xxxxxxxx have a hardware bug in the
  serial output buffer (p. 1).
- Faulty TX swings only -3.6 V to +4 V into a 3.3 kOhm load; into 100 kOhm it
  swings higher; after the buffer it swings -5 V to +8.7 V (p. 2).
- The buffer is powered from the PC: negative rail from TX, positive from
  RTS and DTR. "Fortunately HPCOMM sets these signal[s] high"; other terminal
  programs must enable hardware handshaking; RTS and CTS are bridged (p. 1).

Relevance to satx: a host program should assert DTR and RTS so that
port-powered adapters work ([[protocols/kermit-hp]], [[hardware/hp49g]]).
