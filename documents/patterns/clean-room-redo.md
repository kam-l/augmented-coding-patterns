---
authors: [kamil_letki]
---

# Clean Room Redo

## Problem
The end of a long session is when you know the most. The context window holds every correction, lesson, and decision that happened along the way — and the right solution often only became visible during or after implementation, as unknown unknowns surfaced. But the work still carries the scars of the discovery path: abandoned approaches, leftover abstractions, patches on patches.

Capturing lessons for future sessions preserves the knowledge — but the artifact in front of you still carries the scars, and only this session knows where they are. Close the session and that hindsight evaporates; a fresh session would have to re-earn it before it could act.

## Pattern
Before ending the session, ask:

**"Knowing everything we know now, what could have been done better from the start?"**

Then act on the answer while the context is still loaded:

- **Redo the work**: refactor or rebuild toward what you would have built knowing the end state — delete abstractions that only existed to serve dead ends
- **Redo the plan**: restructure specs or task boundaries before the next phase inherits the mess
- **Redo the setup**: turn hindsight into new instructions or guardrails for the agent, so the next session starts where this one ended

The agent proposes, grounded in what actually happened — a regret that can't point to a specific discovery from the session is noise. You decide which redos are worth doing.

Asking costs one prompt, and the knowledge it harvests exists nowhere else. But a long session is also where context rot lives: if the window is too degraded to trust with the rework itself, capture the answer as a redo brief and execute it in a fresh session seeded with that brief.

"Plan to throw one away" was a planning discipline; this makes it an executable move — throw it away now, while the window still knows why.

## Example
A long session bootstrapped specifications for a virtual-credit-card service by scanning the codebase. At the end, the clean-room question:

> Knowing what we do now, what could have been done better from the start?

The answer surfaced problems invisible at the start: shared concepts were duplicated across 3–4 specs because no spec was designated authoritative; "Card Operations" had become a catch-all for cross-cutting concerns; "Security" mixed auth with operational infrastructure; Vendor/Supplier terminology should have been in the initial prompts.

The redo wasn't advisory — the user chose "Restructure to 9 specs", and the agent split the offenders:

```
card-operations.md          security-authorization.md
├── card-lifecycle.md       ├── security-auth.md
└── cross-cutting-           └── operational-
    infrastructure.md            infrastructure.md
```

Cross-references were updated so each spec owned its subject instead of repeating shared behavior. This topology only became visible after the full pass over the codebase — no starting prompt could have specified it.

In a follow-up session on the same service, the answer pointed at setup instead: the counterfactual surfaced a pipeline reordering that no individual correction had — "vocabulary scanning should run before parallel scans, dead-code detection should be a pre-pass" — which became a reusable scan playbook for the sessions that followed.
