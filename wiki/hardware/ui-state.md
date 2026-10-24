---
title: "What has the keyboard: the stack, a form, a box, the graphics screen, a halted program (48SX, 48GX, 49G)"
type: rom-behaviour
models: [48sx, 48gx, 49g]
status: draft
sources: ["[[sources/rplman]]", "[[sources/hp48-internals-address-list]]"]
tags: [ram, rpl, keyboard, display, memory]
---

# What has the keyboard

How a host can tell, from RAM and without pressing a key, whether the
stack's own loop reads the keys or something else does: an input form, a
choose box, a message box, the graphics screen, a program waiting in
`INPUT`, the interactive stack, an editor; and whether programs are
halted. Found for saturnus iteration 61 (its AI tools must not type RPL
into a form's field), by observation in saturnus as
[[hardware/command-line]] and [[hardware/hp48-system-ram]] were: each
screen opened by its keys or command, then the return stack, the
temporary environments and system RAM read while the ROM waits for a key,
and diffed against the bare stack. ROMs: 48SX J, 48GX R, 49G 2.10; the
49G 1.19-6 emulator ROM checked for the bare stack, a form, a choose box,
a message box, a halt, PVIEW and the interactive stack (observed
2026-10-24).

## The return stack

The 1991 address list names the saved RPL registers: `HeapStart` at
#7056F and `FramePtr` (saved B, the return stack pointer) at #70574 on
the 48SX (src: [[sources/hp48-internals-address-list]]). The 48GX and the
49G keep them at the 48SX address plus #1017F (#806EE and #806F3), as the
other pointers of [[hardware/hp48-system-ram]] (observed). While the ROM
waits for a key, the return stack lies between the two: `HeapStart`
points at its bottom entry, `FramePtr` just past its top one; each entry
is a 5-nibble return address (observed).

- At the bare stack it holds 8 entries on the 48SX and 48GX, 9 on the
  49G, the same after commands, errors, `CRDIR` and with a command line
  open. Bottom first: a 0 entry, two entries of the system outer loop
  (the 49G has one more below them), the outer loop's return from its key
  wait, and the key wait's own four entries on top. The top four are the
  same in every state observed (observed; 48SX J and 48GX R share the
  addresses, #4204B to #421C2).
- **Stack in control**: the entry right under the key wait's four (the
  sixth from the top) equals the outer loop's frame at the bottom (the
  third entry from the bottom on the 48SX and 48GX, the fourth on the
  49G). Every other loop puts its own frame there: the parameterized
  outer loop of RPLMAN chapter 21 (src: [[sources/rplman]] 21.4) for
  input forms, choose boxes, message boxes, the graphics screen, `INPUT`,
  `WAIT`, `PVIEW`, the interactive stack and the 48 matrix writer; the 48
  equation writer runs a loop of its own (observed on all three ROMs).
  The rule holds without knowing a ROM address, and held on the 49G 1.19-6
  ROM, whose loops sit at other addresses.
- **Halted programs**: `HALT` and `PROMPT` leave the program's frames on
  the return stack and start a new outer loop above them, so the stack is
  in control and the return stack is deeper (17 entries on the 48SX with
  one halted program, 26 with two). Each halted program adds one more
  copy of the outer-loop frame: the count of halted programs is the
  number of copies minus one. `CONT` and `KILL` bring it back to the
  bare depth (observed).
- Pressing ATTN (CANCEL), at most five times, returned to the stack from
  every screen in the table below, from `INPUT`, `WAIT`, the interactive
  stack and the writers, and from a message box over a form; halted
  programs stayed halted (observed).
- Valid only while the ROM waits for a key: the saved B is written when
  the RPL machine saves its registers, so a running program's state is
  not read this way.

## The temporary environments

RPLMAN describes the temporary environment area: a chain of
environments, newest first, each a link field, a protection word and
pairs of pointers to a temporary identifier and to the object bound to it
(src: [[sources/rplman]] 13.1). The newest environment is at the pointer
`local_vars` (#70583 on the 48SX, #80702 on the 48GX and 49G; see
[[hardware/command-line]]). Walking it, link by link from that address,
gives the names the open screens bound (observed):

| Screen | 48GX R | 49G 2.10 |
| --- | --- | --- |
| Input form (PLOT, MODES, `INFORM`) | `IF`, under the loop's `SavedUI` | `CmdLine`, `IfMenu`, `CurrentField`, `FieldHandler`, `IfHandler`, `StringData` |
| Choose box (`CHOOSE`, APPS) | only unnamed ones | `NbElm`, `DispOffset`, `DispTop`, `Pos`, `Message` and others |
| Message box (`MSGBOX`, the 49G's error box) | only unnamed ones (7 pairs) | only unnamed ones (7 pairs) |
| Graphics screen (PICTURE) | `PlotEnv`, `SAVEDDEPTH`, `EXITFCN`, `EnvOK` | the same |
| Equation writer | `ploc`, `FontGauge`, `cx`, `cy` and others | only unnamed ones |

- On the 48SX and 48GX every parameterized outer loop binds `SavedUI`
  (RPLMAN: the outer loop saves the interface it replaces and restores
  it on exit, src: [[sources/rplman]] 21.4); the environments from the
  newest down to the first `SavedUI` belong to the innermost loop, so a
  box over a form does not read as the form. The 49G binds no `SavedUI`;
  there the newest environment is the innermost loop's.
- The user's strings appear among the bound objects (`INFORM`'s title,
  `CHOOSE`'s title on the 48GX); the built-in PLOT form keeps its title
  as a graphic, so a title is not generally readable from here.
- The 49G 1.19-6 ROM binds the same names; a halted program there binds
  `halt`.

## The graphics screen

The pointer to the GROB the display shows below the header
(`Stk_56line_GROB_ptr`, #7055B on the 48SX) equals the PICT pointer
(`graph_GROB_ptr`, #70565) while the graphics screen shows (GRAPH or
PICTURE, `PVIEW`), and differs at the stack (src:
[[sources/hp48-internals-address-list]] for the names; observed). The 48GX
and 49G keep them at #806DA and #806E4. The display controller's start
address (#120) points into PICT at the same time
([[hardware/display]]).

## Message and choose boxes on the display

The 48GX's message and choose boxes, and the 49G's message box, bind only
unnamed temporaries, so saturnus tells them apart on the display: both
draw a rectangle with a shadow two dots wide on the right and one dot
below; a choose box also has a dark row inside (the rule under its title
or the highlighted item), a message box has none. On the 49G the error
box ("Invalid Syntax", a run-time error) is the same message box
(observed).

## Command line in another loop

The editor's active bit ([[hardware/command-line]]) is set in a form's
field while it is edited, while `INPUT` waits, and in the 49G's equation
writer, which edits on the command line; only with the stack in control
is it the user's command line at the stack (observed).

- On the 49G the nibble at #80EC8 reads #F in the interactive stack too,
  not only while an error box shows ([[hardware/command-line]], "Errors
  after ENTER"); the return stack tells them apart (observed).

## Not found

- No reliable sign in RAM was found for the 48GX's choose and message
  boxes (see above), for the interactive stack (a parameterized outer
  loop with no named temporaries) or for `WAIT`.
- Titles and message texts in general.

## Open

- Other ROM revisions of the 48SX and 48GX are not checked.
- The 38G, 39G and 40G (aplets, no RPL stack) are not covered.
