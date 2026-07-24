# Use-case playbooks

Read this after classifying the ask (Step 1.5 in SKILL.md). Each playbook lists
signal words, which fields to weight harder in the draft, and defaults to
reach for unless the user says otherwise. Use these as a starting point, not
a rigid template — override anything the user states explicitly.

---

## Coding / engineering
**Signals:** mentions of code, repo, function, bug, refactor, migration, API, deploy, tests, "don't touch X".

**Weight harder:** DON'T (scope boundaries — what NOT to touch matters more here than almost any other category) and EDGE CASES (partial states, mixed old/new code, backward compatibility).

**Defaults:**
- DO: work incrementally / file-by-file, preserve existing business logic unless told otherwise, show diffs or before/after
- DON'T: don't introduce new dependencies unless necessary, don't touch unrelated files, don't silently change behavior
- FORMAT: default to file-by-file walkthrough with a summary at the end, not a single monolithic dump
- Flag if the ask implies a framework/library/version assumption that should be confirmed (e.g. which stack, which package manager)

---

## Content / marketing
**Signals:** blog post, social post, newsletter, ad copy, brand voice, SEO, landing page.

**Weight harder:** FORMAT (tone, length, audience) and DO (brand voice specifics).

**Defaults:**
- DO: match a specified tone/voice, target a stated audience, include a clear CTA if it's promotional
- DON'T: don't use generic filler phrases, don't make unverifiable claims
- FORMAT: default to specifying word/character count and platform (e.g. "under 280 characters for X/Twitter") since content pieces without a length bound tend to run long
- Ask/assume the audience if not stated — this changes tone more than almost anything else in this category

---

## Product / SaaS building (in-app AI features, API prompts)
**Signals:** "prompt for our app," "AI feature that does X," references to an API call, a specific product (e.g. Connectiko, Finqdoc, Beat the AI), lead qualification, document processing, financial/investment content.

**Weight harder:** DON'T (hallucination/scope guardrails — this is prompt going into a live product, so failure modes matter) and EDGE CASES (what happens on missing/malformed input).

**Defaults:**
- DO: ground output only in provided input data, return errors/fallbacks gracefully rather than guessing
- DON'T: don't infer or fabricate data not present in the source (especially critical for financial or lead data), don't give unqualified investment/financial advice if user-facing
- FORMAT: default to structured output (JSON) if this will be consumed by code rather than read by a human — ask if unclear
- Note: if this is for financial, medical, or legal-adjacent output, flag that a disclaimer or human-review step may be warranted

---

## Documentation / knowledge work
**Signals:** "write a doc about X," meeting notes, onboarding material, internal wiki, summaries, action items.

**Weight harder:** FORMAT (structure/depth) and GOAL (what will the reader do with this).

**Defaults:**
- DO: state the intended audience and their existing familiarity with the topic, specify depth (overview vs. deep-dive)
- DON'T: don't pad with restated context the audience already has
- FORMAT: default to headers + short sections over dense prose for anything meant to be scanned rather than read start-to-finish

---

## Personal / misc (resumes, interview prep, research summaries)
**Signals:** resume, cover letter, interview, personal research, one-off asks with no reuse intent.

**Weight harder:** GOAL and DO (personal asks are usually simpler — don't over-engineer the DON'T list).

**Defaults:**
- DO: reflect the person's actual background/experience rather than generic templates
- FORMAT: keep tight — personal-use prompts don't need heavy structure, a short GOAL + DO block is often enough; skip EDGE CASES entirely unless genuinely relevant

---

## If nothing matches clearly
Default to the general shape in SKILL.md step 2 with no extra weighting. Don't force a category — most asks that don't clearly fit one of the above are fine with the plain template.
