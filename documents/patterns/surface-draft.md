---
authors: [kamil_letki]
---

# Surface Draft

## Problem
An approved sketch is still prose. Corrections to it travel through chat — "that method should also take the tenant" — instructions that point at code which does not exist yet, connected to their target only by the agent's interpretation. And nothing can bind to a sketch: no test compiles against an ASCII tree, so verification waits for the full implementation.

## Pattern
Zoom in one level from Schema Sketches: have the agent write the approved sketch into the codebase as the real public-facing structure — classes, interfaces, method signatures, endpoints — with every body stubbed: pseudocode, a comment, `throw new NotImplementedException()` in .NET, or the equivalent in your language.

The draft is not implementation. It is the binding surface made concrete, and that buys two things:

- **Corrections become code annotations, not chat.** Write each instruction as an Agent Annotation — a bare `AGENT: ...` one-liner on the code it corrects, anchored to the exact code area and a build error until honored.
- **Tests wire up before implementation.** The structure compiles, so tests can be written and run against it now — Approved Scenarios in concrete form, or the RED phase of TDD.

Then let the agent fill the bodies. Names, shapes, callers and failing tests are already fixed; implementation becomes the constrained step. The constraint is the point — it buys autonomy: structure-level mistakes and over-engineering are pre-empted before generation, and the wired scenarios stand as the human-approved oracle, so the agent can be left to run with less supervision.

## Example
Task: a scheduled-reports feature in a .NET service. The drafted surface was reviewed in the files themselves, and corrections were written as `AGENT:` annotations directly on the code they correct:

```csharp
public long? RecipientId { get; init; }
AGENT: ALL IDS MUST BE STRING/GUID ALWAYS, NEVER LONG/INT
```

```csharp
AGENT: split this file - take non-trivial records into their separate files. This file should contain only main flow methods.
```

```csharp
AGENT: this whole file is bloat and redundancy, it should be a simple local method creating name per format.
```

No instruction names its target by description — its position is its address. The agent converted the annotations into a plan before touching code:

```
Planned implementation (strict, minimal scope):
1) Apply all AGENT directives in scheduled-reports files
   (split/move/collapse/simplify, remove persisted logs)
2) Propagate the refactor across Application/Domain/Persistence/Service wiring
3) Update scheduled-report specs only where code behavior changed
4) Run build + scheduled-reports-focused tests
```

Then it edited the drafted surface instead of speculating a new architecture: `RecipientId` became `string?`, the non-trivial records moved into their own files, the execution service kept only main-flow methods, the filename helper collapsed toward a local method — and the `AGENT:` lines were removed as each was honored.

One caveat from this session: the build and application tests passed, but some scheduled-report integration expectations still needed follow-up after the strict refactor. The draft constrains structure; it does not make the last mile free.
