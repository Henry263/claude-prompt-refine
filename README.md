# Claude Prompt-Refiner

Turns a short, informal ask ("build me a prompt that does X but not Y") into a
fully-structured, **use-case-aware** prompt — goal, do/don't, output format,
edge cases — confirms it with you before use, then either runs it or hands it
back as a clean, reusable block.

It automatically detects which category the ask falls into (coding,
content/marketing, product/SaaS feature, documentation, or personal) and
weights the draft accordingly — e.g. coding asks get stricter scope
boundaries, marketing asks get tighter format/tone defaults, product/SaaS
asks get stronger anti-hallucination guardrails. See
`plugins/prompt-refiner-plugin/skills/prompt-refiner/references/use-case-playbooks.md`
for the full breakdown per category.

**A note on `/` access:** true slash-command autocomplete (typing `/` and
seeing it appear) is a **Claude Code** feature only. In claude.ai (browser),
skills auto-trigger based on your request matching the description above —
there's no formal slash-command parser there. Typing `/prompt-refiner` in
claude.ai still works as a plain-text way of saying "use this skill
explicitly," but it won't autocomplete or show in a command list.

**What works where:**

| Capability | claude.ai | Claude Code |
|---|:---:|:---:|
| Auto-trigger on a matching request | ✅ | ✅ |
| Force the skill explicitly | ✅ (plain language) | ✅ (plain language or `/`) |
| Force a specific use-case category | ✅ (plain language) | ✅ (plain language or `/` + argument) |
| `/` autocomplete menu | ❌ | ✅ |

The functional parts — auto-trigger, forcing the skill, forcing a category —
work identically on both platforms. The only thing claude.ai can't do is the
`/` autocomplete UI itself, since that's a client feature rather than
something a skill file controls.

This repo supports two install paths:

- **Option A — claude.ai (browser)**: upload the skill as a zip through Settings.
- **Option B — Claude Code**: install it as a plugin via a one-line marketplace command.

Pick whichever matches how you use Claude. Both use the exact same `SKILL.md`.

**How to start after installation:**
- go to the new chat. copy and paste the follwoing statement
'Hey Claude—I just added the “prompt-refiner” skill. Can you make something amazing with it?'

---

## Option A: Install in claude.ai (browser)

1. Download **`claude-ai-upload/prompt-refiner.zip`** from this repo (the zip file)
2. In claude.ai, go to **Settings → Capabilities** and make sure **"Code
   execution and file creation"** is turned on. Skills require this.

3. Go to **Settings → Capabilities → Skills** (may also appear under
   **Customize → Skills**).

4. Click **Upload skill** (or the **+** button) and select your
   `prompt-refiner.zip`.

5. Toggle the skill **on** once it appears in your list.

**Using it:** in any chat, just describe what you want a prompt to do
("write me a prompt that drafts follow-up emails, formal tone, under 100
words"). Claude will recognize the request and draft a structured prompt,
show it to you, and wait for your confirmation before using or handing it
back. You can also invoke it explicitly — *"use my prompt-refiner skill for
this"* — and force a specific use-case category by naming it directly, e.g.
*"use prompt-refiner, treat this as coding: ..."* This manual-override path
works identically here and in Claude Code; only the `/`-argument shortcut is
Claude Code-only.

Note: skills you upload to claude.ai are private to your account. If you're
on a Team/Enterprise plan and want to share it org-wide, see Anthropic's
"Provision skills for your organization" docs.

---

## Option B: Install in Claude Code (plugin marketplace)

This repo is also a valid **plugin marketplace**, so Claude Code users can
install it with two commands — no manual file copying.

1. Open a terminal in any project and start Claude Code.

2. Add this repo as a marketplace:
   ```
   /plugin marketplace add https://github.com/Henry263/claude-prompt-refine
   ```

3. Install the plugin:
   ```
   /plugin install prompt-refiner-plugin@prompt-refiner-marketplace
   ```

4. Reload plugins if needed:
   ```
   /reload-plugins
   ```

**Using it:** Claude Code auto-triggers the skill when your request matches,
same as claude.ai. You can also invoke it directly by name:
```
/prompt-refiner-plugin:prompt-refiner
```

**Forcing a specific use-case category (works on both platforms):** name it
directly and the skill skips auto-classification.

In claude.ai — plain language:
```
Use prompt-refiner, treat this as coding: refactor the auth module
```

In Claude Code — same plain language, or pass it as an argument:
```
/prompt-refiner-plugin:prompt-refiner coding refactor the auth module
```
Valid category keywords: `coding`, `content`, `product`, `docs`, `personal`.
Anything else after the skill name is treated as the ask itself and
classified automatically — you don't need to specify a category most of the
time, on either platform.

To update later, after you push changes to this repo:
```
/plugin marketplace update prompt-refiner-marketplace
```

---

## Repo structure

```
prompt-refiner/
├── README.md
├── LICENSE
├── .claude-plugin/
│   └── marketplace.json          ← marketplace catalog (Claude Code)
├── plugins/
│   └── prompt-refiner-plugin/
│       ├── .claude-plugin/
│       │   └── plugin.json       ← plugin manifest (Claude Code)
│       └── skills/
│           └── prompt-refiner/
│               ├── SKILL.md              ← the actual skill
│               └── references/
│                   └── use-case-playbooks.md   ← per-category tailoring rules
└── claude-ai-upload/
   └── prompt-refiner.zip    ← Download this zip for the browser based claude.
    └── prompt-refiner/
        ├── SKILL.md                      ← same skill, packaged for zip upload
        └── references/
            └── use-case-playbooks.md
```

The skill content is identical in both places — one copy is wired up as a
Claude Code plugin, the other is a plain folder ready to zip for claude.ai.
When zipping for claude.ai, zip the whole `prompt-refiner` folder (including
`references/`), not just `SKILL.md` on its own.

## How it works

1. You give a short, informal ask.
2. The skill extracts the goal, explicit/implicit constraints, and expected
   output format — asking at most one clarifying question if the ask is too
   thin.
3. It classifies the ask into a use case (coding, content/marketing,
   product/SaaS feature, documentation, or personal) and pulls the matching
   playbook from `references/use-case-playbooks.md` to decide which fields to
   weight harder and what defaults to reach for.
4. It drafts a structured prompt (GOAL / DO / DON'T / FORMAT / EDGE CASES)
   shaped by that playbook, and shows it back to you — flagging assumptions
   and noting which playbook it used.
5. It waits for your confirmation or edits — it never skips this step.
6. Once confirmed, it either continues the conversation with the refined
   prompt, or hands it back as a clean copy-pasteable block for use
   elsewhere (another tool, an API call, etc.).


## Contributing

Contributions are welcome! Feel free to open a pull request to add new skills, improve the existing `SKILL.md`, fix bugs, or enhance the documentation. If you have an idea or run into an issue, open a GitHub Issue to start the conversation. There are no strict guidelines — just fork the repo, make your changes, and submit a PR.

## License

MIT — see [LICENSE](LICENSE).
