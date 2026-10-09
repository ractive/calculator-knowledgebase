---
title: "The Kermit Project, HP-48 Kermit Hints and Tips"
type: source
authors: ["The Kermit Project, Columbia University", "Joe Horn"]
year: 1999
raw: "raw/protocols/hp48-kermit-columbia.txt"
status: digested
tags: [kermit, hp48, iopar, server, hptx]
---

# The Kermit Project, HP-48 Kermit Hints and Tips

Columbia's web page (1999, updated 2011-07-22), from the Wayback copy; the
`.txt` is a conversion of the `.html`. Collects newsgroup consensus; the
"HP-48 Kermit protocol notes" section is by Joe Horn. Cite by section name.

## Sections

- Communications settings: 9600 8N1 defaults, no flow control, no modem
  signals; IOPAR's six fields.
- Protocol settings: no long packets or windows; block checks 1-3 (3 default
  on most models); control characters must always be prefixed.
- Programs versus data: what ASCII mode does (decompile/compile, backslash
  translation, CR insertion), translation modes 0-3, binary receive slows
  down, HPHP48-x check after a binary receive.
- HP-48 Kermit protocol notes (Horn): which packets and generic commands the
  HP server answers, and what MS-DOS Kermit answers to an HP client using PKT.

## Reliability

Practical and consistent with the HP manuals as far as checked; "seems to
reflect a consensus ... but with no guarantees". Horn's server notes are
based on experiments with PKT and are the most specific source for
[[protocols/server-commands]].
