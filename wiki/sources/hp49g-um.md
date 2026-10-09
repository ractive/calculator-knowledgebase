---
title: "HP 49G User's Manual"
type: source
authors: ["Hewlett-Packard"]
year: 1999
raw: "raw/manuals/hp49g-um-en.pdf"
status: digested
tags: [hp49g, manual, transfer]
---

# HP 49G User's Manual

236-page text PDF, but most body text uses a font whose characters are
shifted by 29 code points in the text layer, so pdftotext output must be
decoded (add 29 to each character) to be readable. The only I/O material is
Appendix A "Connecting to another calculator" (p. A-1 to A-3); read in full.

- PC transfers need a separately purchased HP Connectivity Kit, which can
  also load new versions of the calculator software (A-1).
- 49G to 49G: on both, APPS > I/O FUNCTIONS; receiver chooses "Get from HP
  49", which connects to the sender; sender chooses "Send to HP 49", picks
  objects, SEND (A-1, A-2).
- 49G to HP 48: use the adaptor supplied with the 49G on the cable; only
  user-created objects can be exchanged; on both, the Transfer form must have
  FMT set to ASC and all other settings matching; sender SEND, receiver RECV
  (A-2, A-3).

The I/O reference (commands, IOPAR, XSERV) would be in the image-only
Advanced User's Guide (`hp49g-aug-en.pdf`), which was not opened.

Facts used on [[protocols/kermit-hp]], [[hardware/hp49g]].
