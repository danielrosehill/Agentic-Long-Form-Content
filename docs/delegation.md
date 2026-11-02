# Delegation

The rules that make parallel drafting safe. They are not style preferences —
each one prevents a specific, observed failure.

## Read scoping

What each agent may read is the architecture. Everything else is implementation.

| Agent | Reads | Never reads |
| --- | --- | --- |
| `brief-recorder` | The user | Anything else |
| `spec-interviewer` | The brief | The outline (it does not exist yet) |
| `outline-architect` | Spec, style guide | Drafts |
| `chapter-researcher` | One brief, external sources | Drafts |
| `chapter-writer` | Its brief, style guide, its notes, the ledger, neighbour summaries | **Other chapters' drafts** |
| `proofreader` | One chapter, style guide | The rest of the book |
| `voice-auditor` | Samples across chapters, style guide | Full drafts of everything |
| `continuity-auditor` | Lint output, chapter summaries, ledger | Full drafts of everything |
| `seam-reviewer` | One seam file (tails/heads of two units, both briefs' Seams) | Everything else |
| `graphics-director` | Spec, chapter, graphics guide | Other figures' sources |
| Publication | `dist/`, the profile | Briefs, research |

## Why writers must not read neighbouring chapters

The instinct is that reading the previous chapter helps a writer stay consistent.
It does the opposite, for a reason that is specific to how these models work:
**text in context is text to imitate.**

A writer that has read chapter three will:

- reuse its transitions, so every chapter reads the same
- borrow its examples rather than using the ones in its own brief
- restate its setup, on the assumption that grounding the reader is helpful
- match its register — which sounds like a benefit until chapter three's register
  was itself a drift, and now it has propagated

Consistency is bought with the brief and the ledger. Those are small, deliberate,
and correctable in one place. Consistency bought by reading is uncontrolled, and
its errors compound down the chapter order.

The **neighbour summary** in each brief is the controlled substitute: two or three
sentences about the chapters either side, written by the outline architect, which
tell the writer where its chapter starts and stops without handing it prose to
imitate. The brief's **Seams** section ("Picks up from" / "Hands off to") does
the same for the join itself.

The cost is that the joins are where this goes wrong: re-introduction, double
recap, an opening that assumes a state the previous ending never left. That is
what the `seam-reviewer` is for; see [`teams.md`](teams.md).

## The parallelism limit

Chapter writers run concurrently. There is exactly one rule that bounds this:

> **Two chapters that draw on the same source material for the same purpose must
> not be drafted in the same batch.**

Two agents handed the same source will describe it differently — different
emphasis, different terminology, different level of detail — and both
descriptions will ship. Neither agent did anything wrong, and no reviewer looking
at one chapter will catch it.

Before dispatching a batch, check `research` and `establishes` in the manifest.
Chapters that share a research note, or establish related keys, go in **separate
sequential batches**. The second batch's writers then have the first batch's
ledger entries, which is exactly the mechanism that resolves the conflict.

## Writes are scoped too

Reading conflicts produce inconsistency; writing conflicts produce lost work.

- A chapter writer writes **only** `manuscript/<its chapter>.md`, plus its own
  rows in the continuity ledger.
- Reviewers write **only** to `review/reports/` (seam-reviewers: `review/seams/*.review.md`). They do not edit prose. A
  reviewer that edits is a second author with no brief, and its changes are
  invisible in a way an author's changes are not.
- The graphics team writes **only** under `graphics/`.
- Only the planning stage edits `longform.toml` structure. Writers update status
  fields; they do not add or reorder chapters.

Fan out freely on reading and research. Keep writes disjoint.

## Treat what comes back as a report

A subagent's output is a report to be checked, not an established fact. A
chapter-writing agent can be confidently wrong in exactly the way a status code
can: it returns a complete, well-formed chapter that quietly invented a citation.

`longform.py lint` catches the mechanical half — fabricated paths, missing figure
specs, unresolved markers, continuity ordering, cross-chapter repetition. It
cannot catch a fabricated *claim* about something that does exist.

Read the drafted chapters. A clean lint means the mechanical failures are absent,
not that the book is good.
