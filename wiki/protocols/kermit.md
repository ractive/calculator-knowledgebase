---
title: "Kermit protocol (generic)"
type: protocol
status: draft
sources: ["[[sources/kermit-protocol-manual]]"]
tags: [kermit, protocol, transfer, satx]
---

# Kermit protocol

The protocol as defined by Columbia; what the HP calculators actually do is
on [[protocols/kermit-hp]]. All citations: [[sources/kermit-protocol-manual]]
(page, section).

## Encoding helpers

- tochar(x) = x + 32, unchar(x) = x - 32, ctl(x) = x XOR 64 (p. 5, 1.6).
- A control character is any byte whose low 7 bits are 0-31 or 127; printable
  is 32-126 (p. 5, 1.5).

## Packet

```text
MARK  tochar(LEN)  tochar(SEQ)  TYPE  DATA...  CHECK  [terminator]
```

- MARK: normally SOH (Ctrl-A); the only control character inside a packet
  (p. 15, 4.1; p. 9, 3).
- LEN: number of characters after LEN, up to and including CHECK; 0-94, so a
  normal packet is at most 96 characters (p. 15, 4.1).
- SEQ: sequence number mod 64; an ACK or NAK carries the number of the packet
  it answers (p. 9, 3; p. 15).
- TYPE: one uppercase letter (table below).
- CHECK: 1, 2 or 3 characters over everything from LEN to the end of DATA,
  never including MARK (p. 15, 4.1; p. 29, 6.3).
- Terminator: any EOL after the check is outside the packet and not counted;
  default CR (p. 16, 4.2). Anything between packets is ignored (4.3).
- Control fields are never prefixed and never carry 8-bit data (p. 16, 4.4).

### Packet types

| Type | Meaning |
| --- | --- |
| S | Send-Init (parameters) |
| Y | ACK (data depends on what is acknowledged) |
| N | NAK (data always empty) |
| F | File header (file name) |
| D | Data |
| Z | End of file |
| B | Break, end of transaction |
| E | Error (text message) |
| A | File attributes (optional) |
| X | Text header, data goes to the screen |
| R | Receive-Init (server: send me these files) |
| I | Init-Info (parameter exchange without a transfer) |
| C | Host command |
| K | Kermit command |
| G | Generic command |
| Q, T | reserved for internal use (T often marks a timeout) |

(p. 15, 4.1; p. 25, 6.2.1)

## Block checks

- Type 1 (required): s = sum of the characters from LEN to end of DATA;
  check = tochar((s + ((s AND 192) / 64)) AND 63) (p. 15, 4.1).
- Type 2: low 12 bits of the same sum as two characters, tochar(bits 6-11)
  then tochar(bits 0-5) (p. 29, 6.3).
- Type 3: 16-bit CRC-CCITT, polynomial x^16+x^12+x^5+1, initial value 0, bits
  taken LSB first; sent as tochar(bits 12-15), tochar(bits 6-11), tochar(bits
  0-5). Per byte, process the low nibble then the high nibble with
  `crc = (crc >> 4) ^ (((crc ^ nibble) & 15) * 4225)`; mask off parity first
  if parity is in use (p. 29, 6.3). 4225 = #1081: this is the same CRC as
  the Saturn hardware CRC ([[hardware/crc]]), fed nibble-wise.
- When parity is used the 8th bit is excluded from all checks; on an 8-bit
  channel it is included (p. 16-17, 4.4).
- Switching rule: every transaction starts with type 1. A check type agreed
  in S or I applies only after the S/I and its ACK have been exchanged with
  type 1, so the first type 2/3 packet is F or X. Both sides revert to type 1
  after B or E is ACKed or the transaction aborts (p. 29-30, 6.3).
- Heuristics: a packet of type S is always type 1; a NAK has an empty data
  field, so its check type is unchar(LEN) - 2 (p. 30).

## Send-Init (S, I and their ACKs)

The data field of S, I, A and their ACKs is never prefix-encoded (p. 15,
4.1). Fields, each optional from the right, blank meaning default (p. 19-21,
5):

| # | Field | Encoding | Meaning | Default |
| --- | --- | --- | --- | --- |
| 1 | MAXL | tochar | longest packet I want to receive (LEN value, max 94) | 80 |
| 2 | TIME | tochar | seconds before you should time me out | 5 |
| 3 | NPAD | tochar | padding characters I need before each packet | 0 |
| 4 | PADC | ctl() | padding character | NUL |
| 5 | EOL | tochar | terminator I need | CR |
| 6 | QCTL | literal | control prefix I will use | `#` |
| 7 | QBIN | literal | `Y` will do 8th-bit prefixing if asked, `N` won't, or the prefix char I need (33-62, 96-126; `&` recommended) | space, none |
| 8 | CHKT | literal | `1`, `2` or `3`; used only if both agree, else 1 | `1` |
| 9 | REPT | literal | repeat prefix (`~` normal); used only if both send the same | none |
| 10 | CAPAS | tochar bitmask, bit 0 = another CAPAS byte follows | #3 attributes (bit 3), #4 sliding windows (bit 2), #5 long packets (bit 1) | 0 |
| +1 | WINDO | tochar | window size 0-31 | 0 |
| +2, +3 | MAXLX1, MAXLX2 | tochar | extended max length = 95 x MAXLX1 + MAXLX2 | none |

