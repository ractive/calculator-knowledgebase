---
title: "Serra, HP200LX Filer protocol (j200lx project)"
type: source
authors: ["Rodrigo Serra"]
year: 2002
raw: "https://j200lx.sourceforge.net/Filer.html"
status: digested
tags: [filer, protocol, serial]
models: [100lx, 200lx]
---

# Serra, HP200LX Filer protocol

One web page of the j200lx project (a Java client for the palmtop's
Filer), dated 09/02/2002, read on 2026-11-08. Web page, not in `raw/`. The
project's SourceForge licence is LGPL-2.0; the page states none. The
project's code was not opened.

What it covers: the packet layout in both directions, the CRC update for
one byte (reflected polynomial 0xA001), the 2048-byte packet data limit,
and 15 command codes with their data: connect, disconnect, remove and
make directory, delete, rename, list a directory, get and send a file. The
author warns that the page has inaccuracies.

Reliability: the first public description, superseded in detail by
[[sources/wfx-hplx]], which builds on it and differs in several places
(see [[protocols/filer-hplx]] "Contradictions"). It calls the byte after
the counter "STAT", "the result of the operation", and shows it as 0x81 in
every reply.

Facts used on [[protocols/filer-hplx]].
