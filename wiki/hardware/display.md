---
title: "Display controller and LCD"
type: hardware
models: [48sx, 48gx, 49g, 38g, 39g, 40g]
status: draft
sources: ["[[sources/mastracci-saturn-guide]]", "[[sources/saturn-tutorial]]", "[[sources/voyage-48gx]]", "[[sources/hp48-faq]]", "[[sources/hdwreg-taplin]]", "[[sources/screen-brittenson]]", "[[sources/keyboard-ervin]]"]
tags: [saturn, display, lcd]
---

# Display

## Geometry and refresh

- 131 x 64 pixels on all Clarke/Yorke models (38G, 39G/40G, 48S/SX/G/GX,
  49G), driven by the Clarke/Yorke plus two column-driver chips (src:
  [[sources/saturn-tutorial]] p. 165; [[sources/mastracci-saturn-guide]] 4.2).
- One row is refreshed per 4096 Hz tick, 64 rows, so 64 Hz per frame. A row
  takes about 22-23 microseconds out of every 244. Display memory is ordinary
  RAM (unified memory), and the CPU is stalled while the controller fetches a
  row, which slows the CPU. Clearing DON (#100 bit 3) stops the refresh and
  the stalls (src: [[sources/saturn-tutorial]] p. 165, 169).
- The screen is drawn from two bitmaps: a main area whose start address is
  at #120 and whose last row is set via #128, then the menu bitmap from #130 for
  the remaining rows (src: [[sources/mastracci-saturn-guide]] 4.2;
  [[sources/saturn-tutorial]] p. 166).
- The row counter read from #128 counts down: #3F at the top row, #0 at the
  bottom (src: [[sources/saturn-tutorial]] p. 165).
- All display addresses are byte aligned: a bitmap must start at an even
  nibble address (p. 166).

## Registers

| Addr | Bits | Meaning |
| --- | --- | --- |
| #100 | 0-2 | horizontal (left) pixel offset 0-7; Mastracci's "vertical" is wrong (see Open) |
| #100 | 3 | display enable |
| #101 | 0-3 | contrast bits 0-3 |
| #102 | 0 | contrast bit 4 |
| #102 | 1-3 | display test |
| #103 | 0-3 | display test; bit 3 no-refresh mode, "possibly dangerous" |
| #10B | 0-3 | annunciators: left shift, right shift, alpha, alert (bell) |
| #10C | 0-2 | annunciators: busy, I/O, XTRA (reserved) |
| #10C | 3 | annunciators enabled |
| #120-#124 | | main bitmap start address, 5 nibbles (W/O); odd values have bit 0 cleared |
| #125-#127 | | line offset, 3 nibbles (W/O): added at end of each row, the right margin |
| #128-#129 | read | current refresh row (6 bits), then M32 (bit 2 of #129), DA19 (bit 3) |
| #128-#129 | write | number of main-bitmap rows before switching to the menu bitmap, plus M32, DA19 |
| #130-#134 | | menu bitmap start address (W/O) |

(src: [[sources/mastracci-saturn-guide]] 4.2)

- The 6-bit line count is written as a byte together with M32 and DA19
  in its top two bits; the example code writes #37 to restore the menu
  and #3F to hide it (6.1 code), so the value is the index of the last main row
  (inferred: #3F = 63 gives 64 rows, #37 = 55 gives 56 rows plus 8 menu rows).
- DA19 (bit 3 of #129; 1 = upper ROM, 0 = port 2) also switches the
  upper-ROM / port-2 multiplexer on the GX ([[hardware/memory-controller]], 4.4); a display routine that writes
  the byte must preserve it (6.1 code masks #C0).
- The current-row readback is usable for vertical-sync timing: greyscale code
  waits for the row counter to pass a given row before flipping buffers (6.1).

## Main area: margins and scrolling

- #100 bits 0-2 (OFF0-OFF2, "BITOFFSET") shift the main area left by 0-7
  pixels; for more, add 2 nibbles (8 pixels) to the start address. This
  answers the axis question: it is horizontal (src:
  [[sources/saturn-tutorial]] p. 167; [[questions/display-offset-axis]]).
- #125-#127 (LINENIBS) is a signed 3-nibble right margin: extra nibbles added
  after each row. A 131-pixel row occupies 34 nibbles (17 bytes). In general
  next row = this row + 34 + right margin + 2 x floor(left offset / 4),
  rounded down to an even nibble count. So a GROB w pixels wide needs
  right margin = 2 x ceil(w / 8) - 34 - 2 x floor(offset / 4) (p. 167-168,
  restated). A negative margin, e.g. #FBC = -68, draws rows bottom-up (p.
  168).
- The menu area is always 131 pixels (34 nibbles) wide with no margins
  (p. 169).
- LINECOUNT write: the 6-bit index of the last main-area row. #37 gives 56
  main rows and 8 menu rows; #3F removes the menu. Writing 0 or 1 behaves
  like #3F: the main area cannot be empty (p. 166).

## Voyage additions

- Turning the display off (#100 bit 3 = 0) frees the bus from refresh
  fetches and speeds the machine up by about 13%; it also makes the keyboard
  inactive (src: [[sources/voyage-48gx]] p. 193).
- Contrast is 5 bits (bit 4 in #102); ON+ and ON- only move it within #3-#13
  (p. 193).
- Setting bit 3 of the scan register (#103, "Balayage") stops the row scan,
  so every row is drawn on the same line of the panel; doing this for long
  can damage the LCD (p. 193; OCR prints #105, the table on p. 192 shows the
  register is #103).
- The bitmap start address ignores bit 0 because the display circuit is
  byte-wide; to show a bitmap at an odd nibble, use a left margin of 4-7
  (0-3 for even addresses) (p. 201).
- The right margin at #125 counts nibbles with bit 0 ignored and is signed;
  a suitable negative value with the start address at the last row turns the
  picture upside down (p. 201).
- The row counter ("Vsync", #128 read, 6 bits) counts down; a full refresh
  takes 1/64 s. For tear-free scrolling, wait for it to reach 0 and then
  1/(64 x 64) s (p. 202).
- The OS save copies on the G/GX: #8068D main bitmap address, #80695 menu
  bitmap address (OCR: #80696), #80692 right margin, #8069A LINECOUNT byte
  (p. 202); these match the tutorial's ghost-register table.

## Pixel order

Within each nibble of a bitmap the least significant bit is the leftmost
pixel; rows are padded to whole bytes (src: [[sources/hp48-faq]] 8.18). See
[[protocols/hp-object-format]] for the GROB object layout.

## Emu48 findings

The line counter runs 63 down to 0 and restarts from the LINECOUNT value when
the display is switched on; nothing is drawn while DON is clear; address and
offset registers take effect nibble by nibble (src: [[emulators/emu48]]
Display). Contrast reset values and keyboard limits per model are on
[[emulators/emu48]]. Voyage's ON+/ON- range #3-#13 (3-19) matches Emu48's
48SX limits, not its 48GX limits (9-24) (src: [[sources/voyage-48gx]] p.
193; [[sources/kml20]] LCD), see [[questions/contrast-range-48gx]].

## Bit names (Giesselink)

| Reg | Name | Bits |
| --- | --- | --- |
| #100 | BITOFFSET (R/W) | 3 DON display on; 2-0 OFF2-OFF0 left shift |
| #101 | CONTRAST (R/W) | 3-0 CON3-CON0 (higher = darker) |
| #102 | DTEST (R/W) | 3-1 VDIG, LID, TRIM, internal, keep 0; 0 CON4 |
| #103 | DSPCTL (W/O) | 3-0 LRT, LRTD, LRTC, BIN, internal, keep 0; "may damage your display" |
| #10B | ANNCTRL (R/W) | 3-0 LA4 alert, LA3 alpha, LA2 right shift, LA1 left shift |
| #10C | ANNCTRL+1 (R/W) | 3 AON annunciators on (independent of DON); 2 XTRA unused; 1 LA6 transmitting; 0 LA5 busy |
| #120-#124 | DISP1CTL (W/O) | main start address, LSB first |
| #125-#127 | LINENIBS (W/O) | right margin, signed, LSB first |
| #128-#129 | LINECOUNT | write: LC5-LC0 last main row, bit 6 M32, bit 7 DA19; read: LC5-LC0 current row, M32, DA19 |
| #130-#134 | DISP2CTL (W/O) | menu start address, LSB first |

(src: [[sources/saturn-tutorial]] p. 169-171.) M32 is "unused, leave
unchanged" here; Taplin says setting it remaps the display (below).

## RAM ghost registers

Because DISP1CTL, LINENIBS, LINECOUNT and DISP2CTL cannot be read back, the
OS keeps the last written values in RAM (src: [[sources/saturn-tutorial]] p.
166):

| Register | 38G | 48SX | 39G/40G/48G/49G |
| --- | --- | --- | --- |
| DISP1CTL | #F062C | #7050E | #8068D |
| LINENIBS | #F0631 | #70513 | #80692 |
| LINECOUNT | #F0639 | #7051B | #8069A |
| DISP2CTL | #F0634 | #70516 | #80695 |

An emulator does not need these, but they are useful for debugging ROM
behaviour and they reveal where RAM sits on each model.

## Other witnesses

- Taplin: #100 bits 0-1 give a 4-pixel offset "useful for smooth scrolling";
  bit 2 "is also involved" but disturbs the scan length; bit 3 "puts the
  machine into a coma" (consistent with display enable) (src:
  [[sources/hdwreg-taplin]]).
- Taplin: #102 bits 1 and 3 and #103 bit 3 control the LCD drive
  voltage; #102 bit 2 unknown (src: [[sources/hdwreg-taplin]]). Mastracci calls the
  same bits "display test" (4.2). Emulators can ignore them but must store
  them.
- Taplin: #128 is normally 7 and #129 bits 0-1 normally 3, i.e. the byte #37
  seen in Mastracci's code; setting #129 bit 2 (M32) "can totally remap your
  calculator's display", recovered only by power-off (src:
  [[sources/hdwreg-taplin]]).
- Taplin: the display start address is "usually #F097C (or #F09BC with the
  equation card installed)", typed nibble-reversed. Read as a 48SX RAM
  address this is #C790F, which is outside the #70000 RAM window, so the
  value is unclear (src: [[sources/hdwreg-taplin]]; see
  [[questions/display-start-address-taplin]]).
- The 48SX ROM keeps pointers to a 131x56 stack GROB and a 131x8 menu GROB
  (src: [[sources/screen-brittenson]]), matching 56 main rows plus 8 menu rows.
- The ROM keeps a RAM shadow of the annunciator nibbles #10B/#10C
  (48SX: #706C3) (src: [[sources/keyboard-ervin]] 3.4).

## Overhead projector signals

Port 1 carries the LCD data stream on pins 33-36 (XSCL clock, LP line sync,
LD0/LD1 data). Per row 131 columns are sent left to right, in the 64 shifts
before each LP pulse and the 2 after; there is no vertical sync. On port 2
these pins are address lines A19-A21 and BEN (src:
[[sources/mastracci-saturn-guide]] 4.3).

## Physical

Two SED1181 column drivers (one mirrored) and an Epson LD-F8845A panel (src:
[[sources/mastracci-saturn-guide]] 5.7, 5.9).

## Memory remaps while the display is on

- The 48SX ROM J (at #0C0B2-#0C0FA, interrupts disabled) and the 48GX
  ROM R (at #72386-#72D6B, interrupts enabled) unconfigure the built-in RAM
  that holds both display bitmaps and configure it again with another size
  mask, many times while busy: about 1500 times in 70 s of boot, arithmetic
  and the PLOT menu on the SX, 31 times after "Memory Clear" on the GX. The
  gaps mostly last about 200 cycles, at most 466 cycles on the SX (233 us)
  and 1021 on the GX (255 us), about one row period (244 us). The 49G ROM
  showed one 136-cycle gap during boot (observed in saturnus traces,
  2026-10-05).
- If the row fetches decode through the chip selects (inferred: the display
  controller and the memory controller are in the same Clarke/Yorke chip
  and display memory is ordinary RAM, src: [[sources/saturn-tutorial]] p.
  165), a gap reaches only the one or two rows fetched during it, for one
  1/64 s frame, which is not visible. An emulator that renders all 64 rows
  at one instant inside a gap shows NCE1 data on every row; saturnus keeps
  the picture from before the UNCNFG until CONFIG restores the decoding, or
  for at most one frame (decision log, 2026-10-05).

## Open

- Mastracci calls #100 bits 0-2 a vertical offset; Giesselink shows it is a
  horizontal (left) shift. Answered: [[questions/display-offset-axis]].
- Tutorial p. 170 places LINENIBS at #123; p. 167 and Mastracci place it
  at #125, which is consistent with DISP1CTL occupying #120-#124.
- Exact row-timing and when address registers take effect (start of frame?).
- Whether the controller's row fetches go through the configurable chip
  selects (so a fetch during an UNCNFG gap reads NCE1), or reach the RAM by
  another path; no source says. See "Memory remaps while the display is
  on".
- DA19 polarity in #129 bit 3: answered, [[questions/da19-polarity]].

## Menu labels

- Every ROM (48SX J, 48GX R, 38G A1.67, 49G 2.10, 39G/40G) draws the six
  softkey labels in the 8-row menu area as 21-pixel-wide boxes with a
  1-pixel gap, from column 0: label i covers columns 22 i to 22 i + 20, so
  the label pitch is 22 pixels and the first label is centred 10.5 pixels
  from the left edge (observed on saturnus's framebuffer dumps of all six
  ROMs at their HOME menus, 2026-10-05). On a drawn case the labels sit
  over the six menu keys when the display's active width is 131 / 22 =
  5.95 menu-key pitches and its left edge is 10.5 / 22 = 0.48 pitches left
  of the first menu key's centre (how saturnus places its skins).
