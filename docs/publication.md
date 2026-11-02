# Publication

Turning a directory of chapters into something a print-on-demand service will
accept. This is the `book` output type; the other three are at the end.
Publication is the only stage that differs by `[output] type`.

## Two jobs, deliberately split

| Job | Owned by | Why |
| --- | --- | --- |
| Manuscript — concatenation, body typesetting, profile conformity | This pattern | It is downstream of authorship and needs the manifest |
| Print geometry — trim, gutter, spine, cover wrap, pre-flight | [`kdp-publishing`](https://github.com/danielrosehill/KDP-Publishing-Plugin) | It is downstream of the *finished interior*, and it is already solved |

The split is not administrative. **A cover cannot be built until the interior is
final**, because spine width is page count multiplied by paper caliper. A cover
built from an estimated page count produces a book whose spine type sits
off-centre, and nothing on screen reports it — you find out when the proof copy
arrives.

So print geometry is handed off, last, to a tool that refuses to take a page
count and measures the built PDF instead. Reimplementing that here would mean
maintaining a second copy of the one calculation in this pipeline where being
approximately right costs a print run.

The division runs both ways. `kdp-publishing` states plainly that it does not
write your book; this pattern does not typeset it. Typesetting and authorship
have different failure modes, and tools that bundle them tend to be good at
neither.

## The handoff artefact

`longform.py assemble` writes two files:

- `dist/manuscript.md` — every drafted chapter concatenated in manifest order,
  with part dividers, front matter from the manifest, and undrafted chapters
  marked as comments rather than silently omitted
- `dist/manuscript.json` — title, author, snapshot date, chosen profile, handoff
  target, word count, and per-chapter status

The sidecar exists because the next tool should not have to parse prose to learn
what it is being handed, and because "which chapters were actually in this build"
is the first question asked when a proof looks wrong.

## Typesetting profiles

`typeset/profiles/*.toml` describe how the *text* is set: measure, type families,
sizes, leading. They deliberately do **not** describe trim, gutter, spine or
bleed.

One value in a profile leaks upward into authoring: **the measure**. The measure
fixes the maximum monospace line length that will not wrap in print, and that cap
belongs in the style guide.

It is a measurement, not a convention. A book authored against Crown Quarto's
5.62in measure and later moved to KDP's 6x9 will wrap its listings, and the
symptom appears in a proof rather than in a build. Re-derive the cap whenever the
trim or the monospace size changes; never carry the number forward because it was
already written down.

## Channel differences that matter

- **Lulu** offers Crown Quarto (7.44 × 9.68in); **KDP** does not. KDP's 7.5 ×
  9.25in is not offered by Lulu. A book targeting both needs a profile per
  channel, and the two will have different page counts.
- **Colour interior** changes cost per page substantially. It is a spec decision
  taken during definition, not a production decision discovered at upload.
- **Greyscale interiors** require that no figure carries a distinction by hue
  alone — a graphics style guide constraint that has to be set before thirty
  figures are drawn.

## Other output types

- **report** — `longform.py export` produces `dist/report.pdf` via pandoc + typst.
- **screenplay** — units are Fountain files. `longform.py assemble` writes
  `dist/screenplay.fountain`; `export` makes a PDF via afterwriting/screenplain or
  a configured `export_cmd`.
- **audiobook** — the manuscript is the text of record. A `narration-adapter`
  agent writes a spoken script per chapter to `audio/script/` (numbers, symbols,
  acronyms and URLs normalised to spoken form; no visual references; optional
  SSML). `longform.py script-lint` checks them, and `guides/pronunciation.md`
  keeps pronunciations consistent across chapters. `longform.py render` runs an
  engine-agnostic `[audio] render_cmd` once per chapter, resumably.
  `longform.py audio-assemble` joins with gaps, loudness-normalises, embeds
  chapter marks, tags and cover, and writes `dist/audiobook.m4b` and
  `dist/audiobook.json`. The seam pass reruns on the scripts (`seams --layer script`).

## Pre-editing

The review stage sits between drafting and publication and is skippable by
explicit user choice.

Worth stating clearly: skipping it is a decision about what the book is for. A
personal reference printed for one shelf and a book sold to strangers do not
warrant the same scrutiny, and the pattern does not pretend otherwise. What it
does insist on is that the choice was made rather than defaulted into.

The mechanical gates are **not** skippable. `longform.py lint --strict` runs before
production regardless, because an unresolved `[VERIFY:]` marker in a printed
reference work is not a style question.
