# Daily Assistant

Daily Assistant is a Markdown-first planning ritual for maintaining one daily plan through one-question-at-a-time dialogue.

It supports `build`, `edit`, and `delete` planning, checkpoints after every three user answers, and optional time blocks when scheduling is requested.

## Use

Point an agent at `SKILL.md`:

```text
Use Daily_Assistant/SKILL.md to maintain today's plan.
```

Daily plans live in:

```text
plans/YYYY-MM-DD.md
```

Each date should have one source-of-truth plan file. Real daily plans are ignored by git; publish `.example.md` files only.

## Files

- `SKILL.md` - canonical Daily Assistant protocol.
- `templates/daily-plan.md` - reusable daily plan template.
- `plans/README.md` - daily plan storage convention.
- `plans/2026-04-30.example.md` - fake example plan.
- `docs/superpowers/specs/2026-04-30-daily-assistant-plan-design.md` - approved design spec.
- `docs/superpowers/plans/2026-04-30-daily-assistant-implementation-plan.md` - implementation plan used to build this artifact.

## Superpowers Reference

This project was designed and implemented with the Superpowers workflow as a process reference.

- Official repository: https://github.com/obra/superpowers
- Skill authoring reference: https://github.com/obra/superpowers/blob/main/skills/writing-skills/SKILL.md
- License reference: https://github.com/obra/superpowers/blob/main/LICENSE

No Superpowers source code is vendored here. See `THIRD_PARTY_NOTICES.md`.

## Verification

```bash
sed -n '1,40p' SKILL.md
rg -n "^## (Focus|Now|Later|Questions|Log|Done|Deleted|Time Blocks)$" templates/daily-plan.md
rg -n "OAuth|token|credential|send email|create event|write to Google|MCP server" SKILL.md templates/daily-plan.md plans/README.md plans/2026-04-30.example.md
```
