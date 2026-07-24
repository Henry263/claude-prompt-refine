---
name: prompt-refiner
description: Turns a short, informal user ask into a fully-structured prompt (goal, constraints, do/don't list, output format, edge cases) before it gets used. Use this whenever the user gives a brief or underspecified request and says something like "build me a prompt that...", "I want AI to do X but not Y", "write a prompt for...", "help me prompt for...", or otherwise signals they want a reusable, structured prompt drafted and confirmed rather than the task done directly. Always confirm the drafted prompt with the user before treating it as final.
---

# Prompt Refiner

Turns a 2-3 sentence ask into a tight, structured prompt, confirms it with the user, then either hands it off or runs it — never both draft and execute in the same breath without a checkpoint.

## When this triggers

The user gives a rough, brief description of something they want an AI to do — often followed by constraints tacked on almost as an afterthought ("...and don't do X"). The tell is that they're describing what they want a *prompt* to accomplish, not asking you to do the underlying task yourself right now.

Do NOT trigger this for requests that are already well-specified prompts, or for simple one-off tasks where drafting a reusable prompt would be overkill (e.g. "summarize this paragraph" — just do it).

## Workflow

### 1. Extract intent
From the user's short ask, identify:
- **Core goal** — what the output should actually accomplish
- **Explicit constraints** — anything they said to include or avoid
- **Implicit constraints** — reasonable inferences from context (tone, audience, prior conversation) — but flag these as assumptions, don't silently bake them in
- **Output format** — is one implied (list, code, JSON, email, etc.)? If not, ask or pick a sensible default and say so

If the ask is too thin to do this confidently (e.g., single ambiguous sentence with no context), ask ONE clarifying question rather than guessing. Otherwise, proceed — don't stall on minor ambiguity.

### 2. Draft the structured prompt
Write the refined prompt using this shape (omit sections that don't apply):

```
GOAL: <one or two sentences, unambiguous>

DO:
- <explicit instruction>
- <explicit instruction>

DON'T:
- <explicit constraint>
- <explicit constraint>

FORMAT: <expected output shape/length/style>

EDGE CASES / NOTES: <anything the model should watch for>
```

Keep it as tight as possible — no filler, no restating the obvious. This is a working prompt, not a document.

### 3. Confirm with the user
Show the drafted prompt back verbatim, and briefly flag anything you assumed (e.g., "I assumed you want this in Markdown — let me know if you want plain text instead"). Ask if it's ready to use or needs edits. **Do not proceed to step 4 until the user confirms or edits.**

### 4. Dispatch
Once confirmed:
- If the user wants it run right now in this conversation, just continue with the confirmed prompt as the next turn.
- If the user wants it saved for reuse (e.g., to paste into another tool, or to call via the Anthropic API from their own app), give it back as a clean copy-pasteable block — no extra commentary mixed in.
- If they want it applied against a different model/tool, hand back the final block only; don't assume you can dispatch outbound on their behalf.

## Notes
- One clarifying question max per round — if there's more ambiguity than that, draft your best guess and let the confirmation step catch the rest.
- Never skip the confirmation step, even if the ask seems simple. The whole point of this skill is the checkpoint.
- If the user iterates on the draft ("no, also exclude Y"), fold it in and re-show the full updated prompt, not just a diff.
