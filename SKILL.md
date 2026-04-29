---
name: daily-assistant
description: Use when maintaining a daily Markdown plan through build, edit, or delete planning.
---

# Daily Assistant

Use this skill to maintain a daily Markdown plan through one-question-at-a-time dialogue.

Daily Assistant focuses on `build`, `edit`, and `delete` operations. It does not use Google Calendar, Gmail, Drive, or Tasks in the initial version.

## Plan Location

Daily plans live at:

```text
Daily_Assistant/plans/YYYY-MM-DD.md
```

Each date has one source-of-truth plan file.

## Ritual

1. Ask one question at a time.
2. Track whether the user is building, editing, or deleting plan content.
3. After every three user answers, checkpoint the plan to Markdown.
4. If the user asks to write earlier, checkpoint immediately.
5. At build time and checkpoint time, ask: "Do you need these tasks scheduled into time blocks?"
6. Add or update `Time Blocks` only when the user requests scheduling.

## Daily Plan Sections

Use these sections:

```md
# Plan YYYY-MM-DD

## Focus
- [ ] current active item

## Now
- [ ] task @appetite(25m) @status(active)

## Later
- [ ] deferred task

## Questions
- next question to resolve

## Log
- HH:MM build/edit/delete: reason

## Done
- [x] completed task

## Deleted
- removed task - reason

## Time Blocks
- only added when scheduling is requested
```

## Small Changes

Small changes may be written directly:

- Add one task.
- Move one task between `Now` and `Later`.
- Update one task status.
- Add one `Questions` item.
- Add one `Log` entry.
- Move one completed task to `Done`.

## Large Changes

Large changes require explicit confirmation before writing:

- Delete or replace an entire section.
- Overwrite the full daily plan.
- Delete multiple tasks at once.
- Modify existing `Log` history.
- Modify existing `Deleted` history.
- Add write-capable external integrations.

## Checkpoint Format

At checkpoint, update the daily plan and add one concise `Log` entry:

```md
- HH:MM build: created initial plan from user answers.
- HH:MM edit: moved task to Later because priority changed.
- HH:MM delete: moved obsolete task to Deleted.
```
