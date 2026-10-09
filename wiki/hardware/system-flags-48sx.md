---
title: "System flags: HP 48S / 48SX"
type: rom-behaviour
models: [48sx]
status: draft
sources: ["[[sources/hp48sx-om]]", "[[sources/hp48-faq]]"]
tags: [hp48, 48sx, flags, rom]
---

# System flags: HP 48S / 48SX

What each system flag means to the 48S/SX ROM, transcribed in our own
words from appendix E of the Owner's Manual (src: [[sources/hp48sx-om]]
App. E, p. E-1 to E-7 = PDF 743-749). Every flag from -1 to -64 is in that
appendix, either described or as "not used"; nothing here is carried over
from the 48G.

- All system flags default to clear except the word-size flags -5 to -10
  (src: [[sources/hp48sx-om]] p. E-1).
- Compared with the 48G/GX ([[hardware/system-flags-48gx]]): -14, -27, -28,
  -29 and -54 are not used on the S/SX; -30 (function plotting) exists only
  on the S/SX; -59 controls the Equation Catalog's display, not a variable
  browser. The HP 48 FAQ says the same of -14, -27 to -30 and -54 (src:
  [[sources/hp48-faq]] 10.1). The other flags have the same meaning on
  both.
- **User flags have no fixed meaning**: positive flags are left to the user
  and programs; setting user flag 1 to 5 shows a small number at the top of
  the screen (src: [[sources/hp48-faq]] 4.12). The appendix covers system
  flags only; that the S/SX has 64 user flags is what the ROM's two 64-bit
  flag words show ([[hardware/hp48-system-ram]]).

Multi-flag fields are one row with a range; their encoding is in the Clear
column. The appendix says how many flags hold the word size and the digit
count but not their bit order; that is left open here. The fields'
plain-words descriptions and named settings, for a flags list:
[[hardware/system-flag-fields]].

The table is parsed by saturnus' `scripts/flags-json.py`; keep its columns,
its Flags syntax (`-1` or `-5..-10`), the Topic and Status vocabularies, and
one row per flag or field.

## Flags

