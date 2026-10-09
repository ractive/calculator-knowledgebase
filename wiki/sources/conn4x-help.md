---
title: "Graves, Conn4x 2.0 help (XModem server, connection, HP48/HP49 operations)"
type: source
authors: ["William G. Graves"]
year: 2002
raw: "raw/protocols/xserv-conn4x/conn4xhelp/HelpConn4x/Connectivity/"
status: digested
tags: [xserv, xmodem, hp48, hp49g, iopar]
---

# Graves, Conn4x 2.0 help (XModem server, connection, HP48/HP49 operations)

HTML help pages of Conn4x 2.0 (also as `.chm`). Read: "XModem Server
Menu.htm", "XModem Library.htm", "ConnectivityCom.htm",
"ConnectivityHP48Ops.htm", "ConnectivityHP49Ops.htm". The rest is UI help.
Same non-commercial terms as the source. Cite by page file name.

- XSRVR48 library menu: XSERV, XGET, XPUT, XRECV, XSEND (XModem Server Menu).
- IOPAR for XSERV: `{ 9600 0 0 0 x x }` (9600 baud, no parity, no pacing;
  checksum and translation don't matter) (ConnectivityCom).
- 49G: start XSERV with right-shift, a pause, then right-arrow, or from the
  CAT list; pressing them close together starts the Kermit server instead
  (ConnectivityHP49Ops; Conn4x ReadMe).
- 49G Transfer screen: Type can be set to XModem but resets to Kermit when
  the screen is left; XSEND/XRECV buttons do manual transfers
  (ConnectivityHP49Ops).
- 48: built-in XSEND/XRECV are on the second page of the I/O menu; there is
  no built-in XModem server, the XSRVR library provides one, in port 0 or 1
  only (ConnectivityHP48Ops; [[sources/xsrvr48-readme]]).
