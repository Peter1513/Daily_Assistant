# Daily Assistant Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the Daily Assistant planning ritual as Markdown-first protocol artifacts.

**Architecture:** Create a small protocol folder under `Daily_Assistant/` with one core `SKILL.md`, one daily-plan template, and one example plan. The skill defines the build/edit/delete ritual, the three-answer checkpoint rule, and the optional scheduling prompt; the template and example make the behavior easy to follow without adding a task database or Google integration.

**Tech Stack:** Markdown, Codex/Claude-compatible skill format, shell verification with `sed`, `rg`, and `find`.

---

## File Structure

- Create: `Daily_Assistant/SKILL.md`
  - Canonical protocol for the Daily Assistant ritual.
  - Contains YAML frontmatter with a short trigger-only `description`.
  - Defines one-question-at-a-time planning, three-answer checkpoints, small-change/direct-write rules, large-change/confirmation rules, and optional `Time Blocks`.
- Create: `Daily_Assistant/templates/daily-plan.md`
  - Reusable daily plan template with `Focus`, `Now`, `Later`, `Questions`, `Log`, `Done`, `Deleted`, and optional `Time Blocks`.
- Create: `Daily_Assistant/plans/README.md`
  - Short note explaining that daily plans are stored as `YYYY-MM-DD.md`.
  - States that each date has one source-of-truth plan file.
- Create: `Daily_Assistant/plans/2026-04-30.example.md`
  - Example daily plan showing the schema in use.
  - Uses fake tasks only.
- Modify: `README.md`
  - Update the `Daily_Assistant` pending entry to point at `Daily_Assistant/SKILL.md`, the template, and the design spec.
- Read-only verification: `docs/superpowers/specs/2026-04-30-daily-assistant-plan-design.md`
  - Confirm implementation matches the approved design.

## Task 1: Create Daily Assistant Skill Protocol

**Files:**
- Create: `Daily_Assistant/SKILL.md`
- Read: `docs/superpowers/specs/2026-04-30-daily-assistant-plan-design.md`

- [ ] **Step 1: Create the skill file**

Create `Daily_Assistant/SKILL.md` with this content:

````md
---
name: daily-assistant
description: Maintain a daily Markdown plan through one-question-at-a-time build, edit, and delete planning.
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
````

- [ ] **Step 2: Verify the skill file exists and has trigger-only frontmatter**

Run:

```bash
sed -n '1,40p' Daily_Assistant/SKILL.md
```

Expected:

```text
---
name: daily-assistant
description: Maintain a daily Markdown plan through one-question-at-a-time build, edit, and delete planning.
---
```

- [ ] **Step 3: Verify the skill does not include Google integration steps**

Run:

```bash
rg -n "Google Calendar|Gmail|Drive|Tasks|OAuth|MCP" Daily_Assistant/SKILL.md
```

Expected:

```text
Daily_Assistant/SKILL.md:8:Daily Assistant focuses on `build`, `edit`, and `delete` operations. It does not use Google Calendar, Gmail, Drive, or Tasks in the initial version.
```

## Task 2: Create Daily Plan Template

**Files:**
- Create: `Daily_Assistant/templates/daily-plan.md`

- [ ] **Step 1: Create the template directory**

Run:

```bash
mkdir -p Daily_Assistant/templates
```

Expected: command exits with status 0.

- [ ] **Step 2: Create the daily plan template**

Create `Daily_Assistant/templates/daily-plan.md` with this content:

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

- [ ] **Step 3: Verify required sections**

Run:

```bash
rg -n "^## (Focus|Now|Later|Questions|Log|Done|Deleted|Time Blocks)$" Daily_Assistant/templates/daily-plan.md
```

Expected:

```text
Daily_Assistant/templates/daily-plan.md:3:## Focus
Daily_Assistant/templates/daily-plan.md:6:## Now
Daily_Assistant/templates/daily-plan.md:9:## Later
Daily_Assistant/templates/daily-plan.md:12:## Questions
Daily_Assistant/templates/daily-plan.md:15:## Log
Daily_Assistant/templates/daily-plan.md:18:## Done
Daily_Assistant/templates/daily-plan.md:21:## Deleted
Daily_Assistant/templates/daily-plan.md:24:## Time Blocks
```

## Task 3: Create Plans Folder README and Example

**Files:**
- Create: `Daily_Assistant/plans/README.md`
- Create: `Daily_Assistant/plans/2026-04-30.example.md`

- [ ] **Step 1: Create the plans directory**

Run:

```bash
mkdir -p Daily_Assistant/plans
```

Expected: command exits with status 0.

- [ ] **Step 2: Create plans README**

Create `Daily_Assistant/plans/README.md` with this content:

```md
# Daily Plans

Daily plans live here as `YYYY-MM-DD.md`.

Each date has one source-of-truth plan file. Use `Daily_Assistant/templates/daily-plan.md` when starting a new day.

Example files may use `.example.md` and should not be treated as real plans.
```

