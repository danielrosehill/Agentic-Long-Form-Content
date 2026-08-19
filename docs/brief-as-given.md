# Brief, as given

> The originating description of the pattern, recorded before it was structured
> into [`SPEC.md`](../SPEC.md).
>
> **Provenance:** spoken aloud and dictated on 2026-08-19. Reproduced with
> disfluencies and repeated false starts removed and nothing else changed — no
> content added, no reasoning supplied, no terms substituted. Where the speaker
> corrected themselves mid-sentence, the correction is kept and the abandoned
> phrasing dropped.
>
> This file exists because the pattern says to keep it. The first statement of
> what something should be is evidence, and it is unrecoverable once paraphrased
> into a specification. Keeping it also makes the spec auditable: anything in
> `SPEC.md` that cannot be traced back to here is an addition, and should be
> visible as one.

---

This is basically the pattern that I would like to create — the pattern that
I've used. I want to make the use case clear, because the reason I'm sharing
this is that I've created these using print-on-demand or POD services like Lulu
or even KDP Create: educational, instructional, reference material that didn't
exist before. That's the commonality.

I think we might want to think about the right name for that, so that it's not
— it's not a system for creating sort of spam fiction works on KDP. This is
actually an agentic research pattern that happens to use the published paperback,
or unpublished paperback, whatever the case may be, as the target form factor.

The pattern I've figured out is basically a modularised pattern. The challenge in
book writing from an AI agent is essentially one of context management. You can't
get one AI tool to write an entire book. You might be able to, but you'd almost
certainly run into serious issues with degradation in the middle of the context
window.

So I've come up with an approach — a series of engineering decisions to try to
mitigate that. The first one is to create a spec for the book. Then create a
style guide for agents to reference, so that the tone of voice is consistent
throughout. Then create an outline, which doesn't get into the book; it's used
purely for guiding the AI agents, or a human who's co-authoring. This contains
clear chapter briefs. Then subagents actually write the chapters. Then
specialised subagents review the chapters from a proofreading standpoint, and
ensuring a consistent tone of voice.

The best way to diagram this, I think — and we should definitely do it — is to
have agent teams. So you have a writing team, a review team, and then a
publication team. The publication team is involved in the manuscript's
concatenation, generation using Typst, preparing it according to different specs,
typesetting it to the user's desired typesetting profile — KDP or other places —
to ensure conformity.

Maybe a graphic design team as well: subagents generating graphics, a graphics
style guide, so the book has a consistent visual language, and then subagents
actually generating the individual illustrations and diagrams for technical
books.

And then the process for that: firstly, the user defines why they want to create
this, what the book's going to be about, the approximate target length, and all
the material.

I think I forgot the research team — or research subagent. I think it's more like
a research subagent, an individual, so that it can work with the actual
chapter-writing subagents.

So the overall process is: the user defines the project. We then create the
foundational source-of-truth documents, saved in the repo. Then we delegate to
subagents for the actual authorship, in parallel, chapters. And then we have a
publication team that's responsible for the concatenation. And we can have a kind
of pre-editing step if required, if desired — I think that one can be skipped in
certain cases, if the user is comfortable with it.

But the whole architecture comes down really to carefully planned delegation, so
that no single agent has a giant context task to work with.

The skills we can ship include a user interview: the user defines the spec, and
maybe a subagent asks questions to fill in the blanks that it thinks the
orchestrator — the actual book-writing team — is going to need to do an effective
job, where those aren't clear in the spec. And maybe one agent also in the intake
stage whose task is just to intake the brief from the user without offering to —
without asking questions — just saving that into the repo. So you've got that
stage as well, a kind of project definition stage, which is its own agentic team
in the framework, properly.

---

## What the spec added

Recorded so the additions are visible as additions rather than passing as part of
the original brief.

- **The name.** "Monograph" was tried and rejected as too obscure and likely to
  attract the wrong argument; the scope statement carries the distinction
  instead of the name.
- **The corollary in §2.1** — that removing shared context forces consistency
  onto shared documents — is a restatement of the brief's logic, not a new claim,
  but the brief does not put it that way.
- **The parallelism limit** (chapters sharing a source must not draft
  concurrently) is an inference from the stated architecture, not something the
  brief says.
- **The evidence rule** — mark an unreachable fact rather than write around it —
  is not in the brief. It comes from the stated use case: reference material is
  read for its facts.
- **The print-geometry handoff boundary** is an implementation decision made
  because a dedicated plugin already owns it.
- **The invariants and open questions** in §7 and §8 are analysis.
