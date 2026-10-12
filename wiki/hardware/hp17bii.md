---
title: "HP 17BII and HP 17B"
type: model
models: [17bii, 17b]
status: draft
sources:
  - "[[sources/hp17bii-om]]"
  - "[[sources/hp17b-om]]"
  - "[[sources/commons-pioneer-photos]]"
  - "[[sources/kml20]]"
  - "[[sources/emu42-pioneer-dump]]"
  - "[[sources/emu42-changes]]"
  - "[[sources/pioneers-rom-dumping]]"
tags: [model, pioneer, lewis, keyboard]
---

# HP 17BII and HP 17B

Pioneer-series business calculators: the 17B (1988) and its successor the
17BII (1990). Both are one [[hardware/lewis]] chip with outside ROM and
RAM, the board of the [[hardware/hp42s]], with a clock and appointment
alarms. The 17BII adds an RPN mode to the 17B's algebraic one; otherwise
the two differ in a few key labels. Not to be confused with the later
17bII+, which is not Saturn hardware ([[hardware/saturn-models]]).

Page numbers below are the manuals' own book pages, except "p. 2", the
keyboard figure, which is the PDF page.

## Summary

| Item | 17B | 17BII |
| --- | --- | --- |
| Chip | Lewis | Lewis ([[hardware/saturn-models]]) |
| ROM | 64 KB; international version 96 KB; revisions A, B | 64 KB; international 96 KB; revisions A, B ([[sources/emu42-pioneer-dump]] PIONEER) |
| RAM | 8 KB at #50000 | same ([[sources/pioneers-rom-dumping]]) |
| Display | 131 x 16 dots, two lines (calculator line, menu labels) | same ([[sources/hp17bii-om]] p. 17) |
| Contrast | 0-31, reset 22, keyboard 15-31 | same ([[sources/kml20]] LCD contrast table) |
| Entry logic | algebraic only | ALG or RPN ([[sources/hp17bii-om]] p. 33) |
| Clock | quartz, within three minutes a month at 25 °C | same ([[sources/hp17bii-om]] p. 218; [[sources/hp17b-om]] p. 218) |
| Power | three button cells (R44), ten-minute auto-off | same ([[sources/hp17bii-om]] p. 16, 213) |
| Emu42 model | `Model "T"` | `Model "U"` ([[sources/kml20]] Global) |

## ROM sizes

The English 17B and 17BII have 64 KB of ROM as the 42S. International
versions carry a second ROM: Gießelink's dump utility reads 96 KB
(768 blocks of 128 bytes) from them against 64 KB (512 blocks) for the
27S and 42S, and the memory scanner then shows two revision characters
instead of one (src: [[sources/emu42-pioneer-dump]] PIONEER). Emu42 from
v1.24 prepares every larger Pioneer with a 32 KB extra ROM for these
international 17B and 17BII (src: [[sources/emu42-changes]] v1.24).
Where that ROM sits is inferred: the 42S ROM's hook for a second ROM
at #20000 fits it ([[hardware/saturn-models]]). How the 96 KB image is laid
out as a file (one 96 KB file, or 64 KB plus 32 KB; packed or one nibble
per byte) is open: [[questions/hp17b-international-rom-image]].

The self-test pass message ends in "-E" on the units documented here
("OK-17B-E", "OK-17B II-E"); that the suffix marks the English ROM is a
guess (unverified).

## Keyboard matrix

Same positions as the 42S: 37 keys, six OUT lines by seven IN lines plus
the bottom-left key on the ON line (src: [[sources/kml20]] OutIn Codes
HP17B / HP17BII, one table for both models). OUT bit, IN value:

| IN \ OUT bit | 5 | 4 | 3 | 2 | 1 | 0 |
| --- | --- | --- | --- | --- | --- | --- |
| 64 | menu 1 | menu 2 | menu 3 | menu 4 | menu 5 | menu 6 |
| 32 | STO | RCL | % | DSP | PRT | EXIT |
| 16 | - | INPUT | +/- | ( | ) | ← |
| 8 | ▲ | - | 7 | 8 | 9 | ÷ |
| 4 | ▼ | - | 4 | 5 | 6 | × |
| 2 | shift | - | 1 | 2 | 3 | − |
| 1 | - | - | 0 | . | = | + |

