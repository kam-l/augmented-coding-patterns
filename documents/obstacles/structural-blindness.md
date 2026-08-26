---
authors: [kamil_letki]
synonyms: [Over-Abstraction, Speculative Generality]
---

# Structural Blindness

## Description
The agent judges code by how well it is written, not by whether the system needs it to exist.

It applies real principles competently in the small — separation of concerns, DRY, single responsibility — without weighing them against the structure that is already there. The result is structure that gets added rather than fitted: a new layer where a method would do, a duplicated block where a call would do.

This failure has been observed under measurement. Asked to predict which agent-generated methods reviewers would go on to delete, GPT-4o defended one by praising its type hints, side-effect avoidance and naming — and it was deleted anyway. The authors' conclusion: LLMs "conflate local code quality with system-level necessity" ([Watanabe et al., MSR 2026](https://dl.acm.org/doi/10.1145/3793302.3793591)). Ecosystem-scale data points the same way: since AI assistance became standard, refactored lines fell from 24.1% to 9.5% while duplicated blocks rose eightfold ([GitClear, 2025](https://www.gitclear.com/ai_assistant_code_quality_2025_research)) — suggestive at that scale, not attribution.

## Impact
- Structure nobody asked for becomes structure everybody maintains
- Every unneeded layer is more complexity for the next prompt to work through
- The code reads well locally, so review waves it through
- A duplicated block and a needless service layer look like opposite mistakes, but both are structure added instead of fitted
