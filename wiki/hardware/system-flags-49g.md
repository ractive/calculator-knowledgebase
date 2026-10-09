---
title: "System flags: HP 49G"
type: rom-behaviour
models: [49g]
status: draft
sources: ["[[sources/hp49g-pocket-guide]]", "[[sources/hp49g-aug]]", "[[sources/hp49g-um]]"]
tags: [hp49g, flags, rom]
---

# System flags: HP 49G

What each system flag means to the 49G ROM, transcribed in our own words
from the "System Flags" list of the *HP 49G Pocket Guide* (src:
[[sources/hp49g-pocket-guide]] p. 76-79; read from @ractive's photographs of a
printed copy, which are not distributed). The Advanced User's Guide refers to
that booklet for the list (src: [[sources/hp49g-aug]] p. 2-1); the
statements the two guides make in passing agree with it except for the
default of -90 (see Contradictions).

- 128 system flags (-1 to -128) and 128 user flags (1 to 128); SF, CF and
  FS? and their relatives take the flag number; STOF and RCLF store and
  recall all flags as a list of two 128-bit binary integers, system first
  (src: [[sources/hp49g-pocket-guide]] p. 76). **User flags have no fixed
  meaning**; they are mainly for programs (src: [[sources/hp49g-aug]]
  p. 2-4).
- The booklet calls its list complete, but it lists only 103 of the 128
  system flags, ending at -120. The 25 it leaves out (-4, -13, -30, -33,
  -34, -56, -75, -77, -78, -101, -102, -104, -107, -108, -112, -115,
  -118, -121 to -128) are recorded below as not listed; neither the
  booklet nor the other two guides says whether they are unused.
- Defaults (the booklet marks the default state of each flag): every
  listed flag defaults to clear except the word-size flags -5 to -10,
  which default to set (word size 64).
- Word size: -5 to -10 carry the bit values 1, 2, 4, 8, 16 and 32, and
  the binary word size is 1 more than the sum of the values of the set
  flags (src: [[sources/hp49g-pocket-guide]] p. 76, footnote).
- Display digits: -45 to -48 carry the bit values 1, 2, 4 and 8 of the
  digit count used by Fix, Sci and Eng (p. 77).
- The booklet is terse for some flags (-71, -86, -89, -93, -94, -120): it
  names the two states without saying what they apply to. The rows below
  say no more than it does.
- All four photographs are legible; no entry was guessed.

Multi-flag fields are one row with a range; their encoding is in the
Clear column.

The table is parsed by saturnus' `scripts/flags-json.py`; keep its columns,
its Flags syntax (`-1` or `-5..-10`), the Topic and Status vocabularies, and
one row per flag or field. Runs of unlisted flags are one `unknown` row
each.

## Flags

