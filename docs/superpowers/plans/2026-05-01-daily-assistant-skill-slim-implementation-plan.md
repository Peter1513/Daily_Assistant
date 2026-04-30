# Daily Assistant Skill Slim Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Refactor `Daily_Assistant/SKILL.md` (215 lines / 6.2 KB) into a caveman lite, structurally deduplicated body (~95 lines) plus an extracted `references/examples.md` (~30 lines), reducing per-invocation token cost ~70% while preserving every behavioral rule and verbatim user-facing prompt.

**Architecture:** Three sequenced commits on local branch `dev`: (1) commit the already-staged Runtime Verification Boundary section as baseline; (2) atomically extract example dialogues into `references/examples.md` and add Pattern 1 pointer lines; (3) caveman lite + structural dedup rewrite. Then push `dev`, reinstall the Codex copy via `--ref dev`, run package-aware payload diff, run live-trigger verification, and finally merge `dev → main`.

**Tech Stack:** Markdown, git, bash, Anthropic / OpenAI Codex skill loader (filesystem-based progressive disclosure).

**Spec:** `docs/superpowers/specs/2026-05-01-daily-assistant-skill-slim-design.md`

---

## File Structure

| File | Disposition |
|---|---|
| `SKILL.md` | Modified (Task 1: +6 lines RVB; Task 2: −16 lines examples + 2 pointer lines; Task 3: full rewrite ~95 lines) |
| `references/` | New dir (Task 2) |
| `references/examples.md` | New file ~30 lines (Task 2) |
| `templates/daily-plan.md` | Untouched |
| `agents/openai.yaml` | Untouched |
| `README.md`, `LICENSE`, `THIRD_PARTY_NOTICES.md`, `github-reference-repos.md`, `.gitignore` | Untouched |
| `plans/*` | Untouched (real daily plans are user data) |
| `docs/superpowers/specs/2026-05-01-daily-assistant-skill-slim-design.md` | Already committed (`6cd40d9`, `935a482`) |

Working directory for all tasks: `/home/peter/Project/Plan_Build/Daily_Assistant`. Branch: `dev` (local-only, HEAD `935a482` at plan creation).

---

## Task 1: Commit Runtime Verification Boundary as baseline

**Files:**
- Modify: `SKILL.md:24-28` (add 5-line RVB section + 1 surrounding blank line — already in working tree)

The +6 lines were already staged in the working tree before branching. Commit them as a clean baseline so the caveman rewrite in Task 3 builds on top.

- [ ] **Step 1: Verify current working tree state**

Run:
```bash
git status --short
```
Expected:
```
 M SKILL.md
```
Plus possibly the spec amendments already committed. No other dirty files.

- [ ] **Step 2: Verify the diff content matches the expected RVB section**

Run:
```bash
git diff SKILL.md
```
Expected diff body (verbatim):
```diff
@@ -21,6 +21,12 @@ Daily_Assistant/plans/YYYY-MM-DD.md
 
 This path is relative to the current workspace unless the user gives another root. Do not store real daily plans inside the installed skill folder. Each date has one source-of-truth plan file.
 
+## Runtime Verification Boundary
+
+For normal daily plan `build`, `edit`, and `delete` operations, verify only by reading the target plan file after writing. Do not run `git status`, `git diff`, `git log`, or other repo-state commands during daily intake.
+
+Use git only when maintaining the Daily Assistant skill/repo itself, or when the user explicitly asks for review, commit, publish, install-state, or repo-state verification.
+
 ## Opening Prompt
```

If diff differs, abort and investigate before continuing.

- [ ] **Step 3: Stage and commit**

