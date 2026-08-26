---
authors: [kamil_letki]
synonyms: [Over-Abstraction, Speculative Generality]
---

# Structural Blindness

## Description
The agent judges code by how well it is written, not by whether the system needs it to exist.

It applies real principles competently in the small — separation of concerns, DRY, single responsibility — without weighing them against the structure that is already there. The result is structure that gets added rather than fitted: a new layer where a method would do, a duplicated block where a call would do.

- [Watanabe et al., MSR 2026](https://dl.acm.org/doi/10.1145/3793302.3793591) — asked to predict which agent-generated methods reviewers would delete, GPT-4o defended one by praising its type hints, side-effect avoidance and naming. It was deleted. The authors conclude that "LLMs conflate local code quality with system-level necessity."
- [Abbassi et al., ICSME 2025](https://arxiv.org/abs/2503.06327) — a taxonomy of inefficiencies across 492 generated snippets. Maintainability issues such as unnecessary complexity are prevalent, and vary by model.
- [GitClear, 2025](https://www.gitclear.com/ai_assistant_code_quality_2025_research) — across 211M changed lines, refactored code fell from 24.1% to 9.5% while duplicated blocks rose eightfold. The agent adds; it does not consolidate.
- [METR, 2025](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/) — developers were 19% slower with AI while estimating they were 20% faster. You will not catch this by feel.

## Impact
- Structure nobody asked for becomes structure everybody maintains
- Every unneeded layer is more complexity for the next prompt to work through
- The code reads well locally, so review waves it through
- A duplicated block and a needless service layer look like opposite mistakes, but both are structure added instead of fitted
