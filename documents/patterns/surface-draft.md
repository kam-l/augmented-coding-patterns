---
authors: [kamil_letki]
---

# Surface Draft

## Problem
An approved sketch is still prose. Corrections to it travel through chat — "that method should also take the tenant" — instructions that point at code which does not exist yet, connected to their target only by the agent's interpretation. And nothing can bind to a sketch: no test compiles against an ASCII tree, so verification waits for the full implementation.

## Pattern
Zoom in one level from Schema Sketches: have the agent write the approved sketch into the codebase as the real public-facing structure — classes, interfaces, method signatures, endpoints — with every body stubbed: pseudocode, a comment, `throw new NotImplementedException()` in .NET, or the equivalent in your language.

The draft is not implementation. It is the binding surface made concrete, and that buys two things:

- **Corrections become code annotations, not chat.** Write the instruction as a comment on the method it corrects. The instruction is anchored to a concrete code area — no describing where it applies, no agent guessing which part you meant.
- **Tests wire up before implementation.** The structure compiles, so tests can be written and run against it now — Approved Scenarios in concrete form, or the RED phase of TDD.

Then let the agent fill the bodies. Names, shapes, callers and failing tests are already fixed; implementation becomes the constrained step.

## Example
