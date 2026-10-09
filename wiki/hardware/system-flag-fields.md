---
title: "System flags: multi-flag fields as shown"
type: rom-behaviour
models: [48sx, 48gx, 49g]
status: draft
sources: ["[[sources/hp48sx-om]]", "[[sources/hp48g-ug]]", "[[sources/hp49g-pocket-guide]]"]
tags: [flags, rom, hp48, hp49g]
---

# System flags: multi-flag fields as shown

The system flag pages keep each multi-flag field (a row with a range of
flags) in their Clear column as found in the guides, with notes on what the
guides leave open: [[hardware/system-flags-48sx]],
[[hardware/system-flags-48gx]], [[hardware/system-flags-49g]]. This page
holds two things about the same fields for a user-facing flags list
(saturnus' `scripts/flags-json.py`, its memory view's Flags tab):

- **Shown**: the field's description in plain words, used instead of the
  Clear column where that column holds notes about the sources. `-` keeps
  the Clear column.
- **Values**: the field's named settings and the flags each one needs, so a
  front end can name the setting the flags hold now. Grammar: settings
  separated by `;`, each `Name: -N set|clear[, -M set|clear]`; a flag a
  setting does not list does not matter to it. No two settings may match the
  same combination of flags. A combination no setting matches is one the
  guide does not name (the 48SX's -17 and -18 both set). `-` for a field
  without named settings (a number held in bits).

Both are the model pages' facts restated, no new ones; the source column
names where each model page has them.

## Fields

| Model | Flags | Shown | Values | Source |
| --- | --- | --- | --- | --- |
| 48sx | -5..-10 | Six flags together set the binary word size, 1 to 64 bits. | - | [[sources/hp48sx-om]] p. E-2 |
| 48sx | -11..-12 | - | DEC: -11 clear, -12 clear; BIN: -11 clear, -12 set; OCT: -11 set, -12 clear; HEX: -11 set, -12 set | [[sources/hp48sx-om]] p. E-2 |
| 48sx | -15..-16 | - | Rectangular: -15 clear, -16 clear; Polar/cylindrical: -15 clear, -16 set; Polar/spherical: -15 set, -16 set | [[sources/hp48sx-om]] p. E-2 |
| 48sx | -17..-18 | - | Degrees: -17 clear, -18 clear; Radians: -17 set, -18 clear; Grads: -17 clear, -18 set | [[sources/hp48sx-om]] p. E-2 |
| 48sx | -45..-48 | Four flags together set how many digits Fix, Sci and Eng show. | - | [[sources/hp48sx-om]] p. E-5 |
| 48sx | -49..-50 | - | Std: -49 clear, -50 clear; Fix: -49 set, -50 clear; Sci: -49 clear, -50 set; Eng: -49 set, -50 set | [[sources/hp48sx-om]] p. E-5 |
| 48gx | -5..-10 | Six flags together set the binary word size, 1 to 64 bits. | - | [[sources/hp48g-ug]] p. D-1 |
| 48gx | -11..-12 | - | DEC: -11 clear, -12 clear; BIN: -11 clear, -12 set; OCT: -11 set, -12 clear; HEX: -11 set, -12 set | [[sources/hp48g-ug]] p. D-2 |
| 48gx | -15..-16 | - | Rectangular: -16 clear; Polar/cylindrical: -15 clear, -16 set; Polar/spherical: -15 set, -16 set | [[sources/hp48g-ug]] p. D-2 |
| 48gx | -17..-18 | - | Degrees: -17 clear, -18 clear; Radians: -17 set; Grads: -17 clear, -18 set | [[sources/hp48g-ug]] p. D-2 |
| 48gx | -45..-48 | Four flags together set how many digits Fix, Sci and Eng show. | - | [[sources/hp48g-ug]] p. D-5 |
| 48gx | -49..-50 | - | Std: -49 clear, -50 clear; Fix: -49 set, -50 clear; Sci: -49 clear, -50 set; Eng: -49 set, -50 set | [[sources/hp48g-ug]] p. D-5 |
| 49g | -11..-12 | - | DEC: -11 clear, -12 clear; BIN: -11 clear, -12 set; OCT: -11 set, -12 clear; HEX: -11 set, -12 set | [[sources/hp49g-pocket-guide]] p. 76 |
| 49g | -15..-16 | - | Rectangular: -16 clear; Cylindrical: -15 clear, -16 set; Spherical: -15 set, -16 set | [[sources/hp49g-pocket-guide]] p. 76 |
| 49g | -17..-18 | - | Radians: -17 set; Degrees: -17 clear, -18 clear; Grads: -17 clear, -18 set | [[sources/hp49g-pocket-guide]] p. 76 |
| 49g | -49..-50 | - | Std: -49 clear, -50 clear; Fix: -49 set, -50 clear; Sci: -49 clear, -50 set; Eng: -49 set, -50 set | [[sources/hp49g-pocket-guide]] p. 77 |
