---
title: "Is the ASCII transfer header %%HP: or %HPHP:?"
type: question
status: answered
tags: [object-format, hptx]
---

# Is the ASCII transfer header %%HP: or %HPHP:?

The FAQ writes the header as `%HPHP: T(3)A(D)F(.);` (src:
[[sources/hp48-faq]] 6.12). `raw/README.md` calls it the `%%HP:` header.
Check the 48G user's guide or AUR I/O chapter and an actual transfer from
the saturnng emulator.

## Answer (2026-10-05)

`%%HP:`. Every ASCII GET from the saturnng emulator (48SX ROM J, 48GX ROM
R, 49G ROM 2.15) starts with `%%HP: T(1)A(D)F(.);` and CR LF (the 49G with
`A(R)`); see [[protocols/hp-object-format]]. The FAQ's `%HPHP:` is a typo.
