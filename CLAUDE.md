# calculator-knowledgebase

A wiki about the internals of HP's Saturn-based calculators (HP 48S/SX/G/GX,
HP 49G, HP 38G, HP 39G, HP 40G) and the HP 42S: hardware, ROM behaviour,
serial transfer protocols, file formats and existing emulators. Public,
CC BY 4.0. It feeds saturnus (a clean-room Saturn emulator,
https://github.com/ractive/saturnus) and hptx (HP file transfer,
https://github.com/ractive/hptx). saturnus code cites pages as
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
  two, in quotes, attributed. No copied tables, code or page layouts from
  any document.
- GPL emulator source code (Emu48, Emu42, x48ng, saturnng, ...) is never
  copied or paraphrased here. Reading it to learn a hardware fact is
  allowed: record the fact with a citation and no code, no function names,
  no data-structure layouts. Prefer the emulators' manuals and change logs
  and black-box runs; say which.
- The same applies to other source code with restrictive terms (the Conn4x
  sources are non-commercial): facts only.
- No ROM images, and no private material (photographs of someone's own
  calculators or manuals, local file paths, personal data).

## Getting the sources

```sh
scripts/fetch-sources --dry-run
scripts/fetch-sources          # fetch + zip-member entries into raw/, SHA-256 checked
scripts/fetch-sources --derive # also the .txt conversions (textutil, cp)
```

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
| `hardware/` | `hardware` | One page per subsystem (saturn-cpu, memory-controller, io-ram, display, keyboard, timers, uart, interrupts, ...) and one per model. |
| `protocols/` | `protocol` | Kermit, the HP Kermit quirks and server commands, XModem and its HP variants, HP object/binary format, IOPAR. |
| `emulators/` | `emulator-note` | Facts learned from Emu48 and other emulators' documentation: hardware behaviours they encode. Facts only. |
| `decisions/` | `decision` | Design decisions, ADR style. |
| `questions/` | `question` | Open questions, contradictions between sources, things to verify on hardware. |
| `synthesis/` | `synthesis` | Answers worth keeping: comparisons, analyses, roadmaps. |
| `index.md` | `meta` | Catalogue of every page with a one-line summary, by directory. Updated on every ingest. |
| `log.md` | `meta` | Append-only. Entry prefix `## [YYYY-MM-DD] ingest|query|lint|decision \| title`. |
| `overview.md` | `meta` | The current big picture in one page. |

## Page conventions

- Filenames: kebab-case, no dates in names.
- Frontmatter per type is enforced by `.hyalo.toml`. Common keys: `title`,
  `type`, `tags`, `status`, `sources` (list of `[[sources/...]]` links),
  `models` (subset of `48sx, 48gx, 49g, 38g, 39g, 40g`).
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
