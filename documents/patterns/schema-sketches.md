---
authors: [kamil_letki]
---

# Schema Sketches

## Problem
Between the plan and the code sits a decision nobody reviews: what the binding surface will be. Classes, interfaces, method signatures, tables, views — the shape everything else binds to.

A prose plan hides it. "Share the logic between both callers" reads as one reasonable line. As structure it is a new class, a new registration, and a seam every later change has to respect.

The agent picks that shape by itself, competently and by default. You first see it as a diff.

## Pattern
Have the agent propose the binding surface as a text sketch, and review it before any code exists.

1. Give the agent the requirements and the plan
2. Ask for the resulting binding surface as an ASCII tree — names and relations only, one screen, no method bodies
3. Scan it for three things:
   - structure no requirement asked for
   - a class or method with exactly one caller
   - pieces the requirements imply that are missing
4. Correct the sketch in prose, then let it implement

The sketch is explicit where the plan was abstract, and visual where the plan was narrative. An extra layer is something you see, not something you infer.

Iterating here costs a few hundred tokens. Iterating after implementation costs the unneeded code, the repairs, and every later change routed through a seam that should not exist.

The sketch does not guarantee a better decision. It makes the decision visible and nearly free to reverse, at the one moment when reversing is nearly free.

## Example
Adding a maintenance endpoint that replays booking events. The agent's sketch:

```
HostedServices/BookingEventsHandler     Services/BookingEventProcessor   <- new
  +- intake logic (unchanged)             +- HandleEvent()
                                          +- HandleBookingVersionAsync()
Controllers/MaintenanceController         +- HandleSupplierUpdateAsync()
  +- POST /sync -> processor              +- HandleStatusUpdateAsync()
```

One new class, wrapping methods the handler already had, so the controller and the message handler could share them.

The review comment, in full:

```
revert BookingEventProcessor - it was introduced by changes
```

Collapsing it back cost three `public` keywords. Building it would have cost ~200 lines, still coupled to the handler it came from.

Two callers, extract a service — a principle correctly applied. Whether that seam earns its keep is a system-level call, and the sketch is where you get to make it.

**A rule correctly followed can still be wrong.** A GraphQL endpoint exposing booking versions, with fields extracted from raw JSON. The agent's sketch: a `BookingVersionField` enum whitelisting extractable fields, a `BookingVersionProjection` staging class, one DTO field per enum member — strongly typed DTOs, exactly as the project's ground rules demand.

The review, one question:

```
What's the need for separate BookingVersionProjection class?
```

Revised sketch: enum and staging class deleted. Callers pass JSON paths — `extract: ["services[0].car.package.code"]` — the service resolves each path server-side, and results land in one `extracted: Dictionary<string,string?>`. Flexibility was this endpoint's whole point; every new field would have grown the enum. No rule you write into the agent's instructions can make that call, because in most other places the rule is right.

Recurring shapes the sketch catches:
- duplicate persisted state → one source of truth
- a parallel compatibility layer → extend the existing live types
- many fixed schema members → one dynamic map with explicit extraction
