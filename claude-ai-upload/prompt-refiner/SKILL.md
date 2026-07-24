---
name: prompt-refiner
description: Turns a short, informal user ask into a fully-structured, use-case-aware prompt (goal, constraints, do/don't list, output format, edge cases) before it gets used. Use this whenever the user gives a brief or underspecified request and says something like "build me a prompt that...", "I want AI to do X but not Y", "write a prompt for...", "help me prompt for...", or otherwise signals they want a reusable, structured prompt drafted and confirmed rather than the task done directly. Detects whether the ask is coding, content/marketing, product/SaaS-feature, documentation, or personal, and tailors emphasis accordingly. Always confirm the drafted prompt with the user before treating it as final.
argument-hint: "[optional: coding|content|product|docs|personal] [your ask]"
---

# Prompt Refiner

Turns a 2-3 sentence ask into a tight, structured prompt, confirms it with the user, then either hands it off or runs it — never both draft and execute in the same breath without a checkpoint.

## When this triggers

The user gives a rough, brief description of something they want an AI to do — often followed by constraints tacked on almost as an afterthought ("...and don't do X"). The tell is that they're describing what they want a *prompt* to accomplish, not asking you to do the underlying task yourself right now.

Do NOT trigger this for requests that are already well-specified prompts, or for simple one-off tasks where drafting a reusable prompt would be overkill (e.g. "summarize this paragraph" — just do it).

## Handling arguments (Claude Code manual invocation only)

When invoked as `/prompt-refiner-plugin:prompt-refiner <args>` in Claude Code, `$ARGUMENTS` is available as text. If it starts with one of `coding`, `content`, `product`, `docs`, `personal`, treat that as the same kind of explicit override described in Step 2, and treat the rest of `$ARGUMENTS` as the ask. Otherwise treat the whole string as the ask and classify normally.

This is a Claude Code-specific convenience — the underlying override mechanism (naming a category directly) works identically in claude.ai via plain language, as described in Step 2. Neither platform requires the other to function.

## Workflow

### 1. Extract intent
From the user's short ask, identify:
- **Core goal** — what the output should actually accomplish
- **Explicit constraints** — anything they said to include or avoid
- **Implicit constraints** — reasonable inferences from context (tone, audience, prior conversation) — but flag these as assumptions, don't silently bake them in
- **Output format** — is one implied (list, code, JSON, email, etc.)? If not, ask or pick a sensible default and say so

If the ask is too thin to do this confidently (e.g., single ambiguous sentence with no context), ask ONE clarifying question rather than guessing. Otherwise, proceed — don't stall on minor ambiguity.

### 2. Classify the use case
**Check for an explicit override first.** If the user directly names a category in plain language — "use the coding playbook," "treat this as content/marketing," "this is for our product" — skip auto-classification entirely and go straight to that category's playbook. This is the manual-override path and works the same way on every platform, including claude.ai where there's no `/`-argument syntax.

Otherwise, quickly classify the ask into one of these categories based on signal words and context:
- **Coding / engineering** — code, repo, bug, refactor, migration, API, deploy, tests
- **Content / marketing** — blog post, social post, newsletter, ad copy, brand voice, SEO
- **Product / SaaS building** — an in-app AI feature, an API-consumed prompt, a specific product name, lead/data processing
- **Documentation / knowledge work** — internal docs, meeting notes, onboarding material, summaries
- **Personal / misc** — resumes, interview prep, one-off personal research

Read `references/use-case-playbooks.md` for the matching category and apply its guidance — which fields to weight harder, common DO/DON'T defaults, and format defaults for that category. If nothing matches clearly, skip this and use the plain template in Step 3 with no extra weighting; don't force a category.

### 3. Draft the structured prompt
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

### 4. Confirm with the user
Show the drafted prompt back verbatim, and briefly flag anything you assumed (e.g., "I assumed you want this in Markdown — let me know if you want plain text instead"). If a use-case category shaped the draft, mention which one in one short phrase (e.g., "drafted this with the coding playbook — scoped DON'Ts to protect existing logic"). Ask if it's ready to use or needs edits. **Do not proceed to step 5 until the user confirms or edits.**

### 5. Dispatch
Once confirmed:
- If the user wants it run right now in this conversation, just continue with the confirmed prompt as the next turn.
- If the user wants it saved for reuse (e.g., to paste into another tool, or to call via the Anthropic API from their own app), give it back as a clean copy-pasteable block — no extra commentary mixed in.
- If they want it applied against a different model/tool, hand back the final block only; don't assume you can dispatch outbound on their behalf.

## Notes
- One clarifying question max per round — if there's more ambiguity than that, draft your best guess and let the confirmation step catch the rest.
- Never skip the confirmation step, even if the ask seems simple. The whole point of this skill is the checkpoint.
- If the user iterates on the draft ("no, also exclude Y"), fold it in and re-show the full updated prompt, not just a diff.
