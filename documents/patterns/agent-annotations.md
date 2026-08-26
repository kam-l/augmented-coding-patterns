---
authors: [kamil_letki]
---

# Agent Annotations

## Problem
Corrections travel through chat, detached from their target: you describe the place — "the helper in the execution service, the second overload" — and the agent resolves the description by interpretation. Writing the instruction as a code comment anchors it better, but a comment can be skimmed past like any other comment, and nothing forces it to be honored or cleaned up.

## Pattern
Write the instruction into the code as a bare `AGENT: ...` one-liner at the exact position it applies — deliberately not a comment.

- The position is the address. Nothing to describe, nothing to misresolve.
- The bare line is a compilation error. The build stays red until every annotation is honored and removed — attention enforced by mechanism, not by asking. The error list is the list of open corrections.

Annotate everything you find in one editor pass, then send one generic prompt: "apply all AGENT directives". The prompt carries no locations and no details; the code does.

Works at any stage: on a Surface Draft before implementation, or on finished code during review.

## Example
Used for code review after implementation: read the changes in the editor, drop an `AGENT:` line at each spot that needs work, then one prompt applies them all.

To make the marker one keystroke, a VS Code keybinding injects it at the cursor — `alt+a` in `keybindings.json`:

```json
{
  "key": "alt+a",
  "command": "type",
  "args": { "text": "AGENT: " },
  "when": "editorTextFocus"
}
```
