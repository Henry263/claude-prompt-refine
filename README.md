# prompt-refiner

Turns a short, informal ask ("build me a prompt that does X but not Y") into a
fully-structured prompt — goal, do/don't, output format, edge cases — confirms
it with you before use, then either runs it or hands it back as a clean,
reusable block.

This repo supports two install paths:

- **Option A — claude.ai (browser)**: upload the skill as a zip through Settings.
- **Option B — Claude Code**: install it as a plugin via a one-line marketplace command.

Pick whichever matches how you use Claude. Both use the exact same `SKILL.md`.

---

## Option A: Install in claude.ai (browser)

1. Download **`claude-ai-upload/prompt-refiner`** from this repo (the folder
   contains a single `SKILL.md`), and zip it — the zip must contain a
   *folder* with `SKILL.md` inside, not the bare file.

   From a terminal, if you've cloned the repo:
   ```bash
   cd claude-ai-upload
   zip -r prompt-refiner.zip prompt-refiner
   ```
   Or on GitHub: download the repo as a zip, then re-zip just the
   `claude-ai-upload/prompt-refiner` folder on its own.

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
back. You can also invoke it explicitly: *"use my prompt-refiner skill for
this."*

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
   /plugin marketplace add <your-github-username>/prompt-refiner
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
│               └── SKILL.md      ← the actual skill
└── claude-ai-upload/
    └── prompt-refiner/
        └── SKILL.md              ← same skill, packaged for zip upload
```

The skill content is identical in both places — one copy is wired up as a
Claude Code plugin, the other is a plain folder ready to zip for claude.ai.

## How it works

1. You give a short, informal ask.
2. The skill extracts the goal, explicit/implicit constraints, and expected
   output format — asking at most one clarifying question if the ask is too
   thin.
3. It drafts a structured prompt (GOAL / DO / DON'T / FORMAT / EDGE CASES)
   and shows it back to you, flagging any assumptions it made.
4. It waits for your confirmation or edits — it never skips this step.
5. Once confirmed, it either continues the conversation with the refined
   prompt, or hands it back as a clean copy-pasteable block for use
   elsewhere (another tool, an API call, etc.).

## License

MIT — see [LICENSE](LICENSE).