CLR is "0 32768", IN bit 15. The menu keys are numbered from the left;
they are blank keys under the display's bottom line.

## Key labels

On the case, left to right. "Shifted" is the label printed above the key
in the shift colour; "lower" is a label printed below the key on the
faceplate. A dash means none. 17B and 17BII from each manual's keyboard
figure (src: [[sources/hp17b-om]] p. 2; [[sources/hp17bii-om]] p. 2, with
the RND label from p. 31-32 and photograph P1 of
[[sources/commons-pioneer-photos]]).

| Row | Key | Shifted 17B | Shifted 17BII | Lower |
| --- | --- | --- | --- | --- |
| 1 | six blank menu keys (printed ^) | - | - | - |
| 2 | STO | - | - | - |
| 2 | RCL | - | RND | - |
| 2 | % | MATH | MATH | - |
| 2 | DSP | MODES | MODES | - |
| 2 | PRT | PRINTER | PRINTER | - |
| 2 | EXIT | MAIN | MAIN | - |
| 3 | INPUT (two keys wide) | CLEAR DATA | CLEAR DATA | - |
| 3 | +/- | E | E | - |
| 3 | ( | - | R↓ | - |
| 3 | ) | - | x≷y | - |
| 3 | ← (backspace) | - | - | - |
| 4 | ▲, 7, 8, 9 | - | - | - |
| 4 | ÷ | 1/x | 1/x | - |
| 5 | ▼, 4, 5, 6 | - | - | - |
| 5 | × | y^x | y^x | - |
| 6 | shift (a blank coloured key), 1, 2, 3 | - | - | - |
| 6 | − | √x | √x | - |
| 7 | CLR | OFF | OFF | ON |
| 7 | 0 | MEM | MEM | - |
| 7 | . | SHOW | SHOW | - |
| 7 | = | LAST | LAST | 17BII: ENTER; 17B: - |
| 7 | + | x² | x² | - |

- RND: the 17BII manual's own keyboard photograph shows no label above
  RCL, yet its text uses a shifted RND and the 1999 unit in P1 has RND
  printed there (src: [[sources/hp17bii-om]] p. 2, 31-32). Early 17BII
  keyboards may lack the print (unverified).
- In RPN mode (17BII), = is ENTER, LAST is LAST X, ( is R↓ and ) is x≷y;
  INPUT also works as ENTER and ▼ as R↓ except in lists, ▲ rolls up
  (src: [[sources/hp17bii-om]] p. 251-252).
- The shifted labels name menus (MATH, MODES, PRINTER, MAIN, MEM) or
  functions. The rest of the functions sit in menus on the six menu keys.

## Colours

Approximate, sampled from photographs (P1 17BII, P2 17B in
[[sources/commons-pioneer-photos]]) and the 17BII manual's photograph:

| Part | 17BII | 17B |
| --- | --- | --- |
| Case body | #383228 dark brown | #1c1614 near-black brown |
| Keyboard faceplate | #433b32 | #1a1616 |
| Display surround | brushed champagne-gold band, #efdcb0 (P1 #f7dfa3, manual #efd7b9); "17B II" in black, "BUSINESS" in dark grey | dark brown band, about #2a1c16; "17B" and "BUSINESS" in gold |
| LCD window frame | grey-brown, #8a7a5c | gold, #735b37 |
| LCD background | #989e80 (lit) | not readable (off) |
| Ordinary keys, INPUT, digits | all the same, dark brown #2f2820 | all the same, #181616 |
| Key label text | off-white #d7cfbc | #c2bea5 |
| Shifted labels | gold-orange #deae64 | orange #c78852 |
| Shift key | blank, gold-orange #dcad56 | gold-orange (flash-lit #fbc05a) |
| Lower labels (ON, ENTER) | off-white, as key text | off-white |