Run:
```bash
git add SKILL.md
git commit -m "$(cat <<'EOF'
docs: add runtime verification boundary section

Document that daily build/edit/delete intake should verify only by
reading the target plan file, not by running git or repo-state commands.
Carve out an explicit exception for skill/repo maintenance and explicit
user requests so the rule does not block legitimate review work.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

- [ ] **Step 4: Verify clean working tree and updated HEAD**

Run:
```bash
git status --short && git log --oneline -3
```
Expected: no `M` lines; HEAD log shows the new commit on top of `935a482`.

---

## Task 2: Extract examples to `references/examples.md` (atomic)

**Files:**
- Create: `references/examples.md`
- Modify: `SKILL.md` (remove three example blocks at original lines 62-67, 94-98, 201-211; add two pointer lines)

This task is atomic — examples are removed from `SKILL.md` only when the new file is in place and pointers are added.

- [ ] **Step 1: Create `references/` directory**

Run:
```bash
mkdir -p references
ls -la references/
```
Expected: empty `references/` dir exists.

- [ ] **Step 2: Write `references/examples.md`**

Create file with this exact content:

```markdown
# Daily Assistant Examples

## Contents
- Reflect example
- Commit example
- Checkpoint examples

## Reflect example

Mirror the captured item briefly without over-processing:

> Got it: "整理房間".
> Sounds like a thought, not yet a task.

## Commit example

State the planned placement, then ask time block only if useful:

> I will place this in Now at 60m. Time block it?

> I will keep this in Later as a thought. Anything else on your mind?

## Checkpoint examples

Append one concise `Log` entry per checkpoint. Use one of these forms:

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
```

- [ ] **Step 3: Verify the new file**

Run:
```bash
wc -l references/examples.md
grep -c '^## ' references/examples.md
```
Expected: line count between 28 and 34; section count 5 (Contents, Reflect example, Commit example, Checkpoint examples, plus the H1 isn't `## `, so just count `## `: 4).

Run:
```bash
grep -n 'references/' references/examples.md
```
Expected: empty (references file does not link back to other references — one-level rule).

- [ ] **Step 4: Remove the three example blocks from `SKILL.md`**

Edit `SKILL.md` to delete:

a) Original Reflect example (the section under `### Reflect`):
```text
Examples:

```text
Got it: "整理房間".
Sounds like a thought, not yet a task.
```
```

b) Original Commit example (the section under `### Commit`):
```text
Examples:

```text
I will place this in Now at 60m. Time block it?
I will keep this in Later as a thought. Anything else on your mind?
```
```

c) Original Checkpoint examples block (the `Examples:` block under `## Checkpoint Format`, plus the `Empty sections must use:` block and `Never write a bare dash.` line):
```text
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
```

These three blocks are removed in this task; `SKILL.md` prose around them is left as-is for now (Task 3 rewrites it).

- [ ] **Step 5: Add Pattern 1 pointer lines to `SKILL.md`**

In the Clarify Engine section, after the `### Commit` subsection's surviving prose (the line `State the planned placement, then ask time block only if useful.`), insert one blank line then:

```markdown
**Examples**: see [references/examples.md](references/examples.md). Load when running Reflect or Commit moves.
```

In the Checkpoint Format section, after the surviving prose `At checkpoint, update the daily plan and add one concise `Log` entry. Preserve earlier `Log`, `Done`, and `Deleted` history.`, insert one blank line then:

```markdown
**Checkpoint examples**: see [references/examples.md](references/examples.md). Load when writing checkpoint Log entries or validating empty-section handling.
```

- [ ] **Step 6: Verify `SKILL.md` post-edit**

Run:
```bash
grep -n 'references/examples.md' SKILL.md
```
Expected: exactly two matches, one bold-tagged `**Examples**:`, one bold-tagged `**Checkpoint examples**:`. Both contain the literal `Load when ` phrase.

Run:
```bash
grep -n 'Got it: "整理房間"' SKILL.md
grep -n 'I will place this in Now at 60m' SKILL.md
grep -n 'HH:MM build: captured and placed' SKILL.md
```
Expected: all three return no matches (examples removed).

Run:
```bash
grep -c '^## ' SKILL.md
```
Expected: same number as before extraction (this task only removes example blocks within sections, not whole sections).

