---
authors: [kamil_letki]
---

# Dialectic Review

## Problem
Some artifacts are expensive to be wrong about: an API contract, a schema, a plan, a document others will build on. A single review pass gives them a soft gate. Ask an agent to review critically and trained agreeableness pulls the verdict toward approval-with-nits; tell it to attack and every objection arrives pressed with equal confidence, the first plausible one hardest — and nobody in the room is making the case for the artifact.

This is Feedback Flip escalated — the flip refocuses one agent from producing to critiquing, and stays the right tool for routine diffs; when the stakes justify it, the escalation puts the evaluation itself on trial.

## Pattern
Try the artifact in court.

1. Spawn two subagents with opposed mandates and identical grounding: the artifact files, the project's acceptance standards, and the nearest-neighbor artifacts — that is where overlap hides. The advocate must build the strongest honest case *for*; the prosecutor must attack. Each files a structured opening: numbered counts with evidence and a verdict, not prose.
2. Rebuttal round: each side receives the other's opening.
3. The main session judges. It holds the full working context the subagents never saw, rules per count, and may overrule both sides — a context-free judge demotes the whole thing to vote-counting.

```
ADVOCATE: Build the strongest honest case FOR <artifact>.
Ground every claim in <files>, <acceptance standards>,
<nearest-neighbor artifacts>. Structured opening: numbered
counts, evidence per count, verdict.

PROSECUTOR: Attack <artifact>. Same grounding, same
structure, severity per count.

REBUTTAL (each side gets the other's opening):
"Concede any count you cannot honestly beat — a judge
rewards precision over stubbornness."
  prosecutor variant: "If a defense actually holds, say so;
  drop counts the defense genuinely answers."
```

Each piece is load-bearing. A neutral reviewer softens; a mandate does not — and the advocate is no decoration, it forces the strongest case for the artifact to exist so the judge is not synthesizing from an attack and a shrug. The concession instruction is the anti-theater mechanism: openings are just claim lists, and without permission to fold, two agents will perform disagreement forever. A withdrawn count is the strongest evidence the judge gets. The shape is borrowed from debate-based oversight ([Irving, Christiano, Amodei, "AI Safety via Debate"](https://arxiv.org/abs/1805.00899)): the wager is that judging a debate between opposed experts is easier than producing the review yourself.

A run is not cheap — the one documented below spent about 200k tokens on two openings, two rebuttals, and a judgment, and the bill moves with the artifact, the models, and the reasoning effort. Spend it where being wrong is expensive; a routine diff does not need a trial. Past the baseline, two gauges escalate further: rounds — more rebuttals, or unbounded until neither side moves a count — and participants — extra prosecutors with narrow mandates, one on performance, one on security. The rebuttal exchange works best in a harness where subagents can address each other directly, such as [Claude Code agent teams](https://code.claude.com/docs/en/agent-teams) and its `SendMessage` tool, at a still higher token cost; elsewhere the main session relays each filing.

## Example
The pattern's first documented run reviewed this collection's own Schema Sketches / Structural Blindness contribution, pre-merge. Advocate and prosecutor each read the two documents, CONTRIBUTE.md, eight neighboring documents, and the relationships graph, then filed structured openings — GAP/EVIDENCE/FRAMING/CONCESSIONS/VERDICT for the defense, REDUNDANCY/EVIDENCE/FORM/GRAPH/VERDICT with per-count severity for the prosecution.

The rebuttal round did the sorting. The prosecutor withdrew its lead count — "I withdraw the claim that Schema Sketches should fold into Check Alignment" — and noted the defense had "answered a charge I never filed". The advocate conceded three of the obstacle's four citations: METR and Abbassi cut as borrowed authority, a GitClear gloss hedged; only Watanabe (MSR 2026) held.

The judge ruled per count and overruled both sides twice: it kept a coined name the prosecution wanted gone, and retyped a graph edge to `related` instead of dropping it.

The net catch: three of four citations supported claims adjacent to the ones they were attached to — a defect the authoring session had missed across five drafting rounds. The amended contribution merged.
