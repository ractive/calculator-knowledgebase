---
title: HP Museum forum, "Summation based benchmark for calculators" (pier4r, 2017-)
type: source
status: digested
tags:
  - benchmark
  - timing
raw: https://www.hpmuseum.org/forum/thread-9750.html
year: 2018
authors:
  - pier4r
  - Bob Prosperi
  - forum members
---

# HP Museum forum, "Summation based benchmark for calculators"

Thread started by pier4r on 2017-12-21 at
<https://www.hpmuseum.org/forum/thread-9750.html> (printable version
`printthread.php?tid=9750`). The first post keeps a running table of
timings for `sum(x=1..n) cbrt(exp(sin(atan(x))))` on real calculators,
grouped by n. Read on 2026-10-05 for the HP 48/49 entries:

- 48SX: n=1000, 95.5 s, result 1395.3462877, program
  `TICKS 'Σ(X=1,CNT,XROOT(3,EXP(SIN(ATAN(X)))))' EVAL SWAP TICKS SWAP - 8192 /`
  (Bob Prosperi, post 135, with a TEVAL variant).
- 48GX: n=100 5.5 s UserRPL / 5.9 s sum function; n=1000 54 s / 55 s;
  n=10000 541 s / 554 s. 48G+ sum function n=1000 55 s.
- 49G ROM 2.10: n=100 5.5 s (sum function or FOR/NEXT, radians), n=1000
  47.8 s sum function / 51.0 s FOR/NEXT radians / 53.9 s degrees, n=10000
  487.3 s / 505.4 s / 534.7 s (post 195).
- 28S UserRPL: n=100 13.2-14 s, n=1000 123-130 s.
- Saturn assembly on a 48G/GX ROM R (post 165): n=1000 34.8 s with
  display and keyboard scanning off, 39.7 s with them on (a 12% display
  cost, close to Voyage's 13%).

Used by [[questions/instruction-speed-vs-hardware]] as the only
real-hardware speed oracle available without owning the machines.