- [ ] **Step 3: Create example plan**

Create `Daily_Assistant/plans/2026-04-30.example.md` with this content:

```md
# Plan 2026-04-30

## Focus
- [ ] Draft Daily Assistant protocol @appetite(90m) @status(active)

## Now
- [ ] Review approved design spec @appetite(25m) @status(active)
- [ ] Create daily plan template @appetite(25m) @status(next)

## Later
- [ ] Evaluate Google plugin adapters @appetite(half-day) @status(deferred)

## Questions
- Do these tasks need to be scheduled into time blocks?

## Log
- 09:00 build: created example plan from approved Daily Assistant design.
- 09:30 edit: deferred Google plugin adapters because the initial design focuses on planning flow.

## Done
- [x] Select maintainable daily plan schema

## Deleted
- Schedule-first default format - removed because time blocks are optional.

## Time Blocks
- only added when scheduling is requested
```

- [ ] **Step 4: Verify example has fake tasks only**

Run:

```bash
rg -n "real|private|email|calendar|credential|OAuth" Daily_Assistant/plans/2026-04-30.example.md
```

Expected: no matches.

## Task 4: Update Root README

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Replace the Daily_Assistant pending entry**

Change `README.md` to:

```md
## Pending
- Daily_Assistant: 以一問一答維護每日 Markdown plan，支援 build/edit/delete 與三輪 checkpoint
  - design: `docs/superpowers/specs/2026-04-30-daily-assistant-plan-design.md`
  - implementation plan: `docs/superpowers/plans/2026-04-30-daily-assistant-implementation-plan.md`
  - protocol: `Daily_Assistant/SKILL.md`
  - template: `Daily_Assistant/templates/daily-plan.md`
  - references: `Daily_Assistant/github-reference-repos.md`
```

- [ ] **Step 2: Verify README links**

Run:

```bash
sed -n '1,40p' README.md
```

Expected:

```text
## Pending
- Daily_Assistant: 以一問一答維護每日 Markdown plan，支援 build/edit/delete 與三輪 checkpoint
  - design: `docs/superpowers/specs/2026-04-30-daily-assistant-plan-design.md`
  - implementation plan: `docs/superpowers/plans/2026-04-30-daily-assistant-implementation-plan.md`
  - protocol: `Daily_Assistant/SKILL.md`
  - template: `Daily_Assistant/templates/daily-plan.md`
  - references: `Daily_Assistant/github-reference-repos.md`
```

## Task 5: Final Verification

**Files:**
- Read: `Daily_Assistant/SKILL.md`
- Read: `Daily_Assistant/templates/daily-plan.md`
- Read: `Daily_Assistant/plans/README.md`
- Read: `Daily_Assistant/plans/2026-04-30.example.md`
- Read: `README.md`

- [ ] **Step 1: List Daily Assistant files**

Run:

```bash
find Daily_Assistant -maxdepth 3 -type f | sort
```

Expected:

```text
Daily_Assistant/SKILL.md
Daily_Assistant/github-reference-repos.md
Daily_Assistant/plans/2026-04-30.example.md
Daily_Assistant/plans/README.md
Daily_Assistant/templates/daily-plan.md
```

- [ ] **Step 2: Check for unfinished placeholders**

Run:

```bash
rg -n "TB.?D|TO.?DO|FIXM?E|\\x3f\\x3f" Daily_Assistant README.md
```

Expected: no matches.

- [ ] **Step 3: Check for accidental write-capable integration scope**

Run:

```bash
rg -n "OAuth|token|credential|send email|create event|write to Google|MCP server" Daily_Assistant/SKILL.md Daily_Assistant/templates/daily-plan.md Daily_Assistant/plans/README.md Daily_Assistant/plans/2026-04-30.example.md
```

Expected: no matches.

- [ ] **Step 4: Read back all created and modified docs**

Run:

```bash
sed -n '1,220p' Daily_Assistant/SKILL.md
sed -n '1,120p' Daily_Assistant/templates/daily-plan.md
sed -n '1,80p' Daily_Assistant/plans/README.md
sed -n '1,160p' Daily_Assistant/plans/2026-04-30.example.md
sed -n '1,60p' README.md
```

Expected:

- `Daily_Assistant/SKILL.md` contains the ritual, small-change rules, large-change rules, and checkpoint format.
- `Daily_Assistant/templates/daily-plan.md` contains all eight plan sections.
- `Daily_Assistant/plans/README.md` states `YYYY-MM-DD.md` as the daily file convention.
- `Daily_Assistant/plans/2026-04-30.example.md` uses fake tasks only.
- `README.md` points to the design, implementation plan, protocol, template, and references.

- [ ] **Step 5: Check repository status**

Run:

```bash
git status --short
```

Expected:

```text
fatal: not a git repository (or any of the parent directories): .git
```

Because this workspace is not a git repo, do not commit. Report that no commit was made.