The manuals call the shift key "the colored shift key" and the shifted
labels "printed in color" (src: [[sources/hp17bii-om]] p. 17). A thin gold
line frames the 17BII keyboard (P1). The menu keys print a white ^.

## Display and annunciators

The manuals describe four annunciators: shift (a bent arrow), printer
transmitting (a small printer symbol), alarm due or past due (((•))),
and low battery (a battery outline) (src: [[sources/hp17bii-om]] p. 17;
[[sources/hp17b-om]] p. 17). The Lewis has seven annunciator positions
([[hardware/lewis]] Display). Photograph P1 shows the 17BII at the end of
its self-test with ▲▼ and RAD lit, so at least those segments exist on
the 17BII glass although the manual never names them (src:
[[sources/commons-pioneer-photos]] P1).

Mapping to positions, inferred from the 42S's layout:

| Address | 42S | 17B/17BII |
| --- | --- | --- |
| #40218 | ▲▼ | ▲▼, undocumented, lit in the self-test (P1) |
| #40220 | shift | shift (inferred) |
| #40228 | print | print (inferred) |
| #40230 | busy ((•)) | alarm ((•)), the same symbol (inferred) |
| #40238 | battery | battery (inferred) |
| #40240 | G | unknown |
| #40248 | RAD | RAD, undocumented, lit in the self-test (P1) |

See [[questions/pioneer-annunciator-mapping]].

## Typing letters

Letters come from the ALPHA menu on the six menu keys: the first press
picks a group (ABCDE, FGHI, JKLM, NOPQ, RSTUV, WXYZ), the second the
letter; each group has an OTHER key for punctuation and non-English
letters, and the four-letter groups have a space. ALPHA-Edit offers
delete and cursor moves (src: [[sources/hp17bii-om]] p. 27-28). The 42S
does the same with its menu keys ([[hardware/hp42s]]).

## Key chords

From the manuals (src: [[sources/hp17bii-om]] p. 16, 217-220;
[[sources/hp17b-om]] p. 16, 217-220); identical on both models:

| Chord | Matrix | Effect |
| --- | --- | --- |
| hold CLR, press + or − | ON + OUT 0/IN 1, ON + OUT 0/IN 2 | contrast up or down |
| CLR + third menu key | ON + OUT 3/IN 64 | "MACHINE RESET": stops the calculation, clears the calculator line, shows MAIN; memory kept |
| CLR + first + sixth menu key, held together | ON + OUT 5/IN 64 + OUT 0/IN 64 | erases continuous memory; "MEMORY LOST" when the keys are released |
| CLR + fifth menu key | ON + OUT 1/IN 64 | continuous self-test |

The chords sit on the same matrix positions as the 42S's EXIT + √x
(reset) and EXIT + LN (self-test) ([[hardware/hp42s]]); the 42S's memory
clear is a two-key chord on the second key instead.

Erasing memory clears the calculator line and history, equations,
variables, lists and appointments and restores start-up settings
(month/day/year, 12-hour clock, 2 decimals, period as decimal point,
printer options off, beeper on). It keeps the time and date, and on the
17BII the ALG or RPN mode (src: [[sources/hp17bii-om]] p. 218). The same
message appears when power was lost; then the clock may be wrong (p. 214).

### Self-test

Turn on, go to MAIN (shift, EXIT), hold CLR and press the fifth menu key.
The test beeps and shows patterns and characters, prints to an IR
printer if one is on, and repeats until stopped. At the end of each pass
it shows "OK-17B-E" (17B) or "OK-17B II-E" (17BII), or FAIL with a
five-digit number. CLR + third menu key stops it with "MACHINE RESET";
any other key stops it with a FAIL message, which then means nothing
(src: [[sources/hp17bii-om]] p. 220; [[sources/hp17b-om]] p. 220).
Photograph P1 shows the pass screen: "OK-17B II-E" on the upper line, ▲▼
and RAD lit.