There is exactly one S and one ACK; nothing is negotiated further. Parity is
not a field: it must be known before the first packet (p. 21).

## Data prefixing

- Control characters in DATA become QCTL + ctl(c); the prefix characters
  themselves (by low 7 bits) are prefixed with QCTL; any character following
  QCTL that is not in the control range is taken literally (p. 16, 4.4).
- On an 8-bit channel the 8th bit is kept on the prefixed character; e.g.
  Ctrl-A with bit 7 set is `#` then ctl(^A) with bit 7 set (p. 16).
- 8th-bit prefixing (QBIN, normally `&`) is used only when one side asked for
  it with a character and the other agreed with `Y` or the same character
  (p. 19-20, 5; p. 23, 6.1).
- Repeat count: REPT, tochar(count 1-94), then the (possibly prefixed)
  character; runs over 94 are split. Order is repeat, 8th-bit, control,
  character, e.g. `~(&#A`; 120 NULs encode as `~~#@~:#@` (p. 23-24, 6.1).
- A prefixed sequence is never split across packets (p. 24).
- Text files are sent as 7-bit ASCII lines ending in CRLF (`#M#J`); binary
  files as raw bytes with no conversion (p. 8, 2.2).

## Transaction

1. Sender: S. Receiver: Y with its parameters.
2. F with the file name; Y (may carry the stored name).
3. Optional A packets, then D packets, each ACKed before the next.
4. Z at end of file (data `D` = discard this file); Y.
5. Repeat 2-4 per file; B at the end; Y.

Sequence numbers start at 0 with S and increase mod 64 (p. 9, 3). State
table on p. 14 (3.8).

- Interrupting: sender sends Z with data `D`; receiver puts `X` (this file) or
  `Z` (whole batch) in the data of a D's ACK (p. 30-31, 6.4).
- Errors: either side may send E at any time; both stop (p. 10, 3.3).

## Timeouts, retries and heuristics

- Set a timeout on every read; on timeout the sender resends; the receiver
  re-ACKs the last packet (preferred) or NAKs the expected one. Retry limit
  around 5 (p. 10, 3.2).
- Only one side need time out; if both do, use different intervals (p. 10).
- A NAK for packet n+1 is an ACK for n. Duplicate packet n: ACK again and
  discard. Ignore duplicate ACKs (p. 11, 3.4).
- Flush the input buffer before sending the first packet and before each
  send, to avoid stacked-up NAKs from a waiting peer (p. 11, 3.4).
- File names: send "NAME.TYPE", no path, one dot, uppercase letters and
  digits (p. 11-12, 3.5).
- XON/XOFF may be used by the host; Kermit does not need it (p. 12, 3.7).

## Server mode

- A server waits in "command wait" for packet 0; after every transaction,
  successful or not, it returns there and resets the sequence number to 0.
  It may send periodic NAKs for packet 0, with type 1 checks, at a long
  interval (30-60 s) (p. 24, 6.2; p. 26, 6.2.2).
- Unknown commands get an E packet "Unimplemented Server Command" and the
  server stays in command wait; only GL or GF end server mode. Every server
  should implement S, R, and GL and/or GF (p. 25, 6.2.1).
- R (GET): the server answers with S (not an ACK) and sends the files, or
  with E. R is never ACKed (p. 26, 6.2.3).
- K: execute a command in the server's own language (typically SET); ACK on
  success, E otherwise (p. 26, 6.2.4).
- C: host command; output as a short or long reply (p. 28, 6.2.7).
- Short reply: one ACK whose data is the answer text. Long reply: F or X or
  S-then-F/X, D packets, Z, B, like a transfer. A long reply starts with S
  unless an I exchange already happened and type 1 checks are in use; with
  type 2/3 checks it must start with S (p. 26-27, 6.2.5; p. 30).
- I: like S but does not start a transfer; it is a complete transaction and
  leaves the sequence number at 0. User Kermits that send I must tolerate an
  E reply (p. 28-29, 6.2.8).
- G (generic): first data character is the subcommand; operands are
  length-prefixed with tochar(len), and the whole data field is
  prefix-encoded after that (p. 25, 6.2.1):

| G sub | Command |
| --- | --- |
| I | login |
| C | change directory |
| L | logout / bye |
| F | finish (leave server mode) |
| D | directory |
| U | disk usage |
| E | erase |
| T | type |
| R | rename |
| K | copy |
| W | who |
| M | message |
| H | help |
| Q | status |
| P | program |
| J | journal |
| V | variable set/query |

## Long packets

- Negotiated by CAPAS bit #5 plus MAXLX1/MAXLX2 (default 500 if the bit is set
  but the length fields are missing); maximum 9024 (p. 41, 7.1).
- In a long packet LEN is blank (tochar(0)); then LENX1, LENX2 and a header
  check HCHECK (type 1 check over LEN, SEQ, TYPE, LENX1, LENX2) follow;
  length = 95 x unchar(LENX1) + unchar(LENX2) (p. 42). unchar(LEN) of 1 or 2
  is an error.
- Normal and long packets may be mixed once agreed; reduce size on errors
  (p. 42-43).

## Not used by HP calculators

Sliding windows (p. 43-52) are not used by the HP 48 Kermit (src:
[[sources/hp48-faq]] 6.13). Attributes (p. 31-35) are not documented for
HP; check [[sources/hp48-kermit-hints]].
