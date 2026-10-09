---
title: "System flags: HP 48G / 48GX"
type: rom-behaviour
models: [48gx]
status: draft
sources: ["[[sources/hp48g-ug]]", "[[sources/hp48g-aur]]"]
tags: [hp48, 48gx, flags, rom]
---

# System flags: HP 48G / 48GX

What each system flag means to the 48G series ROM, transcribed in our own
words from the system flag appendix of the User's Guide (src:
[[sources/hp48g-ug]] App. D, p. D-1 to D-6 = PDF 467-472). The Advanced
User's Reference has the same table, entry for entry (src:
[[sources/hp48g-aur]] App. C, p. C-1 to C-6 = PDF 695-700); it was used as a
cross-check, and its page numbers are not repeated per row.

- System flags are -1 to -64; user flags are 1 to 64 (src:
  [[sources/hp48g-aur]] p. 1-42). RCLF returns both sets as two 64-bit
  binary integers, least significant bit = flag -1 or flag 1 (p. 1-44). Where
  the ROM keeps them in RAM: [[hardware/hp48-system-ram]].
- All system flags default to clear except the word-size flags -5 to -10
  (src: [[sources/hp48g-ug]] p. D-1).
- **User flags have no fixed meaning**: no built-in operation uses them and
  their meaning is whatever a program gives them. Setting user flag 1 to 5
  turns on the matching annunciator; plug-in cards may use 31 to 64 (src:
  [[sources/hp48g-aur]] p. 1-42).

Multi-flag fields are one row with a range; their encoding is in the Clear
column. The guides say how many bits the word size and digit count take but
not their bit order; that is left open here rather than filled in.

The table is parsed by saturnus' `scripts/flags-json.py`; keep its columns,
its Flags syntax (`-1` or `-5..-10`), the Topic and Status vocabularies, and
one row per flag or field.

## Flags