## ALG and RPN (17BII)

- shift, DSP (MODES), then the ALG menu key: "ALGEBRAIC MODE"; the RPN
  menu key: "RPN MODE". The mode stays until changed and survives a
  memory erase (src: [[sources/hp17bii-om]] p. 33, 250).
- 2 + 3 in ALG (and on the 17B): `2 + 3 =`.
- 2 + 3 in RPN: `2 = 3 +` (= is ENTER; INPUT also works) (src:
  [[sources/hp17bii-om]] p. 252-253).
- The Solver uses algebraic equations in both modes.

## Clock and alarms

The TIME menu shows the date and time and has CALC (date arithmetic),
APPT, ADJST and SET (src: [[sources/hp17bii-om]] p. 130-133).

- Set the time: TIME is a menu of MAIN (MAIN's five labels are FIN, BUS,
  SUM, TIME, SOLVE); press TIME, then SET, key the time as HH.MMSS (9.0830 for 9:08:30) and press
  TIME; in 12-hour format A/PM switches AM and PM. Date: key MM.DDYYYY
  (or DD.MMYYYY) and press DATE. 12/24 and M/D switch the formats. The
  display then shows the weekday, the date and the time, the time with A
  or P in 12-hour format (src: [[sources/hp17bii-om]] p. 132-133).
- ADJST moves the time by hours, minutes or seconds (p. 133).
- Appointments: ten (APT1 to APT10), each with a time (HH.MM), date,
  optional message of up to 22 characters and optional repeat interval
  up to 104 weeks (p. 133-135).
- When one comes due the alarm beeps and the ((•)) annunciator lights,
  "even if the calculator was off"; the message (or the time and date) is
  shown. A key press within 20 seconds acknowledges it; otherwise it is
  past due and the annunciator stays on. During a long calculation the
  annunciator lights and the calculator beeps once, and the alarm goes
  off when the calculation ends (p. 136).
- The beeper can be on, appointments only, or off (MODES, p. 33).

The manuals say only that a quartz crystal regulates the clock (p. 218).
Which Lewis timer keeps time while the calculator is off, and how the
alarm wakes the CPU, is not documented:
[[questions/pioneer-clock-alarm-hardware]].

## ROM images seen

@ractive's images, run in saturnus (iteration 42); both are international
images of 96 KB in dump order ([[hardware/saturn-models]] "ROM image
forms"):

| Model | Size | SHA-256 | Main ROM | Language ROM |
| --- | --- | --- | --- | --- |
| 17BII | 98304 | bf2f77dabf6aa276a27c86d8536d73742d664c8691b49f63cd7074835637176e | "17B II-EB" (revision B) | "OK-17B-LA" (revision A) |
| 17B | 98304 | 6540a89b9177ed406d097c41d4d5c20b002251c93b5498d2fbbba8550fdacc4e | "OK-17B-EB" (revision B) | "OK-17B-LB" (revision B) |

- The texts are the self-test pass messages each ROM keeps in its last
  32 bytes, after "COPYRIGHT HP 1987". They tell a 17B ROM from a 17BII
  one: the 17BII main ROM has "17B II-", the 17B main ROM "OK-17B-E"; the
  language ROMs of both say "OK-17B-L".
- After a memory loss the international models first show "SELECT
  LANGUAGE" with a menu of languages (the second label is ENGL); then
  "MEMORY LOST" over MAIN (FIN BUS SUM TIME SOLVE). The language ROM
  holds the other languages' messages (Portuguese texts seen in it).
- `2 + 3 =` shows 5.00.
- The self-test runs SPD, BEEP, DISP, ROM, ROM2 (the language ROM), DRAM,
  URAM and ends in a summary; the ROM steps failed with the 48's CRC rule
  and give #FFFF with the Lewis rule ([[questions/hp42s-rom-crc]]).
- In the TIME menu the time line changes once per emulated second, with
  saturnus's 8192 Hz timers ([[questions/pioneer-clock-alarm-hardware]]).
