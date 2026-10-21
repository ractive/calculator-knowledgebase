# calculator-knowledgebase

A wiki about the internals of HP's Saturn-based calculators (HP 48S/SX/G/GX,
HP 49G, HP 38G, HP 39G, HP 40G) and the HP 42S: hardware, ROM behaviour,
serial transfer protocols, file formats and existing emulators. Public,
CC BY 4.0. It feeds saturnus (a clean-room Saturn emulator,
https://github.com/ractive/saturnus) and satx (HP file transfer,
part of saturnus: https://github.com/ractive/saturnus/tree/main/crates/satx-cli).
saturnus code cites pages as
`wiki: hardware/timers`; keep page paths stable.

## Layers

- `raw/`: the source documents. Not in git (`.gitignore`). Fetch them with
  `scripts/fetch-sources` (see below). Never edit a raw file.
- `sources/manifest.json`: one entry per raw file: path, title, authors,
  year, URLs, size, SHA-256, terms, and how to get it.
- `wiki/`: the pages. The only layer you write prose in.
- `CLAUDE.md`: this file. It evolves with the wiki.

## Tooling

Use the `hyalo` CLI (not Read/Grep/Glob) for wiki operations: search,
frontmatter, tags, links, moves, lint. Run it from the repository root;
`.hyalo.toml` sets `dir = "wiki"`, so pass paths relative to `wiki/`
(`hyalo read hardware/io-ram.md`). Orient with `hyalo summary`, then
`hyalo read index.md`. Rename or move pages only with `hyalo mv` (it
rewrites links). Create pages with Write, edit prose with Edit, everything
else through hyalo. `hyalo lint` must be clean before a commit.

## The clean-room rule

This repository is public and exists so that others can build independent
implementations from it. Therefore:

- Facts in our own words, with a citation. Quotations at most a sentence or
  two, in quotes, attributed. No code or page layouts copied from any
  document. Tables of plain facts (flag numbers, register addresses, bit
  meanings) may follow a source's table, with the source cited (owner,
  2026-10-21); their descriptions are still our own words. Not allowed:
  tables whose source forbids changes or redistribution (Mastracci's
  notes), tables from GPL packages (Emu42's PROBLEMS.TXT), and whole
  lists transcribed verbatim (the 49G Pocket Guide's flag list).
- GPL emulator source code (Emu48, Emu42, x48ng, saturnng, ...) is never
  opened for this wiki, not even to look up a fact. Facts about emulators
  come from their published documentation (manuals, KML and skin docs,
  change logs, forum posts by their authors) and from black-box runs; the
  citation says which.
- Other source code with restrictive terms is not opened either. Two
  documented exceptions, both under the same rules (facts only, no code,
  no routine names, no layouts copied from it):
  - The Conn4x sources (non-commercial licence), already read for the
    XSERV and HP XModem facts because no prose specification exists.
  - The owner's own HPComm/HPGComm source (GPL-2, 1999-2001, mainly
    written by the owner, with Mitch Davis and Colin Croft), which may be
    read for protocol and file-format facts, such as the 38G/39G
    directory file.
- No ROM images, and no private material (other people's photographs of
  their calculators or manuals, local file paths, personal data).
- The owner's (@ractive's) own photographs of the owner's calculators and printed
  manuals may be cited as sources (owner, 2026-10-20). The photographs stay
  in `raw/`, out of git; pages take facts from them in their own words, like
  from any other source.

## Getting the sources

```sh
scripts/fetch-sources --dry-run
scripts/fetch-sources          # fetch + zip-member entries into raw/, SHA-256 checked
scripts/fetch-sources --derive # also the .txt conversions (textutil, pdftotext, cp)
scripts/test-fetch-sources     # its tests, offline (a local server, a fake manifest)
```

A file replaces the one in `raw/` only once its SHA-256 matches; manifest
paths must stay inside `raw/`, the manifest size caps a download, and a
`derived` command runs without a shell, limited to the tools in the
script's `DERIVE_TOOLS` (add a new tool there).

`manual` entries (mega.nz files, photographs that are not distributed) and
derived OCR texts are listed with instructions at the end. To add a source:
put it under `raw/`, add a manifest entry (method `fetch` with the direct
URL where one exists, `zip-member`, `manual` or `derived`, plus size and
SHA-256), and write its `sources/` page. Do not run the fetch script
against the hosting sites more often than needed.