- [ ] **Step 7: Stage and commit**

Run:
```bash
git add references/examples.md SKILL.md
git commit -m "$(cat <<'EOF'
docs(skill): extract examples to references/

Move Reflect, Commit, and Checkpoint example blocks from SKILL.md into
references/examples.md and add Pattern 1 pointer lines (bold trigger
context plus "Load when ..." condition) so the on-demand load path is
explicit for both Claude Code and Codex.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

- [ ] **Step 8: Verify commit**

Run:
```bash
git log --oneline -3
git diff HEAD~1 --stat
```
Expected: top commit is `docs(skill): extract examples to references/`; `--stat` shows `SKILL.md` (negative net change ~14 lines) and `references/examples.md` (new, ~30 lines).

---

## Task 3: Rewrite `SKILL.md` in caveman lite + structural dedup

**Files:**
- Modify: `SKILL.md` (full rewrite, target ~95 lines)

This task replaces `SKILL.md` body with the caveman lite + dedup version per spec §SKILL.md body 構. Frontmatter, fixed user-facing prompts, and code blocks are preserved verbatim.

- [ ] **Step 1: Capture pre-rewrite line count for the diff record**

Run:
```bash
wc -l SKILL.md
```
Expected: ~201 lines (215 original + 6 RVB − 14 examples − 6 surrounding blanks + 2 pointer lines + 2 surrounding blanks ≈ 205, within tolerance).

- [ ] **Step 2: Overwrite `SKILL.md` with the caveman lite + dedup body**

Replace the entire file with this exact content:

````markdown
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

Four moves: `Reflect → Test → Shape → Commit`. One question at a time.

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

- actionable + today → ask appetite if missing.
- actionable + not today → place `Later`.
- vague but important → ask whether to make one next action.
- pure thought → place `Later` or `Questions`.
- decision/inquiry → place `Questions`, unless user wants action to answer it.
- fixed-time event → scheduled candidate.

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
````

- [ ] **Step 3: L1 structural verification — frontmatter verbatim**

Run:
```bash
diff <(head -4 SKILL.md) <(printf -- '---\nname: daily-assistant\ndescription: Use when planning a day or maintaining today'\''s Markdown plan through capture, clarification, placement, scheduling, editing, or deletion.\n---\n')
```
Expected: empty diff (frontmatter exact match).

- [ ] **Step 4: L1 structural verification — fixed prompts verbatim**

Run:
```bash
grep -F 'What'"'"'s on your mind?' SKILL.md
grep -F 'Anything else on your mind?' SKILL.md
grep -F 'Time block this item? You can give a time, or say no/later.' SKILL.md
```
Expected: each grep returns exactly one matching line.

- [ ] **Step 5: L1 structural verification — line count and references discipline**

Run:
```bash
wc -l SKILL.md references/examples.md
```
Expected: SKILL.md ≤ 100; references/examples.md ≤ 40.

Run:
```bash
grep -n 'references/' references/examples.md
```
Expected: empty (no cross-references between reference files).

Run:
```bash
grep -nE '\\\\' SKILL.md references/examples.md
```
Expected: empty (no Windows-style backslash paths).

- [ ] **Step 6: L2 behavior preservation — fixed prompts and ordering**

Run:
```bash
grep -c 'Is it for today?' SKILL.md
grep -c 'Is there a next action?' SKILL.md
grep -c 'How much time does it need?' SKILL.md
grep -c 'Does it need a time block?' SKILL.md
grep -c "don't force task shape" SKILL.md
```
Expected: each returns 1.

- [ ] **Step 7: L2 behavior preservation — Classify, Place, Shape coverage**

Run:
```bash
grep -cE '`(task|question|thought|event|maybe)`' SKILL.md
grep -cE '`(Focus|Now|Later|Questions|Time Blocks|Done|Deleted)`' SKILL.md
grep -c 'actionable + today' SKILL.md
grep -c 'fixed-time event' SKILL.md
```
Expected: classify list has all 5 backtick-wrapped types present (count ≥ 5); place list has all 7 placement names present (count ≥ 7); Shape rules retain `actionable + today` and `fixed-time event` lines.

- [ ] **Step 8: L2 behavior preservation — Changes table and deletion rule**

Run:
```bash
grep -c '| Small ' SKILL.md
grep -c '| Large ' SKILL.md
grep -F 'append to `Deleted` with short reason' SKILL.md
grep -F "set `Focus` to `- none`" SKILL.md
```
Expected: 1, 1, 1, 1.

- [ ] **Step 9: L2 behavior preservation — RVB and `- none` rules**

Run:
```bash
grep -F "Don't run \`git status\`/\`diff\`/\`log\` during intake" SKILL.md
grep -F "Don't write a bare dash" SKILL.md
grep -F "store next question in \`Questions\`" SKILL.md
```
Expected: 1, 1, 1.

- [ ] **Step 10: L2 behavior preservation — pointer Pattern 1 compliance**

Run:
```bash
grep -nE '\*\*[A-Z][^*]*\*\*: see \[references/' SKILL.md
grep -c 'Load when' SKILL.md
```
Expected: 2 lines matching the Pattern 1 prefix; `Load when` count is 2.

- [ ] **Step 11: Stage and commit**

Run:
```bash
git add SKILL.md
git commit -m "$(cat <<'EOF'
docs(skill): rewrite SKILL.md in caveman lite + structural dedup

Compress prose to caveman lite (drop articles, fragments OK, short
synonyms) while keeping frontmatter, fixed user-facing prompts, and code
blocks verbatim. Merge Clarify Priority into the Test step of Clarify
Engine, replace the 31-line inline daily plan schema with a one-line
reference to templates/daily-plan.md, fold Small and Large Changes into
a single three-column table, and rephrase ALL CAPS imperatives as
reasoning sentences where the reason is real. SKILL.md drops from ~205
to ~95 lines, cutting per-invocation token cost roughly 70%.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```

- [ ] **Step 12: Verify commit**

Run:
```bash
git log --oneline -4
git diff HEAD~1 --stat
```
Expected: top commit is `docs(skill): rewrite SKILL.md in caveman lite + structural dedup`; `--stat` shows large negative line count on `SKILL.md`.

---

## Task 4: Push `dev` to `origin/dev`

**Files:** none (git remote operation)

`origin/dev` does not exist yet (verified at plan time: `git rev-parse origin/dev` failed with `unknown revision`). Push so the GitHub-based skill installer can fetch from it.

- [ ] **Step 1: Confirm remote and current branch**

Run:
```bash
git remote -v
git branch --show-current
```
Expected: `origin git@github.com:Peter1513/Daily_Assistant.git` for both fetch/push; current branch `dev`.

- [ ] **Step 2: Push `dev` with upstream tracking**

Run:
```bash
git push -u origin dev
```
Expected: remote new branch creation message; local `dev` now tracks `origin/dev`.

- [ ] **Step 3: Verify**

Run:
```bash
git rev-parse origin/dev
git log --oneline origin/dev -5
```
Expected: SHA matches local `dev` HEAD; top three commits are Tasks 1–3 plus the prior spec commits.

---

## Task 5: Reinstall Codex skill copy from `dev`

**Files:**
- Replace: `~/.codex/skills/daily-assistant/` (whole dir)

The installer aborts when the destination directory exists, so remove the old copy first.

- [ ] **Step 1: Confirm the existing install copy is the old one**

Run:
```bash
ls /home/peter/.codex/skills/daily-assistant/
[ -d /home/peter/.codex/skills/daily-assistant/references ] && echo "has references" || echo "no references"
```
Expected: directory listing matches the pre-rewrite layout (no `references/` dir).

- [ ] **Step 2: Remove the old install copy**

Run:
```bash
rm -rf /home/peter/.codex/skills/daily-assistant/
```
Expected: no output. Confirm with `ls /home/peter/.codex/skills/daily-assistant/ 2>&1` returning a "No such file" error.

- [ ] **Step 3: Install from `dev` ref**

Run:
```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo Peter1513/Daily_Assistant \
  --path . \
  --name daily-assistant \
  --ref dev
```
Expected: success message; new dir at `/home/peter/.codex/skills/daily-assistant/` with `SKILL.md`, `references/`, `templates/`, `agents/`, plans/, README.md, etc.

- [ ] **Step 4: Restart Codex (manual user step)**

The installer's official guidance is "Restart Codex to pick up new skills." This is a manual step the user (or Codex orchestrator) performs in the Codex CLI. Mark this checkbox after restart confirmed.

- [ ] **Step 5: Run package-aware payload diff**

Run:
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
Expected: empty output (no `DIFF:` lines, no `Only in` lines from `diff -rq`).

If the payload diff fails: re-check Task 4 push success, re-run Task 5 from Step 2. Don't proceed to Task 6 until empty.

---

## Task 6: L3 live-trigger verification

**Files:** none (interactive testing)

Run three cases on each of Claude Code and Codex sides. Each case has output assertions (must pass) and a diagnostic signal (informational).

- [ ] **Step 1: Claude Code — Build case**

In a fresh Claude Code session with `cwd=/home/peter/Project/Plan_Build/Daily_Assistant`, invoke the `daily-assistant` skill and conduct a three-item intake.

Output assertions (must all hold):
1. Agent's first utterance is exactly `What's on your mind?`.
2. Agent does not ask a day-level Time Blocks question before any item is placed.
3. Each captured item is classified as one of `task`/`question`/`thought`/`event`/`maybe`.
4. Each captured item is placed in one of `Focus`/`Now`/`Later`/`Questions`.
5. After the third user reply, agent triggers a checkpoint that appends a `- HH:MM build: ...` line to `Log` and writes `Daily_Assistant/plans/2026-05-01.md`.
6. Agent ends with `Anything else on your mind?` when intake closes.

Diagnostic signal: agent's tool-call transcript shows a Read on `references/examples.md` during a Reflect or Commit move at least once.

- [ ] **Step 2: Claude Code — Edit case**

Continue the same session (or open the existing daily plan). Ask the agent to move one `Now` item to `Later`.

Output assertions:
1. Agent moves the item from `Now` to `Later` without confirmation prompt (small change rule).
2. Agent appends `- HH:MM edit: <reason>` to `Log`.
3. Existing `Log`/`Done`/`Deleted` history is preserved (no truncation).

- [ ] **Step 3: Claude Code — Delete case**

Ask the agent to delete one item.

Output assertions:
1. Agent prompts for explicit confirmation before deleting (large change rule).
2. After confirmation, item is removed from `Focus`/`Now`/`Later`/`Questions`/`Time Blocks` wherever present.
3. Item is appended to `Deleted` with a short reason.
4. If the deleted item was in `Focus` and no other active task remains, `Focus` becomes `- none`.
5. Agent appends `- HH:MM delete: <reason>` to `Log`.

- [ ] **Step 4: Codex — same three cases**

In a Codex CLI session (after the restart in Task 5 Step 4), invoke `daily-assistant` and run Build, Edit, Delete cases against the same expected outcomes as Steps 1–3. Each case must pass the same output assertions on the Codex side.

- [ ] **Step 5: Failure handling**

If any output assertion fails: patch `SKILL.md` (or `references/examples.md` if the issue is example phrasing), commit the patch on `dev`, push, redo Task 5 from Step 2, rerun the failing case.

If only the diagnostic signal fails (output assertions all pass but agent never reads `references/examples.md`): strengthen the pointer line in `SKILL.md` (e.g., add a leading `IMPORTANT: read this file before proceeding` clause), commit, push, redo Task 5 Step 2, rerun the diagnostic check on the affected case.

---

## Task 7: Merge `dev → main` and push

**Files:** none (git ref operation)

Only run this task after Task 6 passes on both Claude Code and Codex sides.

- [ ] **Step 1: Confirm Task 6 passed**

Visually re-confirm all output assertions in Task 6 are checked off. If any case is still red, do not proceed.

- [ ] **Step 2: Switch to main and update from origin**

Run:
```bash
git checkout main
git pull --ff-only origin main
```
Expected: `main` fast-forwards or is already up to date with `origin/main` (`bd1dcc5` at plan creation time).

- [ ] **Step 3: Merge `dev` into `main` with a merge commit**

Run:
```bash
git merge --no-ff dev -m "$(cat <<'EOF'
Merge branch 'dev' into main

Slim SKILL.md via caveman lite + structural dedup, extract examples to
references/examples.md, document the runtime verification boundary, and
record the spec and implementation plan for the change.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
EOF
)"
```
Expected: merge commit created on `main`.

- [ ] **Step 4: Push `main`**

Run:
```bash
git push origin main
```
Expected: `origin/main` advances to the new merge commit.

- [ ] **Step 5: Verify reinstall via README's default command works**

Run (this is the user-facing reinstall path that defaults to `--ref main`):
```bash
rm -rf /home/peter/.codex/skills/daily-assistant/
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo Peter1513/Daily_Assistant \
  --path . \
  --name daily-assistant
ls /home/peter/.codex/skills/daily-assistant/references/examples.md
```
Expected: install succeeds; the `references/examples.md` path exists in the install copy. Restart Codex once more so the canonical-install path is what's running.

---

## Self-Review

**1. Spec coverage:**

- §旨 (token reduction goal) → Tasks 2 + 3.
- §範 (scope: SKILL.md only + references/examples.md; frontmatter unchanged) → Task 3 Step 3 verifies frontmatter; Task 1 (RVB), Task 2 (extraction), Task 3 (rewrite).
- §約 (behavior equivalence; caveman lite; references one-level) → Task 3 Steps 6–10 verify each rule; Task 2 Step 3 verifies one-level.
- §檔局 → Task 2 (creates `references/examples.md`).
- §SKILL.md body 構 → Task 3 Step 2 contains the literal new body matching this section.
- §references/examples.md 構 → Task 2 Step 2 contains the literal new file.
- §Pointer 句式 → Task 2 Step 5 + Task 3 Step 10 enforce Pattern 1.
- §Git 工作流 (3 commits) → Tasks 1, 2, 3.
- §Codex Sync (--ref dev verification, then dev→main) → Tasks 4, 5, 7.
- §驗 L1 → Task 3 Steps 3–5.
- §驗 L2 → Task 3 Steps 6–10.
- §驗 L3 → Task 6.
- §風險 R1 → Task 6 Step 5 (pointer strength upgrade on diagnostic fail).
- §風險 R2 → Tasks 4, 5.
- §風險 R3 → Task 6 catches via output assertions on Codex side too.
- §風險 R4 → Task 1.

No spec section without a task.

**2. Placeholder scan:**

- No `TBD`, `TODO`, `implement later`, or `add appropriate error handling` strings in the plan.
- Every code block contains the actual content the engineer needs.
- The two manual user steps (Task 5 Step 4 Codex restart; Task 6 interactive sessions) are explicitly labeled as manual, not deferred.

**3. Type / signature consistency:**

- Pointer string format `**[Trigger]**: see [references/examples.md](references/examples.md). Load when ...` is used identically in Task 2 Step 5, Task 3 Step 2, and Task 3 Step 10 grep.
- Branch name `dev` is consistent across Tasks 1–7.
- Install command `--ref dev` (Task 5) vs default `--ref main` after merge (Task 7 Step 5) is intentional and matches the spec's two-stage sync strategy.
- File paths `references/examples.md` and `templates/daily-plan.md` are the only intra-skill references and are spelled identically every time.
