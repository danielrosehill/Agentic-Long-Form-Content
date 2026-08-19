# Why one agent cannot write a book

The premise the whole architecture rests on. If it is wrong, the architecture is
unnecessary overhead.

## It is not a capacity problem

The obvious framing is that a book does not fit in a context window. That framing
is increasingly false and it was never the real constraint.

The real constraint is that **output quality degrades as a function of how much
the model is holding, well before the window is full.** A model asked to write
chapter nine while carrying chapters one through eight is not simply
better-informed. It is a model whose attention is spread across eighty thousand
tokens of its own prior output, and prior output is the worst possible thing to
have in context, because it is the thing the model is most likely to imitate.

## What degradation looks like in a manuscript

Recognisable, and recognisably different from ordinary bad writing:

- **Restatement.** Chapter nine re-explains a term chapter three defined, usually
  with slightly different emphasis, so the book now defines it twice and
  inconsistently.
- **Register drift.** The voice at chapter twelve is not the voice at chapter
  two. Nothing in the middle is wrong; the ends do not match.
- **Structural echo.** Every chapter starts the same way, because the model is
  imitating the chapter openings in its own context.
- **Locally fluent, globally empty.** Paragraphs that read well and advance
  nothing. This is the hardest to catch, because it survives proofreading.
- **Argument drift.** The thesis at the end is not the thesis at the start, and
  no single sentence is where it changed.

These are failures of *long-range coherence*, and they are exactly the failures a
single-agent approach cannot solve by trying harder.

## The mitigation

Never let any agent reach that region. Every writing task is small: one chapter,
one brief, one style guide, a handful of research notes. An agent working on
three thousand words behaves like an agent working on three thousand words,
regardless of how long the book is.

## The cost, and it is a real one

An agent that has not read the rest of the book does not know what the rest of
the book says. Removing shared context removes the mechanism by which
consistency would otherwise happen for free.

So consistency has to be manufactured. It is carried by artefacts small enough to
hand to every agent cheaply:

| Artefact | Replaces |
| --- | --- |
| Style guide | Having read the other chapters' prose |
| Chapter brief | Having read the neighbouring chapters |
| Continuity ledger | Remembering what has already been established |
| Boundaries in the outline | Noticing you are covering someone else's material |
| Manifest | Knowing where the chapter sits |

**This is the trade at the centre of the pattern: shared documents instead of
shared context.** Every rule in the spec is an instance of it, and every failure
mode in [`failure-modes.md`](failure-modes.md) is one of these artefacts being
too thin to carry the load it took on.

## Why this favours reference books

The pattern works better for reference and instructional material than it would
for narrative, and the reason is structural rather than aesthetic: a reference
book's chapters are genuinely separable. Each answers a question. The dependency
between chapter four and chapter eleven is a handful of established terms, which a
ledger can carry.

Narrative dependency is not like that — it is continuous, and it does not
compress into a ledger. A pattern built on independent chapters is the wrong
pattern for a book whose chapters are not independent.
