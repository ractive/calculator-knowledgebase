---
title: "HP 48 FAQ 4.62"
type: source
authors: ["André Schoorl"]
year: 2000
raw: "raw/hp48-internals/faq/48faq.txt"
status: digested
tags: [hp48, faq, transfer, object-format, kermit]
---

# HP 48 FAQ 4.62

The comp.sys.hp48 FAQ, version 4.62 (2000-04-14), text edition; the HTML
edition in `faq/html/` is the same content. Cite by FAQ section number. Read
for I/O and object format only, per the brief.

## Sections read

| Section | Topic | Fed into |
| --- | --- | --- |
| 10.1 | new G/GX commands incl. XSEND, XRECV (binary only) | [[protocols/xmodem-hp]] |
| 10.1 | FLAGS: flags unused on the S/SX that the G/GX uses, -30 dropped; Variable Browser new | [[hardware/system-flags-48sx]] |
| 4.12 | user flags 1-5 shown at the top of the screen | [[hardware/system-flags-48sx]] |
| 2.14 | HP49G overview: no IR, serial Kermit and XModem (128 checksum, 1K, 1K CRC), 9600 bps, "15360 bps internally" | [[hardware/hp49g]], [[protocols/xmodem-hp]] |
| 3.1, 3.2 | S/SX vs G/GX: RAM, ROM, 4 MHz vs 2 MHz, ~40% faster | model pages |
| 3.4 | `#30794h SYSEVAL` returns "HPHP48-x", x = ROM revision | [[protocols/hp-object-format]] |
| 3.5 | ROM bugs incl. XRECV needing twice the file size free before rev. R | [[protocols/xmodem-hp]] |
| 4.1 | BYTES checksum: 16-bit CRC | [[hardware/crc]] |
| 4.6, 4.7 | ON-key combinations, self-tests; test results go out the serial port at 9600 8N1 regardless of IOPAR | [[hardware/uart]] |
| 4.19 | right-shift right-arrow starts Kermit SERVER on the G/GX | [[protocols/server-commands]] |
| 6.4-6.18 | cables, IR range, IrDA, binary vs ASCII transfer, "HPHP48-" strings, the `%%HP:` ASCII header, Kermit slowness, XRECV bug, transfer tips, pinout | [[protocols/kermit-hp]], [[protocols/hp-object-format]] |
| 6.23, 6.26, 6.27 | Invalid card data, covered ports, 64 Hz flicker | model pages |
| 7.6 | ASC format (\->ASC / ASC\->) | [[protocols/hp-object-format]] |
| 8.13 | WSLOG codes | [[hardware/interrupts]] |
| 8.18 | GROB and binary object format, HPHP48-x header | [[protocols/hp-object-format]], [[hardware/display]] |
| 9.2 | OBJFIX repairs downloads with extra trailing bytes | [[protocols/hp-object-format]] |
| 12.1-12.3 | home-made cable; the HP48 plus cable is a DCE | [[protocols/kermit-hp]] |

## Reliability

A community FAQ, mostly reliable for user-visible behaviour, thin on
protocol detail. IOPAR and Kermit settings are better covered by
[[sources/hp48-kermit-hints]] and the HP manuals. Note: the 8.18 line-length
pseudo-code tests `nibs mod 4` where `width mod 4` is meant.