| Flags | Topic | Name | Clear | Set | Status | Source |
| --- | --- | --- | --- | --- | --- | --- |
| -1 | CAS | Principal solution | QUAD and ISOL give the general solutions | QUAD and ISOL give the principal solution | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -2 | Math | Symbolic constants | Symbolic constants stay symbolic, unless -3 is set | Symbolic constants evaluate to numbers | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -3 | CAS | Numeric results | Functions of symbolic arguments stay symbolic | Functions of symbolic arguments give numbers | known | [[sources/hp49g-pocket-guide]] p. 76; [[sources/hp49g-aug]] p. 14-7 |
| -4 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 76 |
| -5..-10 | Binary integers | Word size | Bit values 1, 2, 4, 8, 16, 32 for -5 to -10; the word size is 1 + the sum of the set flags' values; default all set (64 bits) | - | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -11..-12 | Binary integers | Integer base | DEC: both clear; BIN: -12 set only; OCT: -11 set only; HEX: both set | - | known | [[sources/hp49g-pocket-guide]] p. 76; [[sources/hp49g-aug]] p. 8-2 |
| -13 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 76 |
| -14 | Math | TVM payment timing | TVM payments at the end of the period | TVM payments at the beginning of the period | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -15..-16 | Angle and coordinates | Coordinate system | Rectangular: -16 clear (-15 ignored); cylindrical: -16 set, -15 clear; spherical: both set | - | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -17..-18 | Angle and coordinates | Angle mode | Radians: -17 set (-18 ignored); degrees: both clear; grads: -17 clear, -18 set | - | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -19 | Math | 2D constructor | →V2 builds a 2-element vector | →V2 builds a complex number | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -20 | Errors and exceptions | Underflow action | Underflow gives 0 and sets -23 or -24 | Underflow is an error | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -21 | Errors and exceptions | Overflow action | Overflow gives ±MAXR and sets -25 | Overflow is an error | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -22 | Errors and exceptions | Infinite result action | Infinite result is an error | Infinite result gives ±MAXR and sets -26 | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -23 | Errors and exceptions | Negative underflow indicator | No negative underflow recorded | A negative underflow occurred (with -20 clear) | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -24 | Errors and exceptions | Positive underflow indicator | No positive underflow recorded | A positive underflow occurred (with -20 clear) | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -25 | Errors and exceptions | Overflow indicator | No overflow recorded | An overflow occurred (not trapped as an error) | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -26 | Errors and exceptions | Infinite result indicator | No infinite result recorded | An infinite result occurred (with -22 set) | known | [[sources/hp49g-pocket-guide]] p. 76 |
| -27 | Number display | Symbolic complex display | Shown as (x,y) | Shown as x+y*i | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -28 | Plotting | Multiple equations | Equations plotted one after another | Equations plotted at the same time | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -29 | Plotting | Axes | Axes drawn in 2D and statistics plots | No axes in 2D and statistics plots | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -30 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 77 |
| -31 | Plotting | Curve filling | Plotted points joined | Points only, not joined | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -32 | Plotting | Graphics cursor | Cursor always dark | Cursor inverts against the background | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -33..-34 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 77 |
| -35 | I/O and printing | I/O data format | Objects sent as ASCII | Objects sent as binary | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -36 | I/O and printing | Receive overwrite | A received name that exists is renamed | A received name that exists overwrites the variable | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -37 | I/O and printing | Print spacing | Single-spaced printing | Double-spaced printing | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -38 | I/O and printing | Print line feed | Line feed after each printed line | No line feed after printed lines | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -39 | I/O and printing | I/O messages | Transfer messages shown | Transfer messages suppressed | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -40 | Time and alarms | Clock display | Clock shown only in the TIME menu | Clock always shown | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -41 | Time and alarms | Clock format | 12-hour clock | 24-hour clock | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -42 | Time and alarms | Date format | MM/DD/YY | DD.MM.YY | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -43 | Time and alarms | Repeat alarm rescheduling | Unacknowledged repeat alarms are rescheduled | Unacknowledged repeat alarms are not rescheduled | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -44 | Time and alarms | Acknowledged alarms | Removed from the alarm list | Kept in the alarm list | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -45..-48 | Number display | Displayed digits | Bit values 1, 2, 4, 8 for -45 to -48 give the digit count for Fix, Sci and Eng; default all clear | - | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -49..-50 | Number display | Number format | Std: both clear; Fix: -49 set only; Sci: -50 set only; Eng: both set | - | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -51 | Number display | Fraction mark | Period | Comma | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -52 | Display | Level 1 display | Level 1 may use up to four lines | Level 1 limited to one line | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -53 | Display | Algebraic parentheses | Some parentheses left out | All parentheses shown | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -54 | Math | Tiny array elements | Small matrix values become 0; DET rounds | Small matrix values kept; DET not rounded | known | [[sources/hp49g-pocket-guide]] p. 77; [[sources/hp49g-aug]] p. 5-15 |
| -55 | System | Last arguments | Arguments of the last command saved | Arguments not saved | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -56 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 77 |
| -57 | Time and alarms | Alarm tone | Alarm tone on | Alarm tone off | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -58 | Display | Verbose messages | Parameter and variable INFO shown | Parameter and variable INFO not shown | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -59 | Display | Variable browser | Names and contents | Names only | known | [[sources/hp49g-pocket-guide]] p. 77 |
| -60 | Keyboard and entry | Alpha lock | α twice locks alpha | α once locks alpha | known | [[sources/hp49g-pocket-guide]] p. 77; [[sources/hp49g-aug]] p. 2-1 |
| -61 | Keyboard and entry | User mode lock | USER twice locks user mode | USER once locks user mode | known | [[sources/hp49g-pocket-guide]] p. 77; [[sources/hp49g-aug]] p. 13-2 |
| -62 | Keyboard and entry | User mode | User keyboard off | User keyboard on | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -63 | Keyboard and entry | Vectored ENTER | ENTER evaluates the command line | User-defined ENTER on | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -64 | System | Index wrap indicator | Last GETI or PUTI did not wrap the index | Last GETI or PUTI wrapped the index to 1 | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -65 | Display | Multi-line stack levels | Every stack level may use several lines | Only level 1 may use several lines | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -66 | Display | Long strings | Long strings wrap over several lines | Long strings on one line | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -67 | Time and alarms | Clock style | Shown clock is digital | Shown clock is analog | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -68 | Keyboard and entry | Auto-indent | Command line not indented automatically | Command line indented automatically | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -69 | Keyboard and entry | Full-screen editing | Cursor stays in the text line | Cursor may move over the whole text page | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -70 | Display | →GROB lines | →GROB takes one-line strings only | →GROB takes multi-line strings | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -71 | System | Addresses | Addresses added (the booklet says no more) | No addresses (the booklet says no more) | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -72 | Display | Stack font | Stack in the current font | Stack in the minifont when the current font is FONT6 | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -73 | Keyboard and entry | Command line font | Command line in the current font | Command line in the minifont when the current font is FONT6 | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -74 | Display | Stack alignment | Stack right-aligned | Stack left-aligned | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -75 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 78 |
| -76 | System | Filer purge confirmation | Filer purges without asking | Filer asks before purging | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -77..-78 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 78 |
| -79 | Display | Algebraics on the stack | Algebraics shown in Equation Writer form | Algebraics shown in quoted text form | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -80 | Display | Equation Writer stack font | Equation Writer stack display in the current font | Equation Writer stack display in the minifont when the current font is FONT6 | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -81 | Display | Equation Writer grob font | Equation Writer grob editing in the current font | Equation Writer grob editing in the minifont when the current font is FONT6 | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -82 | Display | Equation Writer edit font | Equation Writer algebraic editing in the current font | Equation Writer algebraic editing in the minifont when the current font is FONT6 | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -83 | Display | Grob display | Grobs shown as their picture on the stack | Grobs shown as a description | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -84 | Display | Menu label font | Menu labels in the standard font | Menu labels in the minifont when the current font is FONT6 | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -85 | Display | SysRPL stack display | Normal stack display | System RPL stack display | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -86 | Display | Program prefix | Program prefix on (the booklet says no more) | Program prefix off | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -87 | Display | Recursive stack display | Recursive stack display on | Recursive stack display off | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -88 | Display | Recursive object display | Recursive object display off | Recursive object display on | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -89 | Display | Unknowns | Unknowns shown as addresses (the booklet says no more) | Unknowns shown as mnemonics | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -90 | Display | Choose box font | Choose boxes in the current font | Choose boxes in the minifont when the current font is FONT6 | known | [[sources/hp49g-pocket-guide]] p. 78; [[sources/hp49g-aug]] p. 1-4 (catalog) |
| -91 | Keyboard and entry | MatrixWriter lists | MatrixWriter takes arrays only | MatrixWriter works on lists of lists | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -92 | System | MASD mode | MASD assembles machine code | MASD in System RPL mode | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -93 | Display | Header | Normal header (the booklet says no more) | Math header | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -94 | System | LASTCMD result | Result = LASTCMD (the booklet says no more) | Result ≠ LASTCMD | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -95 | Keyboard and entry | Algebraic or RPN mode | RPN mode | Algebraic mode | known | [[sources/hp49g-pocket-guide]] p. 78; [[sources/hp49g-aug]] p. 2-1 |
| -96 | Display | Menu display | Menu hidden | Menu shown | known | [[sources/hp49g-pocket-guide]] p. 78 |
| -97 | Display | List display | Lists shown on one line | Lists shown in two dimensions | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -98 | Display | Vector display | Vectors shown on one line | Vectors shown in two dimensions | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -99 | CAS | Verbose mode | CAS concise | CAS verbose | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -100 | CAS | Step-by-step mode | CAS step by step | CAS gives the final result only | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -101..-102 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 79 |
| -103 | CAS | Complex mode | Real mode | Complex mode | known | [[sources/hp49g-pocket-guide]] p. 79; [[sources/hp49g-aug]] p. 14-20 |
| -104 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 79 |
| -105 | CAS | Exact or approximate mode | Exact mode | Approximate mode | known | [[sources/hp49g-pocket-guide]] p. 79; [[sources/hp49g-aug]] p. 14-7 |
| -106 | CAS | TSIMP in SERIES | SERIES may call TSIMP | SERIES does not call TSIMP | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -107..-108 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 79 |
| -109 | CAS | Numeric factorization | Numeric factorization not allowed | Numeric factorization allowed | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -110 | Math | Large matrices | Normal matrices | Large-matrix mode (faster on large matrices) | known | [[sources/hp49g-pocket-guide]] p. 79; [[sources/hp49g-um]] p. 8-12 |
| -111 | CAS | Recursive simplification | EXPA and TSIMP simplify recursively | EXPA and TSIMP do not simplify recursively | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -112 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 79 |
| -113 | CAS | RISCH linearity | RISCH tries linearity simplification | RISCH does not try linearity simplification | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -114 | CAS | Polynomial order | Decreasing powers (x+1) | Increasing powers (1+x) | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -115 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 79 |
| -116 | CAS | Prefer sin or cos | Simplify towards cosines | Simplify towards sines | known | [[sources/hp49g-pocket-guide]] p. 79; [[sources/hp49g-aug]] p. 14-61 |
| -117 | Display | Menu style | Menus on the soft keys | Menus as choose boxes | known | [[sources/hp49g-pocket-guide]] p. 79; [[sources/hp49g-um]] p. 2-7 |
| -118 | Other | Not listed in the Pocket Guide | - | - | unknown | absent from [[sources/hp49g-pocket-guide]] p. 79 |
| -119 | CAS | Rigorous mode | Rigorous mode | Non-rigorous mode | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -120 | CAS | Silent mode | Non-silent mode (the booklet says no more) | Silent mode | known | [[sources/hp49g-pocket-guide]] p. 79 |
| -121..-128 | Other | Not listed in the Pocket Guide | - | - | unknown | the list ends at -120, [[sources/hp49g-pocket-guide]] p. 79 |

