# The foundational documents

Created before any prose, saved in the project repository, and **read** by agents
rather than inferred by them.

Each has exactly one owner. A second document describing structure, voice or
scope is drift, and the resolution is always to fold it back and delete it —
never to keep both and remember which one is current.

| Document | Path | Owns | Ships? |
| --- | --- | --- | --- |
| Brief as given | `spec/brief.md` | The original framing, unedited | No |
| Interview | `spec/interview.md` | The gaps, filled | No |
| Spec | `spec/spec.md` | What it is, who for, scope, length | No |
| Style guide | `guides/style-guide.md` | Voice, register, prose conventions | No |
| Graphics style guide | `guides/graphics-style-guide.md` | Visual language | No |
| Outline | `outline/outline.md` | Arc, chapter map, boundaries, order | **No** |
| Unit (chapter) briefs | `outline/briefs/<id>.md` | One chapter's contract | No |
| Continuity ledger | `continuity.md` | What has been established, and where | No |
| Manifest | `longform.toml` | Structure, status, continuity keys | No |

The manifest was `book.toml` before v2; old workspaces still load. Nothing on
this list appears in the finished work. They are production apparatus.

## The outline never ships

Stated explicitly because the drift is natural and common: an outline becomes a
preface, then a "how to read this book" chapter, then chapter zero.

It exists purely to guide the writing agents and any human co-author. A reader
does not want the plan; a reader wants the book. The one legitimate descendant of
an outline in a finished reference work is a table of contents, which is
generated from the manifest.

## The chapter brief is the load-bearing document

A brief is what a writer gets **instead of** the rest of the book. Everything that
would otherwise come from reading adjacent chapters has to be in it:

| Section | Replaces |
| --- | --- |
| Argument | Knowing what this chapter is for |
| What the reader can do afterwards | Knowing where it lands |
| Assumed by this point | Having read the earlier chapters |
| Establishes | Knowing what later chapters will lean on |
| Must cover | The plan |
| **Must not cover** | Noticing you are writing a neighbour's material |
| Sources | Having done the research |
| Neighbour summaries | Having read the chapters either side |
| Seams ("Picks up from" / "Hands off to") | Knowing what state the join starts and leaves in |

"Must not cover" is the section people leave out and the one that prevents the
overlap which shows up as repetition in the assembled manuscript.

If briefs are thin, writers compensate by reading — and the architecture collapses
back into one large context, quietly, without anything failing.

## The continuity ledger

The book's memory: every term, claim, running example and promise it has
established, with the chapter that established it.

It is the mechanism by which chapter twelve knows what chapter three said without
chapter twelve's writer having read chapter three. Four kinds of entry earn their
place:

- **Terms** — so nothing is defined twice, differently
- **Claims** — positions taken, which later chapters may build on but not re-argue
- **Running examples** — with their details fixed, so two chapters do not give the
  same example different numbers
- **Promises** — "we return to this in chapter nine" needs a chapter nine that
  does, or the promise gets cut

For screenplays the ledger also carries **Characters**, **Locations** and
**Timeline** tables.

A fifth section records **deliberate** repetition, because a reference work
sometimes should restate itself for readers who open it in the middle. Recording
those decisions is what stops the continuity pass from "fixing" them.

The manifest's `establishes` / `requires` keys are the machine-checkable shadow of
this document: a chapter may only require what an earlier chapter establishes, and
the linter enforces the ordering.

## Why the brief is kept unedited

The first statement of what someone wants is evidence. Once paraphrased into a
spec, the original framing — what was emphasised, what was assumed, what was left
out — is unrecoverable.

It also makes the spec auditable. Anything in the spec that cannot be traced back
to the brief is an addition, and additions should be visible as additions rather
than passing as the user's own requirements.
