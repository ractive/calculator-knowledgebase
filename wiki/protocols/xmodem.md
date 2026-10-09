---
title: "XMODEM, XMODEM-CRC, XMODEM-1K and YMODEM (generic)"
type: protocol
status: draft
sources: ["[[sources/ymodem-reference]]", "[[sources/zmodem]]"]
tags: [xmodem, ymodem, protocol, transfer, hptx]
---

# XMODEM family

Generic protocol facts. HP calculator behaviour is on [[protocols/xmodem-hp]]
and [[protocols/xserv]]. All citations: [[sources/ymodem-reference]] (section,
page).

## Control bytes

SOH #01 (128-byte block), STX #02 (1024-byte block), EOT #04, ACK #06,
NAK #15, CAN #18, `C` #43 (7.1 p. 20; 4.3 p. 11).

## Line

8 data bits, no parity, 1 stop bit; data are fully transparent, no control
character is special inside a block (7.2 p. 20).

## Block

```text
SOH | blk | 255-blk | 128 data bytes | check
```

- blk starts at 1 and wraps #FF to #00 (not to 1); the second byte is its
  one's complement (7.2 p. 21).
- Checksum mode: one byte, the sum of the 128 data bytes mod 256 (p. 21).
- CRC mode: two bytes, high byte first. CRC-16 with polynomial
  x^16+x^12+x^5+1 processed MSB first (the 0x1021 form), initial 0, over the
  data bytes only (8 p. 24-25, Figure 12). Note: this is not the same bit
  order as Kermit's type 3 CRC or the Saturn CRC, which are LSB-first
  ([[protocols/kermit]], [[hardware/crc]]).
- 1K blocks start with STX and carry 1024 data bytes; the block number still
  increments by one; a receiver must accept any mix of 128 and 1024-byte
  blocks; the sender must not change the size of a block that has not been
  ACKed (4.3 p. 11-12).
- The last block is padded to full size (CP/M uses ^Z); there is no short
  block, so a file can grow by up to one block unless YMODEM sends the length
  (7.2 p. 20-21; 4.3 p. 12).

## Flow

1. Receiver starts: NAK requests checksum mode, `C` requests CRC mode. The
   receiver retries every 10 s (NAK) or a few times at 3 s (`C`) before
   falling back to NAK (7.3.2 p. 21; 8.2.1 p. 25-26).
2. Sender sends blocks; receiver ACKs or NAKs each. A repeat of the previous
   block is ACKed and discarded; any other unexpected block number is a
   fatal loss of sync, abort with CAN (7.3.2 p. 21-22).
3. Sender sends EOT, resending until ACKed (7.3.3 p. 22; 2 p. 4: up to ten
   times).

- Sender: starts in checksum mode; a `C` received before the first NAK/ACK
  switches it to CRC mode; extra `C`s before the first ACK are treated as
  NAK; after the first ACK `C` is ignored. A sender must never use CRC unless
  the receiver asked for it (8.2.3 p. 26; 4.3 p. 12).
- Timeouts: receiver 10 s waiting for a block, 1 s between characters
  within a block (7.3.2 p. 21; 7.4 p. 22). All errors retried 10 times (7.3.1
  p. 21).
- Before NAKing, the receiver waits for the line to be silent (1 s) so the
  sender sees the NAK (7.4 p. 23).
- Abort: two consecutive CANs; implementations send several (4.1 p. 10).

## YMODEM batch

- Receiver sends `C`; sender sends block 0 containing the file name as a
  NUL-terminated string, then optionally the length in decimal, a space, the
  modification time in octal seconds since 1970, a space, the mode in octal,
  a space, a serial number; the rest NUL. Receiver ACKs block 0 and sends `C`
  again to start the file proper as in XMODEM-CRC (5 p. 13-15).
- The length lets the receiver drop the padding of the last block (5 p. 14).
- After the file's EOT is ACKed the receiver asks again with `C`; an empty
  block 0 (all NUL) ends the batch (5 p. 15-16).
- Minimum requirements: name in block 0, CRC in response to `C`, accept mixed
  block sizes, EOT up to ten times, empty name ends the session (2 p. 4).

## ZMODEM

ZMODEM (Forsberg 1988) is a streaming successor that fixes XMODEM's short
blocks, unprotected single-byte control messages, lack of file names and
attributes, and need for full 8-bit transparency (src: [[sources/zmodem]]
section 2). No HP calculator in scope implements it, and the HP48 FAQ notes
the HP48's small input buffer makes it hard (src: [[sources/hp48-faq]] 6.13).
Not needed for hptx.
