---
title: "Ishijima, BSD Filer and LXFS4D readmes (Filer server emulators)"
type: source
authors: ["Dai Ishijima"]
year: 1997
raw: "http://vvvvvv.wheel.gr.jp/~dai/software/lx-filer.html"
status: digested
tags: [filer, server, emulator]
models: [100lx, 200lx]
---

# Ishijima, BSD Filer and LXFS4D

BSD Filer (1996) is a Unix program that pretends to be a palmtop whose
Filer is in server mode, so that a 100LX/200LX's Filer can work on Unix
files as its remote side. LXFS4D (1996-1997) is its port to DOS. Freeware
under the author's own terms: free use, porting and free redistribution
of the unmodified archive; no use of its code in commercial programs. Read
on 2026-11-08 (Japanese, ISO-2022-JP); web pages, not in `raw/`. The
source archives were not opened for this wiki.

| Item | Page | Used for |
| --- | --- | --- |
| B1 | BSD Filer v0.2 readme, <http://vvvvvv.wheel.gr.jp/~dai/software/bsdfil02-readme.html> | roles (the machine that receives the connect becomes the server), palmtop keys, test setups and speeds, limitations, "Remote Connection Broken", references (two BSC texts) |
| B2 | BSD Filer page with v0.3, <http://vvvvvv.wheel.gr.jp/~dai/software/lx-filer.html> | v0.3 runs on Linux and Solaris too; adds `-root`, `-speed` and copying whole Unix directories to the LX |
| B3 | LXFS4D v0.0 readme, <http://vvvvvv.wheel.gr.jp/~dai/software/lxfs4d00-readme.html> | test setups and speeds with a 200LX; LXFS4D also ran on a 200LX as the server |

Reliability: the author's own reports of his tests; good for how HP's
client behaves against a server, not for HP's server's error replies.

Facts used on [[protocols/filer-hplx]] and [[hardware/hp100lx-200lx]].
