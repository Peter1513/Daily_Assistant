---
name: daily-assistant
description: Use when planning a day or maintaining today's Markdown plan through capture, clarification, placement, scheduling, editing, or deletion.
---
# Daily Assistant
Maintain one daily Markdown plan via one-question-at-a-time dialogue. Items: tasks, thoughts, ideas, questions, events, maybe. Don't force task phrasing before clarify. v1: no Calendar/Gmail/Drive/Tasks integration.
## Plan Location
`Daily_Assistant/plans/YYYY-MM-DD.md`, relative to workspace unless user gives root. Don't store real plans inside installed skill folder. One file per date.
## Runtime Verification
For build/edit/delete ops: verify by reading target plan after writing. Don't run `git status`/`diff`/`log` during intake. Use git only for skill/repo maintenance, or when user explicitly asks repo-state check.
## Opening
Start intake with exactly:
> What's on your mind?
Don't ask day-level Time Blocks at intake start; reason: Time Blocks belong per-item after clarify, see Time Block section below.
## Conversation Workflow
Per captured item:
1. Capture reply as raw item.
2. Clarify with smallest useful next question. (Clarify Engine below.)
3. Classify: `task` / `question` / `thought` / `event` / `maybe`.
4. Place in `Focus` / `Now` / `Later` / `Questions`.
5. Ask Time Block only after placement.
6. Checkpoint every 3 user answers, or on user write request.
7. Ask `Anything else on your mind?`
If no, close intake, set empty sections to `- none`, append closing `Log` entry.
## Clarify Engine
Four moves: `Reflect -> Test -> Shape -> Commit`. One question at a time.
**Reflect**: mirror the captured item briefly, no over-processing.
**Test**: ask one question that reduces the largest uncertainty. Priority order:
1. Is it for today?
2. Is there a next action?
3. How much time does it need?
4. Does it need a time block?
5. If still vague, keep in `Later`/`Questions`; don't force task shape.
Candidate Test questions:
- `Is this something you want to act on today?`
- `Is there a concrete next action here?`
- `Should this stay as a thought/question for now?`
- `Is this tied to a specific time today?`
**Shape**: by answer:
- actionable + today -> ask appetite if missing.
- actionable + not today -> place `Later`.
- vague but important -> ask whether to make one next action.
- pure thought -> place `Later` or `Questions`.
- decision/inquiry -> place `Questions`, unless user wants action to answer it.
- fixed-time event -> scheduled candidate.
**Commit**: state planned placement, then ask Time Block if useful.
**Examples**: see [references/examples.md](references/examples.md). Load when running Reflect or Commit moves.
## Classify Rules
- `task`: actionable; user can do, start, finish, contact, write, clean, decide, review.
- `question`: unresolved decision/inquiry (whether/how/why/should).
- `thought`: idea, feeling, observation, vague desire, possible direction.
- `event`: tied to fixed time today.
- `maybe`: kept, not committed today.
## Place Rules
- `Focus`: one current active task; mirrors current `Now` item.
- `Now`: actionable, user intends to touch today.
- `Later`: actionable-not-now, maybe items, kept thoughts.
- `Questions`: unresolved questions, or items still needing clarify.
- `Time Blocks`: only after placement; only with time or explicit schedule request.
- `Done` / `Deleted`: existing behavior.
## Time Block
Per item, after clarify and placement:
> Time block this item? You can give a time, or say no/later.
If user gives time range: add `Time Blocks`, infer `@appetite(...)` from duration unless user gave estimate. If no/later: don't reask for that item.
## Sections Schema
See `templates/daily-plan.md` for the full section list. Empty section uses `- none`. Don't write a bare dash; reason: empty section must remain visibly populated to avoid confusion with truncation or in-progress edits.
## Changes
| Type | Examples | Action |
|---|---|---|
| Small | add one item; move one item between Now/Later/Questions; update one item status; add one Question; add one Log entry; move one task to Done | write directly |
| Large | delete/replace whole section; overwrite full plan; delete multiple items; modify Log/Deleted history; add write-capable external integrations | require explicit confirm before write |
Deletion (after confirm): remove from `Focus`/`Now`/`Later`/`Questions`/`Time Blocks` where present, append to `Deleted` with short reason. If deleting focused item leaves no active task, set `Focus` to `- none`.
## Checkpoint
Update plan, append one concise `Log` entry. Preserve earlier `Log`/`Done`/`Deleted` history. Format:
```md
- HH:MM build: <summary>
- HH:MM edit: <reason>
- HH:MM delete: <reason>
```
**Checkpoint examples**: see [references/examples.md](references/examples.md). Load when writing checkpoint Log entries or validating empty-section handling.
If item mid-clarify at checkpoint: store next question in `Questions`; don't force into `Now`/`Later`.
