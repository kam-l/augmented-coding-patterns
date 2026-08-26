---
authors: [kamil_letki]
---

# Dialectic Review

## Problem
Some artifacts are expensive to be wrong about: an API contract, a schema, a plan, a document others will build on. A single review pass is a soft gate: ask an agent to review critically and trained agreeableness pulls toward approval-with-nits; tell it to attack and it latches onto its first plausible objection, pressing every count with equal confidence — and nobody makes the case for the artifact.

This is Feedback Flip escalated — the flip refocuses one agent from producing to critiquing, and stays the right tool for routine diffs; here the evaluation itself goes on trial.

## Pattern
Try the artifact in court.

1. Spawn two subagents with opposed mandates and identical grounding — the artifact files, the acceptance standards, the nearest-neighbor artifacts, where overlap hides. The advocate builds the strongest honest case *for*; the prosecutor attacks. Each files a structured opening: numbered counts, evidence, verdict.
2. Rebuttal round: each side receives the other's opening.
3. The main session judges. It holds the working context the subagents never saw, rules per count, and may overrule both sides — a context-free judge demotes the trial to vote-counting.

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

A neutral reviewer softens; a mandate does not. The advocate is not decoration: it forces the strongest case for the artifact to exist, so the judge does not synthesize from an attack and a shrug. The concession instruction is the anti-theater mechanism: without leave to fold, opposed agents keep performing disagreement; a withdrawn count is the strongest evidence the judge gets. The shape is debate-based oversight ([Irving, Christiano, Amodei, "AI Safety via Debate"](https://arxiv.org/abs/1805.00899)): judging a debate between opposed experts is easier than producing the review yourself.

A run is not cheap — the one below spent about 200k tokens, and the bill moves with the artifact, the models, and the reasoning effort. Spend it where being wrong is expensive. The escalation gauges: rounds — unbounded, until neither side moves a count — and participants — extra prosecutors with narrow mandates: performance, security. Subagents that can address each other directly, as in [Claude Code agent teams](https://code.claude.com/docs/en/agent-teams) with `SendMessage`, run the exchange best; elsewhere the main session relays each filing.

## Example
The first documented run reviewed this collection's own Schema Sketches / Structural Blindness contribution, pre-merge. Grounding: the two documents, CONTRIBUTE.md, eight neighbors, the relationships graph. The openings: GAP/EVIDENCE/FRAMING/CONCESSIONS/VERDICT from the defense; REDUNDANCY/EVIDENCE/FORM/GRAPH/VERDICT, with per-count severity, from the prosecution.

The rebuttal round did the sorting. The prosecutor withdrew its lead count — "I withdraw the claim that Schema Sketches should fold into Check Alignment" — and noted the defense had "answered a charge I never filed". The advocate conceded three of four citations in the obstacle: METR and Abbassi cut as borrowed authority, a GitClear gloss hedged; only Watanabe (MSR 2026) held.

The judge ruled per count and overruled both sides twice: kept a coined name the prosecution wanted gone; retyped a graph edge to `related` instead of dropping it.

The net catch: three of four citations supported claims adjacent to the ones they were attached to — a defect the authoring session missed across five drafting rounds. The amended contribution merged.
