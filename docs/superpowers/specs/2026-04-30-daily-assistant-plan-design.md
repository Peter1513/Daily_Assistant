# Daily Assistant Plan Design

Date: 2026-04-30

## Purpose

Daily Assistant is a lightweight planning ritual for maintaining a daily Markdown plan through one-question-at-a-time dialogue.

It focuses on `build`, `edit`, and `delete` operations for a daily plan. Google plugins and calendar/email integrations are out of scope for the initial design; they may be added later as adapters.

## File Location

Daily plans live at:

```text
Daily_Assistant/plans/YYYY-MM-DD.md
```

Each date has one source-of-truth file.

## Daily Plan Schema

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

## Dialogue Flow

- Ask one question at a time.
- Maintain the plan through `build`, `edit`, and `delete`.
- After every three user answers, checkpoint the plan to Markdown.
- If the user asks to write earlier, checkpoint immediately.
- At build time and checkpoint time, ask whether the user needs tasks scheduled into time blocks.
- Add or update `Time Blocks` only when scheduling is requested.

## Change Rules

Small changes may be written directly:

- Add one task.
- Move one task between `Now` and `Later`.
- Update one task status.
- Add one `Questions` item.
- Add one `Log` entry.
- Move one completed task to `Done`.

Large changes require explicit confirmation first:

- Delete or replace an entire section.
- Overwrite the full daily plan.
- Delete multiple tasks at once.
- Modify existing `Log` history.
- Modify existing `Deleted` history.
- Add write-capable external integrations.

## Design Rationale

The schema uses a B+C lite shape:

- `Focus`, `Now`, and `Later` keep the active plan simple.
- `Questions` preserves the next planning question.
- `Log`, `Done`, and `Deleted` support maintainable build/edit/delete history.
- `Time Blocks` stays optional so daily planning does not become schedule-heavy by default.

This matches the selected approach: a maintainable daily plan with enough audit trail to recover decisions, but not a full project-management system.

## Reference Patterns

- todo.txt: one-line plain-text tasks with minimal metadata.
- TaskPaper: simple project/task/note/tag structure.
- Bullet Journal rapid logging: low-friction task, event, and note capture.
- Andrej Karpathy projects: prefer simple, readable artifacts over heavy structure.
- Peter Steinberger agent workflow: discuss options, iterate, and keep canonical shared docs.
- Shape Up: use appetite and fixed-time thinking when scheduling is needed.

## Initial Non-Goals

- No Google Calendar, Gmail, Drive, or Tasks integration in the first design.
- No automatic external writes.
- No multi-file index.
- No full task database.
- No fixed daily schedule unless the user asks for scheduling.

## Verification

For docs-only changes:

- Read the spec back after writing.
- Check for placeholders, contradictions, ambiguous requirements, and scope creep.
- If the workspace is not a git repo, report that no commit was made.
