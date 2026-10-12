---
title: "HP Museum forum: threads on the name RPL, Pioneer and Clamshell ROM dumps, and how to search the forum"
type: source
authors: ["Museum of HP Calculators forum posters (named per thread)"]
year: 2016
raw: "https://www.hpmuseum.org/forum/"
status: digested
tags: [history, rpl]
models: [48sx, 48gx, 49g]
---

# HP Museum forum

The Museum of HP Calculators' forum, read on 2026-10-12 through a headless
Firefox (the plain fetch of the earlier attempt hit a Cloudflare check; the
browser did not, and no challenge was shown). Web pages, not in `raw/`.

## Threads read

| # | Thread | Posts used |
| --- | --- | --- |
| F1 | "origin acronym RPL", 2016-09-27, <https://www.hpmuseum.org/forum/thread-6934.html> | Didier Lachieze points to Wickes' note [[sources/wickes-rpl-name]] |
| F2 | "How much LISP is in RPL?", 2014-04-22, <https://www.hpmuseum.org/forum/thread-1140.html> | Didier Lachieze quotes Wickes' "The HP-28C: an insider's perspective", HPX Exchange V1N1, January/February 1987 |
| F3 | "Hp-19Bii ROM dump -- is it possible?", 2014-11-18 to 2023-12-06, <https://www.hpmuseum.org/forum/thread-2447.html> | Mark Hardman, Eric Smith (brouhaha), Christoph Gießelink, J-F Garnier |
| F4 | "Looking for HP-42S ROM image", 2004-06-29, old forum archive 14, message 59526 | Thomas Okken, J-F Garnier |
| F5 | "HP42s ROM", 2012-06-25, old forum archive 21, message 225619 | replies saying 42S images circulate on a hobbyist site |

F3-F5 were read on 2026-10-12 for [[hardware/saturn-models]] (ROM
availability):

- F3: the 19B and 19BII have no memory scanner, so the IR dump of the
  high-end Pioneers does not apply (Hardman, Smith). Gießelink (2014-11-20)
  says some of the images his Emu42 runs were never dumped: about ten
  years earlier some people at HP were interested in an emulator for some
  models and he was given access to some images; several others were lost
  since. He knows only theoretical methods: on the 18C the Saturn bus is
  reachable directly, so a self-test's ROM check would show the ROM on
  it; on the 1LU7 Bert, 1LR2 Lewis and 1LR3 Sacajawea the bus is inside
  the chip, and HP's technical documents mention a debug mode, selected
  through pins at start-up, that shows bus data on other pins, without
  giving the pin layout. Smith describes decapping chips with fuming
  nitric acid to read the ROM optically, which destroys the calculator.
  Garnier suggests asking HP to release the images. A 2023 reply says the
  Pioneer and Clamshell images are reachable from the emulator author's
  site in some form (not checked; no location recorded here).
- F4: Garnier relays that Erik Ehrling's HP-42S site had announced that
  HP was "looking into plans to make the 42s ROM available"; nothing
  followed in the thread.
- F5: in 2012 42S images circulated on a hobbyist emulator site (now
  offline); a member compares them with his own IR dump.

The quoted 1987 article (F2) says: the team that had written the HP-71
Math and FORTH/Assembler ROMs went on to design a new calculator
operating system; it combines Forth and Lisp and was called "RPL"
(Reverse-Polish LISP) informally at HP; RPL execution generalises the
Forth inner loop, and the Saturn in the new products gained the opcode
PC=(A) to speed up threaded execution; the list handling and local
("lambda") variables come from Lisp; the HP-18C was the first product on
the new system, the HP-28C the second (F2, Wickes 1987 as quoted).

## What the forum does not have

Thread searches (titles and posts) of the current forum (2013 onwards)
for 49G boot loader, flash, bricked units, ShellOS, Doom on the 48, Forth
on the 48, mask ROM, Saturn operating system and Saturn boot loader found
nothing on these topics beyond the two threads above. MyBB ignores search
words under four characters, so "49G" alone is not searchable; "HP49G"
is.

The old forum archives (21 archives, March 1999 to 2013,
<https://www.hpmuseum.org/archives.htm>) have their own keyword search
(the "Search / Personalize Display" form of each archive). Searches of
all 21 archives for "49G boot", "mask ROM", "Saturn Forth" and
"operating system 48" returned mostly threads about older HP models
(HP-41, Voyager, HP-67) and nothing on the questions above. The 48 and 49
discussions of that period happened on comp.sys.hp48
([[sources/comp-sys-hp48-archive]]).
