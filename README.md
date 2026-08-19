# Agentic Book Writing

Writing full-length reference and instructional books with Claude Code, using
teams of subagents and a repository as shared memory.

**[`SPEC.md`](SPEC.md) is the source of truth for the pattern.** This repository
documents it; two others implement it.

## What this is for

Producing a print-on-demand paperback — Lulu, KDP Create, or equivalent — that
carries educational, instructional or reference material which did not exist in
that form before. Manuals, field guides, technical references, documentation of a
system nobody has written down.

It is an agentic *research* pattern. The book is the delivery mechanism; the work
is research, structuring and writing. It is deliberately not built for producing
books at volume, and [§1.1 of the spec](SPEC.md#11-what-this-is-not) explains
which parts of the architecture would have to be removed to make it so.

## The problem it solves

One agent cannot write a book — not because a manuscript will not fit in a
context window, but because quality degrades long before the window fills. A
single agent carrying a whole book restates itself, drifts in register, and
starts producing text that is locally fluent and globally empty.

So the architecture is **context management by delegation**: no agent is ever
given a task large enough to reach that region. The cost of that is that agents
cannot stay consistent by reading each other's work, so consistency is carried by
documents instead — a style guide, per-chapter briefs, a continuity ledger.

That trade is the whole pattern. Everything else follows from it.

## The teams

```mermaid
flowchart LR
    U([User]) --> D[Definition]
    D --> P[Planning]
    P --> W[Writing]
    R[Research] -.-> W
    W --> RV[Review]
    W --> G[Graphics]
    RV --> PB[Publication]
    G --> PB
    PB --> H[[Print geometry handoff]]
```

Detail, including who reads what, is in [`SPEC.md` §4](SPEC.md#4-agent-teams)
and [`docs/teams.md`](docs/teams.md).

## The three repositories

| Repository | What it is |
| --- | --- |
| **Agentic-Book-Writing** (this one) | The spec and the documentation of the pattern |
| [Claude-Book-Writing-Plugin](https://github.com/danielrosehill/Claude-Book-Writing-Plugin) | The Claude Code plugin: agents, skills, commands |
| [Claude-Book-Workspace-Template](https://github.com/danielrosehill/Claude-Book-Workspace-Template) | The scaffold a book project is instantiated from |

Split this way because they run on different clocks. The plugin is replaced
wholesale on `/plugin update`; a book project lives for months and diverges from
the template forever; the spec changes more slowly than either.

## Related

[**KDP-Publishing-Plugin**](https://github.com/danielrosehill/KDP-Publishing-Plugin) —
takes a finished manuscript and produces a print-ready KDP paperback: trim
geometry, a full-wrap cover built last from the interior's measured extent, and
mechanical pre-flight. This pattern hands off to it rather than reimplementing
any of it, and the two are designed to meet at `dist/manuscript.json`.

The division is deliberate and runs both ways: that plugin states it does not
write your book, and this one does not typeset it. Typesetting and authorship
have different failure modes, and tools that bundle them tend to be good at
neither.

## Reading order

| Document | |
| --- | --- |
| [`SPEC.md`](SPEC.md) | The pattern, normative |
| [`docs/brief-as-given.md`](docs/brief-as-given.md) | Where the spec came from, unedited |
| [`docs/context-problem.md`](docs/context-problem.md) | Why one agent cannot do this |
| [`docs/teams.md`](docs/teams.md) | Each team, its inputs and its outputs |
| [`docs/documents.md`](docs/documents.md) | The foundational documents and who owns what |
| [`docs/delegation.md`](docs/delegation.md) | Read-scoping and the parallelism rules |
| [`docs/publication.md`](docs/publication.md) | Concatenation, typesetting, the handoff |
| [`docs/failure-modes.md`](docs/failure-modes.md) | What goes wrong, and which rule prevents it |

## Status

**v1.** The pattern has been used by hand to produce printed books; this is the
work of making it repeatable. [§8 of the spec](SPEC.md#8-open-questions) lists
what v1 does not answer — revision passes after first assembly being the largest.

MIT.