| Flags | Topic | Name | Clear | Set | Status | Source |
| --- | --- | --- | --- | --- | --- | --- |
| -1 | Math | Principal solution | QUAD and ISOL return the general solution | QUAD and ISOL return the principal solution only | known | [[sources/hp48g-ug]] p. D-1 |
| -2 | Math | Symbolic constants | e, i, π, MAXR and MINR stay symbolic unless -3 is set | Symbolic constants evaluate to numbers | known | [[sources/hp48g-ug]] p. D-1 |
| -3 | Math | Numerical results | Functions of symbolic arguments give symbolic results | Functions of symbolic arguments give numbers | known | [[sources/hp48g-ug]] p. D-1 |
| -4 | Other | Not used | - | - | unused | [[sources/hp48g-ug]] p. D-1 |
| -5..-10 | Binary integers | Word size | Six flags together hold the binary integer word size, 1 to 64 bits; default not all clear; bit order not given in the guide | - | known | [[sources/hp48g-ug]] p. D-1 |
| -11..-12 | Binary integers | Integer base | DEC: both clear; BIN: -12 set only; OCT: -11 set only; HEX: both set | - | known | [[sources/hp48g-ug]] p. D-2 |
| -13 | Other | Not used | - | - | unused | [[sources/hp48g-ug]] p. D-2 |
| -14 | Math | TVM payment timing | Payments at end of period | Payments at beginning of period | known | [[sources/hp48g-ug]] p. D-2 |
| -15..-16 | Angle and coordinates | Coordinate system | Rectangular: -16 clear (-15 ignored); polar/cylindrical: -16 set, -15 clear; polar/spherical: both set | - | known | [[sources/hp48g-ug]] p. D-2 |
| -17..-18 | Angle and coordinates | Angle mode | Degrees: both clear; radians: -17 set; grads: -18 set, -17 clear | - | known | [[sources/hp48g-ug]] p. D-2 |
| -19 | Math | 2D constructor | →V2 builds a 2-element vector | →V2 builds a complex number | known | [[sources/hp48g-ug]] p. D-2 |
| -20 | Errors and exceptions | Underflow action | Underflow gives 0 and sets -23 or -24 | Underflow is an error | known | [[sources/hp48g-ug]] p. D-2 |
| -21 | Errors and exceptions | Overflow action | Overflow gives ±9.99999999999E499 and sets -25 | Overflow is an error | known | [[sources/hp48g-ug]] p. D-2 |
| -22 | Errors and exceptions | Infinite result action | Infinite result is an error | Infinite result gives ±9.99999999999E499 and sets -26 | known | [[sources/hp48g-ug]] p. D-2 |
| -23 | Errors and exceptions | Negative underflow indicator | No negative underflow recorded | A negative underflow occurred (not trapped as an error) | known | [[sources/hp48g-ug]] p. D-2 |
| -24 | Errors and exceptions | Positive underflow indicator | No positive underflow recorded | A positive underflow occurred (not trapped as an error) | known | [[sources/hp48g-ug]] p. D-2 |
| -25 | Errors and exceptions | Overflow indicator | No overflow recorded | An overflow occurred (not trapped as an error) | known | [[sources/hp48g-ug]] p. D-2 |
| -26 | Errors and exceptions | Infinite result indicator | No infinite result recorded | An infinite result occurred (not trapped as an error) | known | [[sources/hp48g-ug]] p. D-2 |
| -27 | Number display | Symbolic complex display | Shown as (x,y) | Shown as x+y*i | known | [[sources/hp48g-ug]] p. D-3 |
| -28 | Plotting | Multiple functions | Equations plotted one after another | Equations plotted simultaneously | known | [[sources/hp48g-ug]] p. D-3 |
| -29 | Plotting | Axes | Axes drawn in 2D and statistics plots | No axes in 2D and statistics plots | known | [[sources/hp48g-ug]] p. D-3 |
| -30 | Other | Not used | - | - | unused | [[sources/hp48g-ug]] p. D-3 |
| -31 | Plotting | Curve filling | Plotted points joined | Points only, not joined | known | [[sources/hp48g-ug]] p. D-3 |
| -32 | Plotting | Graphics cursor | Cursor always dark | Cursor inverts against the background | known | [[sources/hp48g-ug]] p. D-3 |
| -33 | I/O and printing | I/O device | Serial port (wire) | IR port | known | [[sources/hp48g-ug]] p. D-3 |
| -34 | I/O and printing | Printing device | IR printer | Serial port, when -33 is clear | known | [[sources/hp48g-ug]] p. D-3 |
| -35 | I/O and printing | I/O data format | Objects sent as ASCII | Objects sent as binary memory images | known | [[sources/hp48g-ug]] p. D-3 |
| -36 | I/O and printing | Receive overwrite | A received name that exists gets a numeric suffix | A received name that exists overwrites the variable | known | [[sources/hp48g-ug]] p. D-4 |
| -37 | I/O and printing | Print spacing | Single-spaced printing | Double-spaced printing | known | [[sources/hp48g-ug]] p. D-4 |
| -38 | I/O and printing | Print line feed | Line feed after each printed line | No line feed after printed lines | known | [[sources/hp48g-ug]] p. D-4 |
| -39 | I/O and printing | I/O messages | Transfer messages shown | Transfer messages suppressed | known | [[sources/hp48g-ug]] p. D-4 |
| -40 | Time and alarms | Clock display | Clock hidden | Clock shown, ticking | known | [[sources/hp48g-ug]] p. D-4 |
| -41 | Time and alarms | Clock format | 12-hour clock | 24-hour clock | known | [[sources/hp48g-ug]] p. D-4 |
| -42 | Time and alarms | Date format | MM/DD/YY | DD.MM.YY | known | [[sources/hp48g-ug]] p. D-4 |
| -43 | Time and alarms | Repeat alarm rescheduling | Unacknowledged repeat alarms are rescheduled | Unacknowledged repeat alarms are not rescheduled | known | [[sources/hp48g-ug]] p. D-4 |
| -44 | Time and alarms | Acknowledged alarms | Removed from the alarm list | Kept in the alarm list | known | [[sources/hp48g-ug]] p. D-4 |
| -45..-48 | Number display | Displayed digits | Four flags together hold the digit count for Fix, Sci and Eng; bit order not given in the guide | - | known | [[sources/hp48g-ug]] p. D-5 |
| -49..-50 | Number display | Number format | Std: both clear; Fix: -49 set only; Sci: -50 set only; Eng: both set | - | known | [[sources/hp48g-ug]] p. D-5 |
| -51 | Number display | Fraction mark | Period | Comma | known | [[sources/hp48g-ug]] p. D-5 |
| -52 | Display | Level 1 display | Level 1 may use up to four stack lines | Level 1 limited to one line | known | [[sources/hp48g-ug]] p. D-5 |
| -53 | Display | Algebraic parentheses | Redundant parentheses omitted | All parentheses shown | known | [[sources/hp48g-ug]] p. D-5 |
| -54 | Math | Tiny array elements | RANK-type commands zero singular values below 1E-14 of the largest; DET rounds | Small singular values kept; DET not rounded | known | [[sources/hp48g-ug]] p. D-5 |
| -55 | System | Last arguments | Command arguments saved | Arguments not saved | known | [[sources/hp48g-ug]] p. D-5 |
| -56 | System | Error beep | Error and BEEP sounds on | Error and BEEP sounds off | known | [[sources/hp48g-ug]] p. D-5 |
| -57 | Time and alarms | Alarm beep | Alarm sound on | Alarm sound off | known | [[sources/hp48g-ug]] p. D-6 |
| -58 | Display | Verbose messages | Parameter variable contents shown automatically | Automatic parameter display off | known | [[sources/hp48g-ug]] p. D-6 |
| -59 | Display | Variable browser | Names and contents | Names only | known | [[sources/hp48g-ug]] p. D-6 |
| -60 | Keyboard and entry | Alpha lock | α once = one letter, twice = lock | α once locks alpha | known | [[sources/hp48g-ug]] p. D-6 |
| -61 | Keyboard and entry | User mode lock | USER once = one key, twice = lock | USER once locks user mode | known | [[sources/hp48g-ug]] p. D-6 |
| -62 | Keyboard and entry | User mode | User keyboard off | User keyboard on | known | [[sources/hp48g-ug]] p. D-6 |
| -63 | Keyboard and entry | Vectored ENTER | ENTER evaluates the command line | User-defined ENTER handling on | known | [[sources/hp48g-ug]] p. D-6 |
| -64 | System | Index wrap indicator | Last GETI or PUTI did not wrap to the first element | Last GETI or PUTI wrapped to the first element | known | [[sources/hp48g-ug]] p. D-6 |

Counts: 61 flags known (in 49 rows), 3 unused (-4, -13, -30), none
unknown.

## Related

- The 48S/SX list and how far it is established: [[hardware/system-flags-48sx]].
- The 49G list: [[hardware/system-flags-49g]].
- Model page: [[hardware/hp48gx]].
