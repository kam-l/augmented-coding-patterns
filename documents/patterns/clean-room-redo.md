---
authors: [kamil_letki]
---

# Clean Room Redo

## Problem
The end of a long session is when you know the most. The context window holds every correction, lesson, and decision that happened along the way — and the right solution often only became visible during or after implementation, as unknown unknowns surfaced. But the work still carries the scars of the discovery path: abandoned approaches, leftover abstractions, patches on patches. Close the session and that battle-won hindsight evaporates with it.

## Pattern
Before ending the session, ask:

**"Knowing everything we know now, what could have been done better from the start?"**

Then act on the answer while the context is still loaded:

- **Redo the work**: refactor or rebuild toward what you would have built knowing the end state — delete abstractions that only existed to serve dead ends
- **Redo the plan**: restructure specs or task boundaries before the next phase inherits the mess
- **Redo the setup**: turn hindsight into new instructions or guardrails for the agent, so the next session starts where this one ended

Make the redo verifiable: give the agent an explicit checklist of what must be gone and what must be preserved, and have it verify the result against that list.

## Example
A long session on a Quartz-based workflow scheduler accreted its discovery path: four migrations and a custom replay-tracking layer (replay cursors, occurrence intents, lease and completion columns) — all built before it became clear that Quartz's native misfire handling covered the need.

The end-of-session clean-room prompt:

> Perform a clean-room verification... Simplify to Quartz native misfire handling only; remove replay/cursor/intent/lease tracking artifacts.

with an explicit checklist: no `ScheduleReplayCursors` schema, no `ScheduleReplayOccurrenceIntents`, no `DispatchCompletedAtUtc` or `DispatchLeaseUntilUtc` columns; preserve Quartz `qrtz_*` persistence and `FailedScheduleWorkflow` re-dispatch; consolidate the branch's migrations.

The redo deleted the four migrations, `QuartzDispatchFailureStore`, and the `SchedulingDispatchFailure` entity, replacing them with one consolidated migration ending in idempotent cleanup:

```sql
DROP TABLE IF EXISTS "Workflows"."ScheduleReplayOccurrenceIntents";
DROP TABLE IF EXISTS "Workflows"."ScheduleReplayCursors";
```

The branch ended with a smaller persistence model, a single-migration chain, and 77 scheduling tests passing — a cleanup no fresh session could have specified without re-learning everything this one already knew.
