# Daily Assistant Conversation Workflow Design

Date: 2026-04-30

## Purpose

Daily Assistant shall begin as a gentle capture dialogue, not a schedule-first form.

Opening prompt:

```text
What's on your mind?
```

The answer may be a task, thought, idea, question, event, or maybe item. Codex must not force the user to pre-shape it as a concrete task.

## Current Problem

The current installed skill asks whether today's tasks need `Time Blocks` before any task is known. This is too early. Scheduling should happen after one captured item is clarified and placed.

The old flow:

```text
Ask Time Blocks -> Ask Task -> Place
```

The new flow:

```text
Capture -> Clarify -> Classify -> Place -> Optional Time Block
```

## Precedents

- Karpathy append-and-review note: capture raw ideas, todos, and thoughts first; review later to process, merge, or group them. <https://karpathy.bearblog.dev/the-append-and-review-note/>
- Steinberger agent workflow: start by discussing ideas and sources, then flesh out the plan with the agent. <https://steipete.me/posts/just-talk-to-it>
- GTD: capture what has attention, clarify what it means, then organize where it belongs. <https://gettingthingsdone.com/what-is-gtd/>
- Bullet Journal Rapid Logging: separate tasks, events, and notes; notes may be ideas, thoughts, or observations. <https://bulletjournal.com/blogs/faq/what-is-rapid-logging-understand-rapid-logging-bullets-and-signifiers>
- Org Mode Capture: quick capture first, refile after. <https://orgmode.org/org.html>
- Sunsama: add tasks and estimate workload before optional timeboxing. <https://help.sunsama.com/docs/usage-guides/daily-planning/> and <https://help.sunsama.com/docs/usage-guides/timeboxing/timeboxing-concepts-and-principles/>
- Akiflow: keep capture and execution distinct; process inbox items before Today. <https://product.akiflow.com/articles/3602719-the-methodology>
- Things: Inbox is for unprocessed thoughts; Today is for to-dos to start today; Someday is for ideas not yet planned. <https://culturedcode.com/things/support/articles/4001304/>
- Todoist GTD: clarify captured tasks into concrete next steps before organizing. <https://www.todoist.com/help/articles/getting-things-done-gtd-with-todoist-e5j2h3>

## Conversation Loop

1. Read today's plan if it exists; otherwise begin a new build intake.
2. Ask `What's on your mind?`
3. Treat the reply as a `captured item`.
4. Clarify the item with one smallest useful question.
5. Classify the item.
6. Place the item in `Focus`, `Now`, `Later`, or `Questions`.
7. Ask whether to time block only after the item is placed.
8. Checkpoint after every three user answers, or immediately if asked.
9. Ask `Anything else on your mind?`
10. If the user says no, close intake, set `Questions` to `- none`, and append a closing `Log` entry.

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

State the planned placement, then ask time block if useful.

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

- `task`: actionable; user can do, start, finish, contact, write, clean, decide, review.
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
- `Done` and `Deleted`: unchanged.

## Time Block Rule

Never ask a day-level scheduling question at intake start.

Ask per item after clarification:

```text
Time block this item? You can give a time, or say no/later.
```

If user gives a time range, add `Time Blocks` and infer `@appetite(...)` from the duration unless user gave another estimate. If user says no or later, do not ask again for that item.

## Checkpoint Rule

Each checkpoint writes the plan and appends one `Log` entry. Preserve prior `Log`, `Done`, and `Deleted` history.

Empty sections must use:

```md
- none
```

Never write a bare dash.

If an item is mid-clarification at checkpoint time, store the next question in `Questions` rather than forcing the item into `Now` or `Later`.

## Non-Goals

- No Google Calendar, Gmail, Drive, or Tasks integration.
- No external writes.
- No automatic scheduling.
- No new task database.
- No global day-level time-block decision.

## Acceptance Checks

- Opening question is exactly `What's on your mind?`.
- The skill accepts abstract thoughts without forcing task language.
- `Time Blocks` is asked per item after clarification, never before first capture.
- `later` or `no` ends scheduling for that item.
- The plan preserves existing log history.
- Empty sections use `- none`.
