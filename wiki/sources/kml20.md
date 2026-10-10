---
title: "Gießelink, EmuXY and KML 2.0 (rev. 2024-04-23)"
type: source
authors: ["Christoph Gießelink"]
year: 2024
raw: "raw/emulator-docs/kml20/KML_20.txt"
status: digested
tags: [emu48, kml, keyboard, display]
models: [48sx, 48gx, 49g, 38g, 39g, 40g, 42s]
---

# Gießelink, EmuXY and KML 2.0 (rev. 2024-04-23)

Documentation of the KML skin language used by Emu28/42/48/71. Read for
hardware facts only: the Hardware/Model/Class keywords (p. 1-2 of the
original, text lines 29-110), the LCD contrast table (lines 236-375) and the
OutIn key-code tables for the HP48SX/GX/38G and HP49G/39G/40G (lines
2032-2286). Cite by section name.

OutIn codes are "OUT bit number, IN value": e.g. `OutIn 1 16` is OUT #002,
IN #10. They agree with [[sources/saturn-tutorial]] p. 136-137 and
[[sources/keyb49-sylvester]] for every key, put left shift at OUT bit 2 /
IN #20 on the 48, and ON at IN #8000. The 38G uses the 48 matrix; the
39G/40G use the 49G matrix. Facts on [[emulators/emu48]] and
[[hardware/keyboard]], [[hardware/display]].

The 42S (read 2026-10-05): Emu42 drives it with `Hardware "Lewis"`, `Model
"D"`, `Class 32` for 32 KB RAM (Global); the contrast table gives range 0-31,
reset 22, keyboard limits 15-31; Emu42 (Lewis) has seven annunciators
(Annunciator); the table "OutIn Codes HP42S" (text lines 1929-2031) gives
the 37 keys on OUT bits 0-5, IN #01-#40, EXIT on IN #8000. Facts on
[[hardware/hp42s]] and [[hardware/lewis]].