| Flags | Topic | Name | Clear | Set | Status | Source |
| --- | --- | --- | --- | --- | --- | --- |
| -1 | Math | Principal solution | QUAD and ISOL return the general solution | QUAD and ISOL return the principal solution only | known | [[sources/hp48sx-om]] p. E-1 |
| -2 | Math | Symbolic constants | e, i, π, MAXR and MINR stay symbolic unless -3 is set | Symbolic constants evaluate to numbers | known | [[sources/hp48sx-om]] p. E-1 |
| -3 | Math | Numerical results | Functions of symbolic arguments give symbolic results | Functions of symbolic arguments give numbers | known | [[sources/hp48sx-om]] p. E-1 |
| -4 | Other | Not used | - | - | unused | [[sources/hp48sx-om]] p. E-1 |
| -5..-10 | Binary integers | Word size | Six flags together hold the binary integer word size, 1 to 64 bits; default not all clear; bit order not given in the guide | - | known | [[sources/hp48sx-om]] p. E-2 |
| -11..-12 | Binary integers | Integer base | DEC: both clear; BIN: -12 set only; OCT: -11 set only; HEX: both set | - | known | [[sources/hp48sx-om]] p. E-2 |
| -13 | Other | Not used | - | - | unused | [[sources/hp48sx-om]] p. E-2 |
| -14 | Other | Not used | - | - | unused | [[sources/hp48sx-om]] p. E-2 |
| -15..-16 | Angle and coordinates | Coordinate system | Rectangular: both clear; polar/cylindrical: -16 set, -15 clear; polar/spherical: both set | - | known | [[sources/hp48sx-om]] p. E-2 |
| -17..-18 | Angle and coordinates | Angle mode | Degrees: both clear; radians: -17 set, -18 clear; grads: -18 set, -17 clear | - | known | [[sources/hp48sx-om]] p. E-2 |
| -19 | Math | 2D constructor | →V2 builds a 2-element vector | →V2 builds a complex number | known | [[sources/hp48sx-om]] p. E-2 |
| -20 | Errors and exceptions | Underflow action | Underflow gives 0 and sets -23 or -24 | Underflow is an error | known | [[sources/hp48sx-om]] p. E-2 |
| -21 | Errors and exceptions | Overflow action | Overflow gives ±9.99999999999E499 and sets -25 | Overflow is an error | known | [[sources/hp48sx-om]] p. E-2 |
| -22 | Errors and exceptions | Infinite result action | Infinite result is an error | Infinite result gives ±9.99999999999E499 and sets -26 | known | [[sources/hp48sx-om]] p. E-3 |
| -23 | Errors and exceptions | Negative underflow indicator | No negative underflow recorded | A negative underflow occurred (not trapped as an error) | known | [[sources/hp48sx-om]] p. E-3 |
| -24 | Errors and exceptions | Positive underflow indicator | No positive underflow recorded | A positive underflow occurred (not trapped as an error) | known | [[sources/hp48sx-om]] p. E-3 |
| -25 | Errors and exceptions | Overflow indicator | No overflow recorded | An overflow occurred (not trapped as an error) | known | [[sources/hp48sx-om]] p. E-3 |
| -26 | Errors and exceptions | Infinite result indicator | No infinite result recorded | An infinite result occurred (not trapped as an error) | known | [[sources/hp48sx-om]] p. E-3 |
| -27 | Other | Not used | - | - | unused | [[sources/hp48sx-om]] p. E-3 |
| -28 | Other | Not used | - | - | unused | [[sources/hp48sx-om]] p. E-3 |
| -29 | Other | Not used | - | - | unused | [[sources/hp48sx-om]] p. E-3 |
| -30 | Plotting | Function plotting | For an equation y = f(x), only f(x) is plotted | For an equation y = f(x), y and f(x) are plotted separately | known | [[sources/hp48sx-om]] p. E-3 |
| -31 | Plotting | Curve filling | Plotted points joined | Points only, not joined | known | [[sources/hp48sx-om]] p. E-3 |
| -32 | Plotting | Graphics cursor | Cursor always dark | Cursor inverts against the background | known | [[sources/hp48sx-om]] p. E-3 |
| -33 | I/O and printing | I/O device | Serial port (wire) | IR port | known | [[sources/hp48sx-om]] p. E-3 |
| -34 | I/O and printing | Printing device | IR printer | Serial port, when -33 is clear | known | [[sources/hp48sx-om]] p. E-3 |
| -35 | I/O and printing | I/O data format | Objects sent as ASCII | Objects sent as binary memory images | known | [[sources/hp48sx-om]] p. E-4 |
| -36 | I/O and printing | Receive overwrite | A received name that exists gets a numeric suffix | A received name that exists overwrites the variable | known | [[sources/hp48sx-om]] p. E-4 |
| -37 | I/O and printing | Print spacing | Single-spaced printing | Double-spaced printing | known | [[sources/hp48sx-om]] p. E-4 |
| -38 | I/O and printing | Print line feed | Line feed after each printed line | No line feed after printed lines | known | [[sources/hp48sx-om]] p. E-4 |
| -39 | I/O and printing | I/O messages | Transfer messages shown | Transfer messages suppressed | known | [[sources/hp48sx-om]] p. E-4 |
| -40 | Time and alarms | Clock display | Clock shown only while the TIME menu is selected | Clock shown, ticking, at all times | known | [[sources/hp48sx-om]] p. E-4 |
| -41 | Time and alarms | Clock format | 12-hour clock | 24-hour clock | known | [[sources/hp48sx-om]] p. E-4 |
| -42 | Time and alarms | Date format | MM/DD/YY | DD.MM.YY | known | [[sources/hp48sx-om]] p. E-4 |
| -43 | Time and alarms | Repeat alarm rescheduling | Unacknowledged repeat alarms are rescheduled | Unacknowledged repeat alarms are not rescheduled | known | [[sources/hp48sx-om]] p. E-5 |
| -44 | Time and alarms | Acknowledged alarms | Removed from the alarm list | Kept in the alarm list | known | [[sources/hp48sx-om]] p. E-5 |
| -45..-48 | Number display | Displayed digits | Four flags together hold the digit count for Fix, Sci and Eng; bit order not given in the guide | - | known | [[sources/hp48sx-om]] p. E-5 |
| -49..-50 | Number display | Number format | Std: both clear; Fix: -49 set only; Sci: -50 set only; Eng: both set | - | known | [[sources/hp48sx-om]] p. E-5 |
| -51 | Number display | Fraction mark | Period | Comma | known | [[sources/hp48sx-om]] p. E-5 |
| -52 | Display | Level 1 display | Level 1 may use up to four stack lines | Level 1 limited to one line | known | [[sources/hp48sx-om]] p. E-5 |
| -53 | Display | Algebraic parentheses | Redundant parentheses omitted | All parentheses shown | known | [[sources/hp48sx-om]] p. E-5 |
| -54 | Other | Not used | - | - | unused | [[sources/hp48sx-om]] p. E-5 |
| -55 | System | Last arguments | Command arguments saved | Arguments not saved | known | [[sources/hp48sx-om]] p. E-6 |
| -56 | System | Error beep | Error and BEEP sounds on | Error and BEEP sounds off | known | [[sources/hp48sx-om]] p. E-6 |
| -57 | Time and alarms | Alarm beep | Alarm sound on | Alarm sound off | known | [[sources/hp48sx-om]] p. E-6 |
| -58 | Display | Verbose messages | Prompt messages and data shown automatically | Automatic prompt messages and data off | known | [[sources/hp48sx-om]] p. E-6 |
| -59 | Display | Fast catalog display | Equation Catalog and the SOLVE and PLOT menu messages show equation and name | They show the equation name only | known | [[sources/hp48sx-om]] p. E-6 |
| -60 | Keyboard and entry | Alpha lock | α once = one letter, twice = lock | α once locks alpha | known | [[sources/hp48sx-om]] p. E-6 |
| -61 | Keyboard and entry | User mode lock | USER once = one key, twice = lock | USER once locks user mode | known | [[sources/hp48sx-om]] p. E-6 |
| -62 | Keyboard and entry | User mode | User keyboard off | User keyboard on | known | [[sources/hp48sx-om]] p. E-6 |
| -63 | Keyboard and entry | Vectored ENTER | ENTER evaluates the command line | User-defined ENTER handling on | known | [[sources/hp48sx-om]] p. E-7 |
| -64 | System | Index wrap indicator | Last GETI or PUTI did not wrap to the first element | Last GETI or PUTI wrapped to the first element | known | [[sources/hp48sx-om]] p. E-7 |
