# calculator-knowledgebase

Notes on the internals of the HP 48SX/GX, 49G, 38G, 39G/40G and 42S calculators: hardware, ROM behaviour, protocols and file formats. Not affiliated with or endorsed by HP.

## What it is

A wiki of markdown pages in `wiki/`, one page per subject:

| Directory | Contents |
| --- | --- |
| `wiki/hardware/` | The Saturn CPU, memory controller, I/O registers, display, keyboard, timers, UART, interrupts, and one page per model |
| `wiki/protocols/` | Kermit and the HP Kermit server, XModem and the HP variants, the HP object and binary file formats, IOPAR |
| `wiki/emulators/` | What existing emulators document about the hardware (facts only) |
| `wiki/sources/` | One page per source document: what it covers, how reliable it is, where the useful parts are |
| `wiki/questions/` | Open questions, contradictions between sources, things to check on real hardware |
| `wiki/decisions/`, `wiki/synthesis/` | Design decisions and longer analyses |
| `wiki/index.md` | A catalogue of every page |
| `wiki/overview.md`, `wiki/log.md` | The big picture, and a dated log of what was added |

Start with `wiki/index.md`. The pages are plain markdown with YAML
frontmatter and `[[dir/page]]` links, so they read in any editor, in
Obsidian, or with [hyalo](https://github.com/ractive/hyalo)
(`.hyalo.toml` points it at `wiki/`; `hyalo lint` checks the frontmatter).

## How pages cite sources

Every hardware or protocol fact carries a citation inline, for example
`(src: [[sources/voyage-48gx]] p. 212)`: the source page and the page or
section in the document. Claims without a source are marked
`(unverified)`. Facts observed by running a ROM on an emulator say so, with
the date and the ROM. Contradictions between sources are kept on the page
under a `Contradictions` heading and get a page in `questions/`.

## Clean-room purpose

The notes exist so that people can write independent implementations
(emulators, transfer tools) without copying anyone's code or text. Facts
are restated in our own words; quotations are kept to a sentence or two
and attributed. GPL emulator source code is not reproduced here, and nothing
here is taken from it: the emulator pages draw on the emulators' manuals
and change logs, and on black-box runs. Emulator change logs and manuals
were read for facts, never their source code. One exception is HP's
Conn4x connectivity kit, whose source was read for the XSERV and HP XModem
facts because no written specification exists: facts only, no code or
routine names. The other is the maintainer's own HPComm/HPGComm source
(GPL-2, 1999-2001, written mainly by the maintainer with Mitch Davis and
Colin Croft), which may be read the same way for protocol and file-format
facts, such as the 38G/39G directory file. If you implement from these notes,
implement from the notes, not with someone else's source open beside you.

No ROM images are included.

## Projects using it

- [saturnus](https://github.com/ractive/saturnus): a clean-room emulator of
  the Saturn calculators in Rust. Its code cites pages as
  `wiki: hardware/timers`.
- [hptx](https://github.com/ractive/hptx): file transfer to and from HP
  calculators over Kermit and XModem, in Rust.

## Getting the source documents

The documents the wiki cites are not in this repository. Many are
copyrighted, some may only be passed on unchanged, and some are large.
`sources/manifest.json` lists each one: its path under `raw/`, title,
authors, year, where it came from, its size, SHA-256 and terms. A source
page's `raw:` field is that path.

```sh
scripts/fetch-sources --dry-run   # what would be downloaded
scripts/fetch-sources             # download into raw/ and check SHA-256
scripts/fetch-sources --derive    # also make the .txt conversions it can
```

The script needs Python 3.8 or later and nothing else. It makes one request
at a time, skips files already present with the right hash, and lists at
the end what it could not fetch: a few documents hosted on mega.nz, a few
private photographs that are not distributed, and text conversions that
need tools such as `pdftotext` or OCR. A full fetch is about 430 MB. The
sites hosting these files are run by volunteers; please do not run the
script more often than you need to.

## Licence

The text in this repository (the wiki, the manifest and these notes) is
licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/);
see `LICENSE`. The scripts in `scripts/` are licensed under the MIT
licence; see `LICENSE-MIT`. The source documents it cites keep their own terms, which are listed in
`sources/manifest.json`; they are not included and not covered by this
licence. HP is a trademark of HP Inc.