## Wiki layout

| Directory | Page type | Purpose |
| --- | --- | --- |
| `sources/` | `source` | One page per document: what it is, what it covers, reliability, where the useful parts are (page numbers). |
| `hardware/` | `hardware` | One page per subsystem (saturn-cpu, memory-controller, io-ram, display, keyboard, timers, uart, interrupts, lewis, ...). |
| `hardware/` | `model` | One page per calculator model (hp48sx, hp48gx, hp49g, hp38g, hp39g-40g, hp42s). |
| `hardware/`, `protocols/` | `rom-behaviour` | What the ROM does, as observed or documented: system flags, command line, system RAM layout, RPL libraries and menus. |
| `protocols/` | `protocol` | Kermit, the HP Kermit quirks and server commands, XModem and its HP variants, XSERV, IOPAR. |
| `protocols/` | `file-format` | The HP object and binary file formats, ASCII transfer format, character set. |
| `emulators/` | `emulator` | Facts learned from Emu48 and other emulators' documentation: hardware behaviours they encode. Facts only. |
| `decisions/` | `decision` | Design decisions, ADR style. |
| `questions/` | `question` | Open questions, contradictions between sources, things to verify on hardware. |
| `synthesis/` | `synthesis` | Answers worth keeping: comparisons, analyses, roadmaps. |
| `index.md` | `meta` | Catalogue of every page with a one-line summary, by directory. Updated on every ingest. |
| `log.md` | `meta` | Append-only. Entry prefix `## [YYYY-MM-DD] ingest|query|lint|decision \| title`. |
| `overview.md` | `meta` | The current big picture in one page. |

## Page conventions

- Filenames: kebab-case, no dates in names.
- Frontmatter per type is enforced by `.hyalo.toml` (`hyalo types list`,
  `hyalo types show <type>`). Common keys: `title`, `type`, `tags`, `status`
  (`stub|draft|reviewed`; questions `open|answered`; sources
  `unread|skimmed|digested`), `sources` (list of `[[sources/...]]` links),
  `models` (subset of `48sx, 48gx, 49g, 38g, 39g, 40g, 42s, 27s, 17b,
  17bii, 28s, 19bii`; which models a page covers goes here, never in a
  tag: no `48gx`, `hp48`, `hp49g` or `28s` tags). The type
  says what a page is; the directory stays fixed because other projects
  cite page paths. Change schemas with `hyalo types set`; the `models`
  item pattern is a string-list constraint, edited in `.hyalo.toml`.
- Every hardware or protocol fact carries a citation inline:
  `(src: [[sources/voyage-48gx]] p. 212)` or
  `(src: [[emulators/emu48]] CHANGES SP43)` (Emu48's change log has
  service-pack numbers, not dates). Unsourced claims are marked
  `(unverified)`. Observations from running a ROM on an emulator say so,
  with the date and the ROM.
- A `source` page's `raw` field is its path under `raw/` (as in the
  manifest); for material outside `raw/` give the URL and say so.
- Never start a wrapped line with `#` followed by hex (`#100`): markdown lint
  reads it as a heading. Rewrap so the address sits mid-line, or write `0x100`.
- Addresses in hex with `#` prefix as in HP docs (`#100h` I/O RAM base);
  nibble addresses unless stated.
- Link liberally with `[[dir/page]]`. A link to a missing page is a todo;
  `hyalo find --broken-links` lists them.
- Contradictions between sources go on the page under a `## Contradictions`
  heading and get a `questions/` page.
- Plain, short British English.

## Operations

**Ingest** a document: read it (PDFs via Read with `pages`; `.txt`
conversions sit next to `.doc`/`.html`, manual texts in `raw/manuals/text/`),
write or update its `sources/` page (status `digested`), extract facts into
the `hardware/`, `protocols/` or `emulators/` pages, raise `questions/` for
anything unclear, update `index.md`, append to `log.md`. Large manuals:
ingest only the relevant chapters and say which.

**Query**: read `index.md`, then the pages; answer with citations. If the
answer is worth keeping, file it under `synthesis/` or `questions/` and log it.

**Lint**: `hyalo lint`, `hyalo find --orphan`, `hyalo find --broken-links`,
`hyalo find --view stubs`, `hyalo find --view open-questions`. Look for stale
or contradicting claims and sources still `unread`. Log the pass.
