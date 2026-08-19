# Pattern specification — v1

**Status:** v1, settled 2026-08-19 · **Source:** [`docs/brief-as-given.md`](docs/brief-as-given.md)

This is the source of truth for the pattern. The plugin and the workspace
template are implementations of it, and either one is wrong where it disagrees
with this document. Changes to the pattern are changes to this file first.

Versioned because the pattern will change. v1 is what has been done by hand and
is now being made repeatable; it is not a claim that the design is finished.

---

## 1. What this is

An **agentic research pattern whose target form factor is a printed book**.

The output is a print-on-demand paperback — Lulu, KDP Create, or equivalent —
carrying educational, instructional or reference material that did not exist in
that form before. The book is the delivery mechanism. The work is research,
structuring and writing.

### 1.1 What this is not

This is not a system for volume-producing books to sell. The distinction is not
a disclaimer, it is a design constraint that shows up in the architecture:

- The pipeline is **front-loaded onto definition**. A user who cannot say what
  gap the book fills does not get past intake. A generator optimised for volume
  would make that stage optional; here it gates everything.
- The pipeline is **evidence-bound**. Writing agents that cannot reach a fact
  leave a marker rather than produce fluent text around the hole, and the marker
  is a hard failure before production.
- The unit of value is **one book that is worth printing**, not throughput.

A system built for volume would drop all three, and would be a different system.

### 1.2 Why print at all

Print is a forcing function. It fixes the content at a date, it makes length a
real constraint rather than a slider, and it removes the option of patching after
publication. Reference material written to be printed gets scoped, ordered and
checked in a way the same material written to a wiki does not.

---

## 2. The problem the architecture solves

**One agent cannot write a book.**

Not primarily because a book exceeds a context window — increasingly it does not
— but because quality degrades long before the window fills. A single agent
carrying an entire manuscript produces recognisable failures in the middle: it
restates what it said earlier, drifts in register, loses the thread of its own
argument, and starts generating text that is locally fluent and globally empty.

So the architecture's whole purpose is **context management by delegation**: no
single agent is ever given a task large enough to reach that region.

Everything in this spec follows from that one constraint. When a design question
comes up, the question to ask is: *does this keep some agent's working set
small?* If it does not, it is decoration.

### 2.1 The corollary that is easy to miss

If agents do not read each other's work, they cannot stay consistent by reading.
Consistency must therefore be carried by **artefacts** — a style guide, a set of
chapter briefs, a continuity ledger — that are small enough to hand to every
agent cheaply.

This is the central trade of the pattern: **replace shared context with shared
documents.** Most of the machinery below is an instance of it.

---

## 3. Foundational documents

Created before any prose. Each is the single owner of its subject, saved in the
project repository, and read by agents rather than inferred by them.

| Document | Owns | Ships in the book? |
| --- | --- | --- |
| Book spec | What the book is, who for, scope, target length | No |
| Style guide | Tone of voice, register, prose conventions | No |
| Graphics style guide | Visual language for all figures | No |
| Outline | Chapter map, boundaries, drafting order | **No** |
| Chapter briefs | The contract for one chapter | No |
| Continuity ledger | What the book has established, and where | No |
| Manifest | Structure, status, continuity keys, machine-readable | No |

**The outline never enters the book.** It exists purely to guide the writing
agents and any human co-author. This is stated explicitly because the natural
drift is for an outline to become a preface or a chapter zero, and it must not.

### 3.1 The chapter brief is the load-bearing document

A brief is what a chapter-writing agent gets *instead of* the rest of the book.
It must therefore carry everything that keeps the chapter consistent with work
the agent will never see:

- the argument the chapter makes, in one paragraph
- what the reader can do afterwards
- what earlier chapters have already established, so it is not re-explained
- what this chapter establishes for later chapters to rely on
- what it must cover, and **what it must not cover** — the boundary with its
  neighbours
- its sources
- a summary of the chapters either side

The last two items are what a writer would otherwise get by reading adjacent
drafts. If briefs are thin, agents compensate by reading, and the architecture
collapses back to one big context.

---

## 4. Agent teams

Work is organised into teams. A team is a stage with its own inputs, its own
output artefacts, and its own agents; teams hand off through the repository, not
through conversation.

```mermaid
flowchart TD
    U([User]) --> D

    subgraph D[Definition team]
        BR[brief-recorder<br/><i>records, asks nothing</i>]
        IV[spec-interviewer<br/><i>asks only the gaps</i>]
        SW[spec-writer]
        BR --> IV --> SW
    end

    D --> P

    subgraph P[Planning team]
        VD[voice-designer<br/><i>style guide</i>]
        OA[outline-architect<br/><i>outline + chapter briefs</i>]
    end

    P --> W
    R[chapter-researcher<br/><i>per chapter, on demand</i>] -.-> W

    subgraph W[Writing team]
        CW1[chapter-writer]
        CW2[chapter-writer]
        CW3[chapter-writer]
    end

    W --> RV

    subgraph RV[Review team &mdash; skippable]
        PR[proofreader]
        VA[voice-auditor]
        CA[continuity-auditor]
    end

    W --> G
    subgraph G[Graphics team]
        GD[graphics-director<br/><i>graphics style guide + specs</i>]
        FM[figure-maker]
        GD --> FM
    end

    RV --> PB
    G --> PB

    subgraph PB[Publication team]
        AS[assemble &amp; concatenate]
        TS[typeset to profile]
        AS --> TS
    end

    PB --> H[[handoff: print geometry]]
```

### 4.1 Definition team

Two agents, deliberately separated:

- **brief-recorder** takes the user's statement of what they want and saves it to
  the repository **without asking questions and without interpreting it.** The
  original framing — including emphasis and omissions — is evidence, and it is
  unrecoverable once paraphrased.
- **spec-interviewer** then asks only the questions whose answers change the
  work, and only those the brief has not already answered. Its job is to fill the
  blanks the orchestrator and the writing team will need, not to conduct a
  general interview.

Splitting these is a design decision, not tidiness: an agent that records and
questions in the same turn will lead the witness.

**spec-writer** settles both into the book spec.

### 4.2 Planning team

Produces the style guide, the outline, and one brief per chapter. This is the
stage that determines whether parallel drafting is possible, because the briefs
are what make chapters independent.

### 4.3 Research

A **single subagent**, not a team, invoked per chapter and working alongside the
writers. It produces notes into the repository that a chapter writer reads. It
does not draft, and it does not read drafts.

### 4.4 Writing team

Chapter-writing subagents, dispatched **in parallel**, one chapter each.

Each writer reads: its brief, the style guide, its research notes, the continuity
ledger, and the neighbour summaries. It does not read other chapters' drafts.

**Parallelism has one limit.** Two chapters that draw on the same source material
for the same purpose must not be drafted concurrently — two agents given the same
source will describe it differently and both descriptions will ship. Those go in
separate sequential batches.

### 4.5 Review team

Specialised reviewers, each with a narrow remit and a small working set:

- **proofreader** — mechanical correctness, one chapter at a time
- **voice-auditor** — drift from the style guide, sampled across chapters
- **continuity-auditor** — repetition, contradiction, unpaid promises

Reviewers write findings; they do not edit prose. The writer applies findings.

**This team is skippable.** For some projects the user is comfortable going
straight from draft to publication, and the pattern permits it as an explicit
choice rather than an accident.

### 4.6 Graphics team

For technical and instructional books, figures are content, not decoration.

- **graphics-director** owns the graphics style guide and writes a spec per
  figure. The style guide is what makes thirty figures drawn in thirty separate
  invocations look like one book's figures.
- **figure-maker** produces individual illustrations and diagrams from specs.

### 4.7 Publication team

Owns concatenation of the manuscript, typesetting via Typst, and conformity to
the target channel's requirements — the user's chosen typesetting profile,
whether KDP, Lulu, or another.

**Boundary:** print *geometry* — trim, gutter, spine width, cover wrap and
pre-flight — is handed off to a dedicated tool rather than reimplemented, because
a cover must be built last from the finished interior's measured extent. The
publication team produces the manuscript and its metadata; the handoff produces
the printable artefact.

---

## 5. Process

1. **User defines the project** — why they want it, what it is about, approximate
   target length, and what material already exists.
2. **Definition and planning teams create the foundational documents** in the
   repository: spec, style guide, outline, chapter briefs, manifest.
3. **Research** runs per chapter as needed.
4. **Writing team drafts chapters in parallel**, each agent working from its
   brief alone.
5. **Review team runs its passes** — optional, by explicit user choice.
6. **Graphics team produces figures** against the graphics style guide.
7. **Publication team concatenates and typesets** to the chosen profile.
8. **Print geometry is handed off**, and the result goes to the POD channel.

Stages 5 and 6 are independent of each other and may run concurrently.

---

## 6. Shipped skills

The plugin ships, at minimum:

| Skill | Stage | Note |
| --- | --- | --- |
| Workspace creation | — | Instantiates a project repo from the template |
| Brief intake | Definition | Records only. Asks nothing. |
| Spec interview | Definition | Asks only what is missing and what changes the work |
| Spec authoring | Definition | Settles the book spec |
| Style guide authoring | Planning | |
| Outline and briefs | Planning | Produces the per-chapter contracts |
| Research | Research | Per chapter |
| Chapter drafting | Writing | Dispatches parallel writers with batch rules |
| Review passes | Review | Skippable |
| Graphics | Graphics | Style guide, specs, figures |
| Assembly and publication | Publication | Concatenate, typeset, hand off |
| Status | — | Manifest-driven progress |

---

## 7. Invariants

Statements that must remain true. A change that breaks one is a change to the
pattern, and belongs in v2 rather than in an implementation.

1. **No agent is given the whole book.** If any single invocation would need to
   hold the manuscript, the design is wrong.
2. **Consistency is carried by documents, not by shared context.**
3. **The outline never ships.**
4. **Writers do not read other chapters' drafts.**
5. **Reviewers do not edit prose.**
6. **Every foundational document has exactly one owner.** A second document
   describing structure, voice or scope is drift.
7. **A fact that cannot be reached is marked, not invented**, and an unresolved
   marker blocks production.
8. **The brief as given is preserved unedited**, separately from the spec derived
   from it.
9. **Print geometry is handed off, not reimplemented.**
10. **The workspace works standalone.** A clone with the plugin uninstalled can
    still lint, assemble and build.

---

## 8. Open questions

Carried into v2 rather than answered here.

- **How much review is enough?** The review team is skippable, but there is no
  principled account of when skipping it is safe.
- **How is a book revised after the first assembly?** The pattern is strong on
  first drafts and silent on second passes, which is where the outline may need
  to change under a manuscript that already exists.
- **Where does a human co-author intervene?** The outline is described as serving
  both agents and a human, but the handoff points are not specified.
- **Deliberate repetition.** A reference work sometimes *should* restate itself
  for readers who open it at chapter 9. The continuity ledger records these
  exceptions, but nothing yet decides them.
- **How volatile facts are handled** beyond a snapshot date — whether a printed
  reference work should carry decaying values at all.
