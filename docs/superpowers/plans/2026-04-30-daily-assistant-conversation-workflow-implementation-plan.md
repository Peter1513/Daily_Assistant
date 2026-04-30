# Daily Assistant Conversation Workflow Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Update Daily Assistant so intake starts with open capture, clarifies one item at a time, places the item, and only then asks an optional per-item time-block question.

**Architecture:** Keep `SKILL.md` as the canonical protocol. Make a docs-only protocol update inside `/home/peter/Project/Plan_Build/Daily_Assistant`: revise the canonical skill first, then align the template, example plan, and README. Do not modify the currently installed Codex skill copy; it will be reinstalled from GitHub after the repo source is pushed.

**Tech Stack:** Markdown, Codex skill frontmatter, shell verification with `sed`, `rg`, `diff`, and `git`.

---

## Working Directory

Run all repo commands from:

```bash
cd /home/peter/Project/Plan_Build/Daily_Assistant
```

Only modify files under `/home/peter/Project/Plan_Build/Daily_Assistant`. Do not edit `/home/peter/.codex/skills/daily-assistant/SKILL.md` in this plan.

## File Map

- Modify: `SKILL.md`
  - Canonical Daily Assistant protocol.
  - Must define exact opening prompt, capture-first workflow, clarify engine, placement rules, per-item time-block rule, checkpoint rule, and empty-section format.
- Modify: `templates/daily-plan.md`
  - Reusable empty daily plan template.
  - Must use `- none` for empty sections.
- Modify: `plans/2026-04-30.example.md`
  - Example plan showing the new capture-first behavior.
  - Must not contain the old day-level scheduling question.
- Modify: `README.md`
  - User-facing summary, file list, install/update notes, and verification commands.
- Read only: `docs/superpowers/specs/2026-04-30-daily-assistant-conversation-workflow-design.md`
  - Approved source for this workflow update.
- Read only: `agents/openai.yaml`
  - No planned change. Current `default_prompt` may remain `Use $daily-assistant to plan today.` because the exact opening prompt belongs in `SKILL.md`.
- Modify: `docs/superpowers/plans/2026-04-30-daily-assistant-conversation-workflow-implementation-plan.md`
  - Keep this implementation plan aligned with the repo-only source workflow and final GitHub push.
- Push target: `origin` on branch `main`
  - Remote is expected to be `git@github.com:Peter1513/Daily_Assistant.git`.

## Evidence Ledger

- Approved spec requires opening prompt exactly `What's on your mind?`, then `Capture -> Clarify -> Classify -> Place -> Optional Time Block`.
- Approved spec defines Clarify as `Reflect -> Test -> Shape -> Commit`.
- Approved spec says never ask a day-level scheduling question at intake start.
- Current repo `SKILL.md` still says: `At build time and checkpoint time, ask: "Do you need these tasks scheduled into time blocks?"`
- Current `templates/daily-plan.md` and `plans/2026-04-30.example.md` contain non-empty placeholder lines and the old scheduling-question pattern.
- `Daily_Assistant` is its own git repo. Use `git -C /home/peter/Project/Plan_Build/Daily_Assistant ...` only if running from outside the repo root.
- The installed Codex skill is intentionally out of scope. Reinstall it from GitHub after the source repo is pushed.

## Task 1: Update Canonical Skill Protocol

**Files:**
- Modify: `SKILL.md`
- Read: `docs/superpowers/specs/2026-04-30-daily-assistant-conversation-workflow-design.md`

- [x] **Step 1: Read the approved spec and current skill**

Run:

```bash
sed -n '1,220p' docs/superpowers/specs/2026-04-30-daily-assistant-conversation-workflow-design.md
sed -n '1,180p' SKILL.md
```

Expected:

- The spec includes `What's on your mind?`.
- The current skill still includes a build-time or checkpoint-time scheduling question.

- [x] **Step 2: Replace `SKILL.md` with the new canonical protocol**

Replace `SKILL.md` with this complete content:

````md
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
````

- [x] **Step 3: Verify the new canonical opening and flow**

Run:

```bash
rg -n "What's on your mind\\?|Capture|Clarify|Classify|Place|Optional Time Block|Reflect -> Test -> Shape -> Commit|Time block this item\\? You can give a time, or say no/later\\." SKILL.md
```

Expected:

- Matches for `What's on your mind?`.
- Matches for the capture/clarify/classify/place/time-block concepts.
- Match for `Reflect -> Test -> Shape -> Commit`.
- Match for the exact per-item time-block prompt.

- [x] **Step 4: Verify the old day-level scheduling prompt is gone**

Run:

```bash
rg -n "Do you need these tasks scheduled into time blocks|At build time, ask once|At build time and checkpoint time" SKILL.md
```

Expected: no output and exit status 1.

## Task 2: Update Template and Example Plan

**Files:**
- Modify: `templates/daily-plan.md`
- Modify: `plans/2026-04-30.example.md`

- [x] **Step 1: Replace the reusable template with an empty `- none` template**

