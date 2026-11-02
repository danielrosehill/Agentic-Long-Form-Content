# Failure modes

What goes wrong when agents write a long work, which rule prevents it, and how it is
detected. Every row is a failure that produces a **successful-looking result** —
a complete, fluent chapter that is wrong.

## In the manuscript

| Failure | Looks like | Prevented by | Caught by |
| --- | --- | --- | --- |
| The same thing explained twice | Two chapters both define a term | Continuity ledger; `requires`/`establishes` | `lint` cross-chapter repetition |
| Register drift | Chapter 12 does not sound like chapter 2 | Style guide with checkable rules | `voice-auditor` |
| Structural echo | Every chapter opens identically | Writers not reading each other | Reading the assembled draft |
| Seam failure | Next unit re-introduces what the last just said; both sides recap; opening assumes an unreached state; tonal jump; screenplay prop/position/time jump across the cut | Seams section of the brief | `seam-reviewer` (`longform.py seams`); lint warns on unreviewed or stale seams |
| Boundary overlap | Two chapters cover the same ground | "Must not cover" in the brief | `continuity-auditor` |
| Unpaid promise | "We return to this in chapter 9"; chapter 9 does not | Promises section of the ledger | `continuity-auditor` |
| Contradiction | Chapter 8 contradicts chapter 3's position | Claims section of the ledger | `continuity-auditor` |
| Fabricated fact | A confident, specific, invented number | `[VERIFY:]` rule | **Nothing mechanical.** A human reads it |
| Fabricated citation to a real source | Correct path, wrong claim about it | Source register | **Nothing mechanical** |
| Locally fluent, globally empty | Paragraphs that read well and advance nothing | Argument stated in the brief | Reading against the brief |

The last three are the ones to take seriously, because they survive every
automated gate in the system. `lint` verifies that a source is *cited*, never that
it says what the chapter claims.

## In the process

| Failure | Why it happens | Rule |
| --- | --- | --- |
| The outline ends up in the book | It reads like a useful introduction | The outline never ships |
| Two documents describe the structure | One was written when the other was inconvenient to edit | One owner per document; fold and delete |
| A writer reads three chapters "for context" | It feels responsible | Writers read briefs, not drafts |
| Reviewer edits prose directly | It is faster than writing a finding | Reviewers write findings only |
| Two agents draft chapters sharing a source | Both were dispatched in one batch | Separate sequential batches |
| Briefs written thin, "the writer will figure it out" | Planning feels like overhead | The brief replaces the whole book |
| Status says drafted, file is a stub | Status was set optimistically | `lint` requires the file at each status |

## In production

| Failure | Consequence | Rule |
| --- | --- | --- |
| Cover built from an estimated page count | Spine off-centre; discovered on the proof | Cover built last, from the measured interior |
| Trim changed after drafting | Code listings wrap in print | Measure fixes the line cap; re-derive on change |
| Figure distinguished by colour, printed greyscale | The distinction disappears | Graphics style guide constraint, set before drawing |
| Volatile facts printed undated | The book is wrong within a year | `snapshot_date`, and a scope decision about carrying them at all |
| Unresolved `[VERIFY:]` marker printed | Exactly what it looks like | `lint --strict` before production |

## The meta-failure

**A clean lint is not a good book.**

Every mechanical gate in this system checks that a signal is present, not that
the thing behind it is right. A chapter can pass every check while making a
confident claim about a system that does not behave that way.

The gates exist to make the mechanical failures cheap to eliminate, so that
attention is available for the failures only a reader can catch. They are not a
substitute for reading the book, and a pipeline that produces a printed reference
work nobody read end to end has skipped the only step that was never optional.
