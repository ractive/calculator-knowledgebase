---
title: "Which models have a clock, and how each is set"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g, 42s, 27s, 17bii, 17b, 28s, 19bii]
status: draft
sources:
  - "[[sources/hp48sx-om]]"
  - "[[sources/hp48g-aur]]"
  - "[[sources/hp38g-ug]]"
  - "[[sources/hp39g40g-ug]]"
  - "[[sources/hp27s-om]]"
  - "[[sources/hp17bii-om]]"
  - "[[sources/hp17b-om]]"
  - "[[sources/hp28s-rm]]"
  - "[[sources/hp19bii-om]]"
tags: [clock, timers]
---

# Which models have a clock, and how each is set

Every model here keeps the date and time except the 42S and the 28S. How the owner
sets it, per model, from the manuals; what an emulator that sets the
clock for the user by keys has to know is under each. The hardware that
keeps the time: [[hardware/timers]] (48, 49, aplet models) and
[[questions/pioneer-clock-alarm-hardware]] (27S, 17B, 17BII; the 19BII
has not been traced).

| Model | Clock | Set by | Source |
| --- | --- | --- | --- |
| 48SX | yes | TIME SET menu, or `→TIME` (H.MMSS) and `→DATE` | [[sources/hp48sx-om]] ch. 24, "Setting the Date and Time" (p. 24-2) |
| 48GX | yes | the same commands | [[sources/hp48g-aur]], `→DATE` and `→TIME` |
| 49G | yes | the same commands (seen in saturnus with ROM 2.10; the manual's page not checked) | - |
| 38G | yes | store into the `TIME` and `DATE` variables in HOME; `DISPTIME` shows them | [[sources/hp38g-ug]] 8-22 (`DISPTIME`) |
| 39G, 40G | yes | the same: `Time` and `Date` are HOME's Modes variables | [[sources/hp39g40g-ug]] 15-26 (`DISPTIME`) and the variables table |
| 42S | no | - | see below |
| 28S | no | - | its reference manual has no time or date command; RDZ with 0 seeds from "the system clock" ([[sources/hp28s-rm]], RDZ) |
| 27S | yes | TIME application (shift ÷), SET menu | [[sources/hp27s-om]] p. 137-139 |
| 17BII, 17B | yes | MAIN, TIME, SET menu | [[sources/hp17bii-om]], [[sources/hp17b-om]] p. 132 |
| 19BII | yes | MAIN, TIME, SET menu, as the 17BII | [[sources/hp19bii-om]] p. 176-177; [[hardware/hp19bii]] "Clock" |

## 48SX, 48GX, 49G

- `→TIME` takes the time as H.MMSS in 24-hour form; `→DATE` takes
  MM.DDYYYY, or DD.MMYYYY when flag -42 is set, and only dates from
  January 1, 1991 to December 31, 2090 (src: [[sources/hp48g-aur]],
  `→DATE` remarks). Both take their argument off the stack and leave
  nothing, so typing `H.MMSS →TIME MM.DDYYYY →DATE` and ENTER leaves the
  stack as it was.
- With flag -51 set the fraction mark is a comma, so the numbers are
  typed with one (`14,0530`).
- The 49G in algebraic mode takes `→TIME(14.0530)` and `→DATE(…)` as
  algebraic calls; each leaves a line of history, which the backspace
  key drops (seen in saturnus, ROM 2.10).
- Read back: `TIME` and `DATE` push the current values (seen in
  saturnus: `TIME` gives H.MMSSss, `DATE` 10.132026 for 13 October
  2026 with flag -42 clear).

### 49G: setting the clock from the Kermit server

Seen in saturnus with ROM 2.15 and 2.10 (2026-10-19), by tracing the ROM
while it ran `→DATE`. saturnng, an independent emulator, gives the same:
`1.012030 →DATE DATE` sent to its Kermit server returns 61.877009, as in
saturnus (2026-10-19). Not checked on a real 49G.

- Both the 48GX (ROM R) and the 49G keep the clock as a 13-nibble tick
  count in RAM (#80058) that is the time of the next TIMER2 event:
  `TICKS` is that count minus TIMER2 read as a signed 32-bit number. To
  set a new time, the ROM stores a new next-event time there and loads
  TIMER2 with the next-event time less the new time (see
  [[hardware/timers]]).
- ROM R always takes the new time plus #7E900000 ticks (72 hours) as the
  next event. The 49G ROM takes the 13-nibble value at #80069 instead
  when it is not zero: a deadline five minutes (#258000 ticks) ahead,
  which is armed while the Kermit server runs commands (seen from its
  first command after `SERVER` on). The difference then has to fit TIMER2's 32 bits, and it does
  not for a move of more than 2^31 ticks (about 3 days): it wraps.
  `1.012030 →DATE DATE` sent as server commands to a 49G showing 1 January
  1991 gives 61.877009, and `10.052026 →DATE` sets 1 March 1991.
- Once that deadline has passed it reads 0, and later moves are exact:
  `707788800. CLKADJ` (a day forward, which passes it) and then
  `-707788800. CLKADJ` leave the clock where it was, after which
  `1.012030 →DATE` sets 1 January 2030. From the keyboard (the same
  `→DATE` in a program run from the VAR menu) a move of 39 years is
  exact.

## 38G, 39G, 40G

- The guides document setting the clock by storing into the variables:
  M.DDYYYY into DATE and H.MMSS into TIME, `5.152000 ▶ DATE` (src:
  [[sources/hp39g40g-ug]] 15-26; [[sources/hp38g-ug]] 8-22, its example
  with 1995). No other date order is documented.
- In HOME, STO▶ is the first menu key; the names may be typed in
  capitals (`10.1530▶TIME` was taken on the 39G ROM and the 38G ROM in
  saturnus). Recalling `TIME` gives H.MMSSss of the current time.
- Each store adds two lines to the HOME history (the input and the
  value). With the highlight bar (▲), DEL deletes the highlighted line
  (src: [[sources/hp39g40g-ug]] 1-23); in saturnus ▲, DEL, DEL removes
  the store's two lines and ▼ leaves the history, as it was before.
  ANS keeps the stored value.
- After a cold start the "Memory Clear" box is dismissed by HOME as well
  as by OK (39G ROM in saturnus).

## 42S

No clock. The 42S keyboard and menus have no time or date function, and
its ROM holds none of the strings the clock models' ROMs use for it:
searched in @ractive's 42S image (revision C), `DATE` and `APPT` do not
occur, while `SOLVER` does; the 27S and 17BII images hold `DATE` and
`APPT` (black-box search of the ROM bytes, 2026-10-13). The 42S
manual is not in `raw/`; its table of contents would settle it beyond
doubt.

## 27S, 17BII, 17B, 19BII

The 19BII's TIME and SET menus are the 17BII's (src:
[[sources/hp19bii-om]] p. 175-177); its own addresses are below.

- TIME is shift ÷ on the 27S and the fourth label of MAIN (shift EXIT)
  on the 17B and 17BII; SET is TIME's fourth label. SET's labels are
  DATE, TIME, A/PM, M/D, 12/24, HELP: a number keyed in the calculator
  line and DATE or TIME sets it (src: [[sources/hp27s-om]] p. 137-138,
  Table 7-2; [[sources/hp17bii-om]] p. 132).
- The date is keyed in the calculator's own format, MM.DDYYYY or
  DD.MMYYYY, as M/D last chose; the time as HH.MMSS, and in 12-hour
  format A/PM corrects the half of the day (same pages).
- Seen in saturnus (owner's ROMs, 2026-10-13): in 12-hour format the
  27S takes `21.0830` TIME as 9:08:30 PM, but whether a morning hour
  keeps a PM already set was not tried; pressing 12/24 to 24-hour form,
  keying the time and pressing 12/24 again avoids the question.
- Seen in saturnus: one RAM nibble holds both SET settings, bit 0 set
  for day.month.year and bit 2 set for 24-hour time: #50025 on the 27S, the
  17BII's #5002B, the 17B's #5002A (international images) and the
  19BII's #C41E1, found by pressing M/D and 12/24 and comparing RAM. Read
  only; nothing else about it is known.
- An international 17B or 17BII after a memory loss asks for its
  language first and takes no other key (shift EXIT does not leave it).
  Seen in saturnus: the nibble at #50092 (17BII) or #50091 (17B) reads 0
  there and 2 in every menu tried after it. Every 19BII image known asks
  too; its nibble is #C402B, with the same values (seen in saturnus; its
  menu keys pick the languages, English the sixth).
- The number keyed stays in the calculator line afterwards, so what it
  showed before is replaced, as when the owner sets the clock by hand.