Replace `templates/daily-plan.md` with this complete content:

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

- [x] **Step 2: Replace the example plan with capture-first examples**

Replace `plans/2026-04-30.example.md` with this complete content:

```md
# Plan 2026-04-30

## Focus
- [ ] Update Daily Assistant conversation workflow @appetite(90m) @status(active)

## Now
- [ ] Read approved conversation workflow spec @appetite(25m) @status(done)
- [ ] Update canonical skill protocol @appetite(45m) @status(active)

## Later
- Idea: keep future Google adapters outside the initial Markdown-first workflow.

## Questions
- Should the GitHub source be pushed before reinstalling the skill?

## Log
- 09:00 build: captured workflow update intent from approved spec.
- 09:20 build: placed implementation work in Now and kept adapter idea in Later.

## Done
- [x] Preserve three-answer checkpoint rule.

## Deleted
- none

## Time Blocks
- none
```

- [x] **Step 3: Verify section headings and empty-section format**

Run:

```bash
rg -n "^## (Focus|Now|Later|Questions|Log|Done|Deleted|Time Blocks)$" templates/daily-plan.md plans/2026-04-30.example.md
rg -n "^- $" templates/daily-plan.md plans/2026-04-30.example.md
rg -n "Do you need these tasks scheduled into time blocks|only added when scheduling is requested" templates/daily-plan.md plans/2026-04-30.example.md
```

Expected:

- First command shows all eight sections in both files.
- Second command has no output and exit status 1.
- Third command has no output and exit status 1.

## Task 3: Update README

**Files:**
- Modify: `README.md`
- Read: `docs/superpowers/specs/2026-04-30-daily-assistant-conversation-workflow-design.md`

- [x] **Step 1: Update the top summary**

Replace the first two paragraphs under `# Daily Assistant` with:

```md
Daily Assistant is a Markdown-first planning ritual for maintaining one daily plan through gentle capture and one-question-at-a-time clarification.

It starts with `What's on your mind?`, accepts tasks, thoughts, ideas, questions, events, and maybe items, then uses `Capture -> Clarify -> Classify -> Place -> Optional Time Block`. It checkpoints after every three user answers and asks about `Time Blocks` only per item after clarification and placement.
```

- [x] **Step 2: Keep install/update commands but clarify the source of truth**

Under `## Use`, before `### Install for Codex`, add:

```md
`SKILL.md` is the canonical protocol. The installed Codex skill at `~/.codex/skills/daily-assistant/SKILL.md` is a local copy and may drift until reinstalled from GitHub.
```

- [x] **Step 3: Replace update wording with GitHub reinstall wording**

Ensure the `### Update` section says to reinstall from GitHub after source changes are pushed:

````md
The installer refuses to overwrite an existing skill. After pushing source updates to GitHub, reinstall from GitHub:

```bash
rm -rf ~/.codex/skills/daily-assistant
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo Peter1513/Daily_Assistant \
  --path . \
  --name daily-assistant
```

Restart Codex after reinstalling so the loaded skill text is refreshed.
````

- [x] **Step 4: Update the files list**

Ensure `## Files` contains these bullets:

```md
- `SKILL.md` - canonical Daily Assistant protocol.
- `templates/daily-plan.md` - reusable daily plan template.
- `plans/README.md` - daily plan storage convention.
- `plans/2026-04-30.example.md` - fake example plan.
- `docs/superpowers/specs/2026-04-30-daily-assistant-plan-design.md` - original approved design spec.
- `docs/superpowers/specs/2026-04-30-daily-assistant-conversation-workflow-design.md` - approved conversation workflow update spec.
- `docs/superpowers/plans/2026-04-30-daily-assistant-implementation-plan.md` - original implementation plan used to build this artifact.
- `docs/superpowers/plans/2026-04-30-daily-assistant-conversation-workflow-implementation-plan.md` - implementation plan for the conversation workflow update.
```

- [x] **Step 5: Replace README verification commands**

Replace the `## Verification` command block with:

````md
```bash
sed -n '1,220p' SKILL.md
sed -n '1,120p' templates/daily-plan.md
sed -n '1,160p' plans/2026-04-30.example.md
rg -n "What's on your mind\\?|Reflect -> Test -> Shape -> Commit|Time block this item\\? You can give a time, or say no/later\\.|- none" SKILL.md templates/daily-plan.md plans/2026-04-30.example.md
rg -n "^## (Focus|Now|Later|Questions|Log|Done|Deleted|Time Blocks)$" templates/daily-plan.md plans/2026-04-30.example.md
rg -n "Do you need these tasks scheduled into time blocks|At build time, ask once|At build time and checkpoint time|^- $" SKILL.md templates/daily-plan.md plans/2026-04-30.example.md
rg -n "OAuth|token|credential|send email|create event|write to Google|MCP server" SKILL.md templates/daily-plan.md plans/README.md plans/2026-04-30.example.md
```
````

Expected:

- The first two `rg` checks find required workflow text and all section headings.
- The third `rg` check has no output and exit status 1.
- The fourth `rg` check has no output and exit status 1.

