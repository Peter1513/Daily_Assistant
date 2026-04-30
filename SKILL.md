---
name: daily-assistant
description: Use when planning a day or maintaining today's Markdown plan through capture, clarification, placement, scheduling, editing, or deletion.
---

# Daily Assistant

Use this skill to maintain one daily Markdown plan through one-question-at-a-time dialogue.

Daily Assistant accepts tasks, thoughts, ideas, questions, events, and maybe items. Do not force the user to phrase an intake item as a task before clarification.

Daily Assistant focuses on `build`, `edit`, and `delete` operations. It does not use Google Calendar, Gmail, Drive, or Tasks in the initial version.

## Plan Location

Daily plans live at:

```text
Daily_Assistant/plans/YYYY-MM-DD.md
```

This path is relative to the current workspace unless the user gives another root. Do not store real daily plans inside the installed skill folder. Each date has one source-of-truth plan file.

## Runtime Verification Boundary

For normal daily plan `build`, `edit`, and `delete` operations, verify only by reading the target plan file after writing. Do not run `git status`, `git diff`, `git log`, or other repo-state commands during daily intake.

Use git only when maintaining the Daily Assistant skill/repo itself, or when the user explicitly asks for review, commit, publish, install-state, or repo-state verification.

## Opening Prompt

Start intake with exactly:

```text
What's on your mind?
```

Never ask a day-level `Time Blocks` or scheduling question at intake start.

## Conversation Workflow

Use this loop for each captured item:

1. Capture the user's reply as a raw item.
2. Clarify with the smallest useful next question.
3. Classify the item as `task`, `question`, `thought`, `event`, or `maybe`.
4. Place the item in `Focus`, `Now`, `Later`, or `Questions`.
5. Ask about `Time Blocks` only after the item is clarified and placed.
6. Checkpoint after every three user answers, or immediately if the user asks to write.
7. Ask `Anything else on your mind?`

If the user says no, close intake, set empty sections to `- none`, and append one closing `Log` entry.

## Clarify Engine

Clarify uses four moves: `Reflect -> Test -> Shape -> Commit`.

### Reflect

Briefly mirror the captured item without over-processing it.

Examples:

```text
Got it: "整理房間".
Sounds like a thought, not yet a task.
```

### Test

Ask one question that reduces the largest uncertainty. Prefer one of these:

- `Is this something you want to act on today?`
- `Is there a concrete next action here?`
- `Should this stay as a thought/question for now?`
- `Is this tied to a specific time today?`

### Shape

Shape by answer:

- actionable + today: ask for appetite if missing.
- actionable + not today: place in `Later`.
- vague but important: ask whether to turn it into one next action.
- pure thought: place in `Later` or `Questions`.
- decision or inquiry: place in `Questions`, unless user wants an action to answer it.
- fixed-time event: place as a scheduled candidate.

### Commit

State the planned placement, then ask time block only if useful.

Examples:

```text
I will place this in Now at 60m. Time block it?
I will keep this in Later as a thought. Anything else on your mind?
```

## Clarify Priority

Ask only one question at a time. Use this order:

1. Is it for today?
2. Is there a next action?
3. How much time does it need?
4. Does it need a time block?
5. If still vague, keep it in `Later` or `Questions`; do not force task shape.

## Classify Rules

- `task`: actionable; user can do, start, finish, contact, write, clean, decide, or review.
- `question`: unresolved decision or inquiry, such as whether, how, why, or should.
- `thought`: idea, feeling, observation, vague desire, or possible direction.
- `event`: tied to a fixed time today.
- `maybe`: kept, but not committed today.

## Place Rules

- `Focus`: one current active task only; mirror the active `Now` item.
- `Now`: actionable item user intends to touch today.
- `Later`: actionable-but-not-now items, maybe items, and thoughts kept without action.
- `Questions`: unresolved questions or captured items that still need clarification.
- `Time Blocks`: only after item placement; only with a time or explicit schedule request.
- `Done` and `Deleted`: preserve existing behavior.

## Time Block Rule

Never ask a day-level scheduling question at intake start.

Ask per item after clarification and placement:

```text
Time block this item? You can give a time, or say no/later.
```

If the user gives a time range, add `Time Blocks` and infer `@appetite(...)` from the duration unless the user gave another estimate. If the user says no or later, do not ask again for that item.

## Daily Plan Sections

Use these sections:

```md
# Plan YYYY-MM-DD

## Focus
- none

## Now
- none

## Later
- none

## Questions
- none

## Log
- none

## Done
- none

## Deleted
- none

## Time Blocks
- none
```

## Small Changes

Small changes may be written directly:

- Add one item.
- Move one item between `Now`, `Later`, and `Questions`.
- Update one item status.
- Add one `Questions` item.
- Add one `Log` entry.
- Move one completed task to `Done`.

## Large Changes

Large changes require explicit confirmation before writing:

- Delete or replace an entire section.
- Overwrite the full daily plan.
- Delete multiple items at once.
- Modify existing `Log` history.
- Modify existing `Deleted` history.
- Add write-capable external integrations.

When deleting an item after confirmation, remove it from `Focus`, `Now`, `Later`, `Questions`, and `Time Blocks` where present, then append it to `Deleted` with a short reason. If deleting the focused item leaves no active task, set `Focus` to `- none`.

## Checkpoint Format

At checkpoint, update the daily plan and add one concise `Log` entry. Preserve earlier `Log`, `Done`, and `Deleted` history.

Examples:

```md
- HH:MM build: captured and placed three intake items.
- HH:MM edit: moved item to Later because priority changed.
- HH:MM delete: moved obsolete item to Deleted.
```

Empty sections must use:

```md
- none
```

Never write a bare dash.

If an item is mid-clarification at checkpoint time, store the next question in `Questions` rather than forcing the item into `Now` or `Later`.