Counts: 103 flags listed and transcribed (in 91 rows), 25 not listed in
the Pocket Guide (unknown, in 14 rows), none unreadable.

## Observed: the ROM's own defaults

After a cold start of ROM 2.10 in saturnus (Try To Recover Memory? NO,
Memory Clear) the set system flags are -5 to -10, -11, -12, -17, -27,
-34, -90, -95 and -128 (observed 2026-10-07 through saturnus' flags
read, iteration 12d; not checked on @ractive's 49G). Against the
booklet's defaults that is HEX instead of DEC, radians instead of
degrees, x+y*i display, the minifont for choose boxes (as the AUG says)
and algebraic instead of RPN; -34 and -128, which the booklet does not
list, are set too, so at least those two are in use. The booklet's
defaults are probably those of the first ROMs; later ROMs changed the
mode defaults (unverified).

## Contradictions

- **-90 default.** The booklet marks clear (current font) as the default
  (src: [[sources/hp49g-pocket-guide]] p. 78); the Advanced User's Guide
  says set is the default, giving a minifont catalog with six commands
  per page (src: [[sources/hp49g-aug]] p. 1-4). The two may describe
  different ROM versions; ROM 2.10 starts with it set (see Observed
  above). See [[questions/hp49g-system-flags]].
- **-100 polarity.** A saturnus reviewer (PR 30) recalled the CAS
  modes form the other way round. The booklet is unambiguous (set = final
  result only, clear = step by step, p. 79) and the row follows it; a
  check on the calculator (CAS MODES form against `FS? -100`) would
  settle it.
- **-25 condition.** The booklet's set state of -25 reads "if Flag -25 is
  clear", which cannot be meant; from -21 (overflow sets -25 when -21 is
  clear) the intended flag is -21. The row says "not trapped as an
  error" rather than repeat the slip.

## Related

- The 48G/GX list: [[hardware/system-flags-48gx]].
- The 48S/SX list: [[hardware/system-flags-48sx]].
- Model page: [[hardware/hp49g]].