## Task 4: Final Docs-Only Verification

**Files:**
- Read: `SKILL.md`
- Read: `templates/daily-plan.md`
- Read: `plans/2026-04-30.example.md`
- Read: `README.md`

- [x] **Step 1: Read changed docs back**

Run:

```bash
sed -n '1,260p' SKILL.md
sed -n '1,120p' templates/daily-plan.md
sed -n '1,180p' plans/2026-04-30.example.md
sed -n '1,180p' README.md
```

Expected:

- `SKILL.md` contains the exact opening prompt.
- `SKILL.md` contains `Reflect -> Test -> Shape -> Commit`.
- Template empty sections use `- none`.
- Example has no old day-level scheduling question.
- README says `SKILL.md` is canonical and update happens by reinstalling from GitHub.

- [x] **Step 2: Run positive workflow checks**

Run:

```bash
rg -n "What's on your mind\\?|Capture|Clarify|Classify|Place|Reflect -> Test -> Shape -> Commit|Time block this item\\? You can give a time, or say no/later\\.|- none" SKILL.md README.md templates/daily-plan.md plans/2026-04-30.example.md
```

Expected:

- Matches appear for the exact opening prompt, clarify engine, per-item time-block prompt, and `- none`.

- [x] **Step 3: Run negative workflow checks**

Run:

```bash
rg -n "Do you need these tasks scheduled into time blocks|At build time, ask once|At build time and checkpoint time|^- $" SKILL.md templates/daily-plan.md plans/2026-04-30.example.md
```

Expected: no output and exit status 1.

- [x] **Step 4: Run section and integration scope checks**

Run:

```bash
rg -n "^## (Focus|Now|Later|Questions|Log|Done|Deleted|Time Blocks)$" templates/daily-plan.md plans/2026-04-30.example.md
rg -n "OAuth|token|credential|send email|create event|write to Google|MCP server" SKILL.md templates/daily-plan.md plans/README.md plans/2026-04-30.example.md
```

Expected:

- First command shows all eight daily-plan sections in both files.
- Second command has no output and exit status 1.

- [x] **Step 5: Check git diff and status before commit**

Run:

```bash
git diff -- SKILL.md templates/daily-plan.md plans/2026-04-30.example.md README.md docs/superpowers/plans/2026-04-30-daily-assistant-conversation-workflow-implementation-plan.md
git status --short
```

Expected:

- Diff shows tracked docs/protocol changes listed in this plan.
- Status shows modified docs and the untracked implementation plan file before staging.
- Do not claim tests pass. This is docs-only verification.

## Task 5: Commit and Push Source Repo

**Files:**
- Commit: `SKILL.md`
- Commit: `templates/daily-plan.md`
- Commit: `plans/2026-04-30.example.md`
- Commit: `README.md`
- Commit: `docs/superpowers/plans/2026-04-30-daily-assistant-conversation-workflow-implementation-plan.md`

- [x] **Step 1: Confirm remote and branch**

Run:

```bash
git remote -v
git branch --show-current
```

Expected:

- `origin` points to `git@github.com:Peter1513/Daily_Assistant.git`.
- Current branch is `main`.

- [x] **Step 2: Stage only source repo files**

Run:

```bash
git add SKILL.md templates/daily-plan.md plans/2026-04-30.example.md README.md docs/superpowers/plans/2026-04-30-daily-assistant-conversation-workflow-implementation-plan.md
git status --short
```

Expected:

- Only the five listed repo files are staged.
- No file under `/home/peter/.codex/skills` appears.

- [ ] **Step 3: Commit**

Run:

```bash
git commit -m "docs: update daily assistant conversation workflow"
```

Expected: commit succeeds.

- [ ] **Step 4: Push to origin**

Run:

```bash
git push origin main
```

Expected: push succeeds to `git@github.com:Peter1513/Daily_Assistant.git`.

If network or SSH credentials block the push, report the exact blocker and leave the committed repo ready for the user to push.

- [ ] **Step 5: Verify clean post-push state**

Run:

```bash
git status --short
git log -1 --oneline
```

Expected:

- `git status --short` has no output.
- `git log -1 --oneline` shows `docs: update daily assistant conversation workflow`.

## Self-Review Checklist For Implementer

- [ ] The first intake prompt in `SKILL.md` is exactly `What's on your mind?`.
- [ ] No day-level `Time Blocks` question is asked before capture.
- [ ] Clarify is explicitly `Reflect -> Test -> Shape -> Commit`.
- [ ] Abstract thoughts, ideas, questions, events, and maybe items are accepted.
- [ ] `Time Blocks` is asked per item only after clarification and placement.
- [ ] Checkpoints still happen after every three user answers or on immediate write request.
- [ ] Existing `Log`, `Done`, and `Deleted` history is preserved.
- [ ] Empty sections use `- none`; no bare dash is present.
- [ ] No installed skill file under `/home/peter/.codex/skills` is modified.
- [ ] Source repo changes are committed and pushed to `origin main`.
