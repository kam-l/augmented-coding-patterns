---
authors: [kamil_letki]
---

# Schema Sketches

## Problem
Between the plan and the code sits a decision nobody reviews: what the binding surface will be. Classes, interfaces, method signatures, tables, views — the shape everything else binds to.

A prose plan hides it. "Share the logic between both callers" reads as one reasonable line. As structure it is a new class, a new registration, and a seam every later change has to respect.

This is not a misunderstanding — Check Alignment catches the plan the agent got wrong. This is the structure a perfectly understood plan still hides. The agent picks that shape by itself, competently and by default. You first see it as a diff.

## Pattern
Have the agent propose the binding surface as a text sketch, and review it before any code exists.

1. Give the agent the requirements and the plan
2. Ask for the resulting binding surface as an ASCII tree — names and relations only, one screen, no method bodies
3. Scan it for three things:
   - structure no requirement asked for
   - a class or method with exactly one caller
   - pieces the requirements imply that are missing
4. Correct the sketch in prose, then let it implement

Recurring finds, and their fixes:
- duplicate persisted state → one source of truth
- a parallel compatibility layer → extend the existing live types
- many fixed schema members → one dynamic map with explicit extraction

The sketch is explicit where the plan was abstract, and visual where the plan was narrative. Iterating here costs a few hundred tokens; iterating after implementation costs the unneeded code, the repairs, and every later change routed through a seam that should not exist. The sketch does not guarantee a better decision — it makes the decision visible and nearly free to reverse, at the one moment when reversing is nearly free.

## Example
Task: expose the version history of orders over GraphQL, with chosen fields unpacked from each version's raw JSON.

The agent's sketch — a fixed field contract:

```
orderVersions(fields: [OrderVersionField!])
├── OrderVersion
│   ├── metadata: ref, dates, status, tenant, updated_at
│   ├── supplier            ┐
│   ├── carrierCode         ├ one typed field per extractable value
│   └── packageCode         ┘
├── OrderVersionField enum
│   └── whitelists the extractable fields, grows with each one
└── OrderVersionProjection
    └── stages raw JSON into the typed fields
```

Strongly typed DTOs, exactly as the project's own ground rules demand. The review, one question:

```
What's the need for separate OrderVersionProjection class?
```

The revised sketch:

```
orderVersions(extract: [String!])
├── OrderVersion
│   ├── metadata: ref, dates, status, tenant, updated_at
│   └── extracted: Dictionary<string, string?>
│       └── jsonb_extract_path_text(raw, ...)   <- resolved server-side
├── OrderVersionField enum
│   └── deleted
└── OrderVersionProjection
    └── deleted
```

One dynamic map instead of a growing enum, a staging class, and a typed field per extractable value:

```
orderVersions(
  extract: ["items[0].shipping.carrier.code", "supplier"]
) {
  ref
  status
  extract
}
```

Explicit strongly typed fields in a DTO are *locally correct* — the ground rules literally ask for them. In the context of the wider structure and requirements they were wrong: flexibility was this endpoint's whole point, and every new field would have grown the enum and its projection. No static rule could mark this endpoint as the exception; everywhere else, the rule is right.
