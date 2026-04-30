# Daily Assistant

Daily Assistant is a Markdown-first planning ritual for maintaining one daily plan through gentle capture and one-question-at-a-time clarification.

It starts with `What's on your mind?`, accepts tasks, thoughts, ideas, questions, events, and maybe items, then uses `Capture -> Clarify -> Classify -> Place -> Optional Time Block`. It checkpoints after every three user answers and asks about `Time Blocks` only per item after clarification and placement.

## Use

Daily Assistant is published as a Codex skill, not a Codex plugin.

`SKILL.md` is the canonical protocol. The installed Codex skill at `~/.codex/skills/daily-assistant/SKILL.md` is a local copy and may drift until reinstalled from GitHub.

### Install for Codex

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo Peter1513/Daily_Assistant \
  --path . \
  --name daily-assistant
```

Restart Codex after installation so the new skill is discovered.

Verify:

```bash
sed -n '1,20p' ~/.codex/skills/daily-assistant/SKILL.md
```

### Invoke

After install:

```text
Use $daily-assistant to plan today.
```

Without installing, point an agent at `SKILL.md`:

```text
Use Daily_Assistant/SKILL.md to maintain today's plan.
```

### Update

The installer refuses to overwrite an existing skill. After pushing source updates to GitHub, reinstall from GitHub:

```bash
rm -rf ~/.codex/skills/daily-assistant
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo Peter1513/Daily_Assistant \
  --path . \
  --name daily-assistant
```

Restart Codex after reinstalling so the loaded skill text is refreshed.

## Development Breakpoint

Status as of 2026-05-01:

- Branch: `dev`, pushed to `origin/dev`.
- Slim work commits on `dev`: `2136c37` runtime boundary, `f014df3` examples extraction, `799a235` caveman-lite rewrite.
- Slim skill payload: `SKILL.md` is 81 lines; `references/examples.md` is 39 lines.
- Installed Codex copy: reinstalled from GitHub with `--ref dev`; package-aware payload diff returned no output.
- Codex L3 live check: build, edit, and delete cases were run against `/tmp/daily-assistant-l3-codex-write` and matched the output assertions.
- Claude Code L3 live check: deferred by user. A CLI attempt stopped at API/socket availability, not at a skill behavior assertion.
- Merge status: `dev` is not merged into `main`. Do not merge until Claude Code L3 is tested or the user explicitly waives that gate.

### Resume for Codex

Start here:

```bash
cd /home/peter/Project/Plan_Build/Daily_Assistant
git status --short --branch
git log --oneline --decorate -8
wc -l SKILL.md references/examples.md
```

Expected state:

```text
## dev...origin/dev
<sha> (HEAD -> dev, origin/dev) docs: record daily assistant slimming breakpoint
799a235 docs(skill): rewrite SKILL.md in caveman lite + structural dedup
f014df3 docs(skill): extract examples to references/
2136c37 docs: add runtime verification boundary section
b4fa901 docs(plan): add daily assistant skill slim implementation plan
```

Confirm installed Codex payload still matches `dev`:

```bash
SRC=/home/peter/Project/Plan_Build/Daily_Assistant
DST=/home/peter/.codex/skills/daily-assistant
for f in SKILL.md README.md LICENSE THIRD_PARTY_NOTICES.md \
         github-reference-repos.md .gitignore \
         plans/README.md plans/2026-04-30.example.md \
         templates/daily-plan.md agents/openai.yaml; do
  diff -q "$SRC/$f" "$DST/$f" || echo "DIFF: $f"
done
diff -rq "$SRC/references" "$DST/references"
```

Expected: empty output.

Next gate:

1. Run Claude Code L3 build/edit/delete verification from `docs/superpowers/plans/2026-05-01-daily-assistant-skill-slim-implementation-plan.md`.
2. If Claude Code L3 passes, or if the user explicitly waives it, merge `dev -> main`.
3. Reinstall from the README default command, which pulls `main`, and restart Codex.

### Remove

```bash
rm -rf ~/.codex/skills/daily-assistant
```

Daily plans live in:

```text
plans/YYYY-MM-DD.md
```

Each date should have one source-of-truth plan file. Real daily plans are ignored by git; publish `.example.md` files only.

## Files

- `SKILL.md` - canonical Daily Assistant protocol.
- `references/examples.md` - on-demand examples for Reflect, Commit, and Checkpoint moves.
- `templates/daily-plan.md` - reusable daily plan template.
- `plans/README.md` - daily plan storage convention.
- `plans/2026-04-30.example.md` - fake example plan.
- `docs/superpowers/specs/2026-04-30-daily-assistant-plan-design.md` - original approved design spec.
- `docs/superpowers/specs/2026-04-30-daily-assistant-conversation-workflow-design.md` - approved conversation workflow update spec.
- `docs/superpowers/specs/2026-05-01-daily-assistant-skill-slim-design.md` - approved skill slimming design.
- `docs/superpowers/plans/2026-04-30-daily-assistant-implementation-plan.md` - original implementation plan used to build this artifact.
- `docs/superpowers/plans/2026-04-30-daily-assistant-conversation-workflow-implementation-plan.md` - implementation plan for the conversation workflow update.
- `docs/superpowers/plans/2026-05-01-daily-assistant-skill-slim-implementation-plan.md` - active breakpoint plan for the slimming work.

## Superpowers Reference

This project was designed and implemented with the Superpowers workflow as a process reference.

- Official repository: https://github.com/obra/superpowers
- Skill authoring reference: https://github.com/obra/superpowers/blob/main/skills/writing-skills/SKILL.md
- License reference: https://github.com/obra/superpowers/blob/main/LICENSE

No Superpowers source code is vendored here. See `THIRD_PARTY_NOTICES.md`.

## Verification

```bash
sed -n '1,220p' SKILL.md
sed -n '1,120p' templates/daily-plan.md
sed -n '1,160p' plans/2026-04-30.example.md
rg -n "What's on your mind\\?|Reflect -> Test -> Shape -> Commit|Time block this item\\? You can give a time, or say no/later\\.|- none" SKILL.md templates/daily-plan.md plans/2026-04-30.example.md
rg -n "^## (Focus|Now|Later|Questions|Log|Done|Deleted|Time Blocks)$" templates/daily-plan.md plans/2026-04-30.example.md
rg -n "Do you need these tasks scheduled into time blocks|At build time, ask once|At build time and checkpoint time|^- $" SKILL.md templates/daily-plan.md plans/2026-04-30.example.md
rg -n "OAuth|token|credential|send email|create event|write to Google|MCP server" SKILL.md templates/daily-plan.md plans/README.md plans/2026-04-30.example.md
```
