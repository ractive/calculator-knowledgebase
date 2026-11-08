---
title: "Zampach, WFX-HPLX 1.2: Filer protocol text, recorded session, instructions"
type: source
authors: ["Pavel Zampach", "Rodrigo Serra"]
year: 2005
raw: "http://web.archive.org/web/2010id_/http://mizj.com/wfx_hplx.zip"
status: digested
tags: [filer, protocol, serial]
models: [100lx, 200lx]
---

# Zampach, WFX-HPLX 1.2

A Total Commander plugin (2003-2005) that browses and copies files on a
100LX/200LX over the Filer protocol. GPL-2.0-or-later. The S.U.P.E.R.
archive (<http://mizj.com/>) lists it as `wfx_hplx.zip` and calls it "the
most complete description of the Filer communications protocol"; the
mizj.com copy is gone, the archive was fetched from the Wayback Machine
(URL above) on 2026-11-08. Not in `raw/`.

Three documents of the package were read; its Pascal source was not
opened:

| Item | File | What it holds |
| --- | --- | --- |
| Z1 | `protocol/Filer protocol.txt` (Serra 2002, Zampach 2003-2005) | wire settings, packet layout with the CRC-excluded bytes marked, the CRC update for one byte, request and reply data of 28 commands, their codes, the listing entry and the disk-information layout |
| Z2 | `protocol/example.TXT` | a session between two 200LX machines, recorded as hex with sync, end and CRC stripped: connect, drive, directory, listing, write a file, set its date, read it back, delete it, disconnect |
| Z3 | `Instructions.html` | version history (1.01: "handshakeless communications", three-wire cable), setup on the palmtop, limitations (rename only within one drive) |

Reliability: Z1 is a reverse-engineered description and has typos (see
[[protocols/filer-hplx]] "Contradictions"); Z2 is a recording of HP's own
client and server and outranks Z1 where they differ. Tables and the
recording are GPL material: facts only, nothing transcribed.

Facts used on [[protocols/filer-hplx]] and [[hardware/hp100lx-200lx]].
