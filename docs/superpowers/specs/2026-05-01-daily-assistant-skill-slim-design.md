# Daily Assistant Skill Slim Design

Date: 2026-05-01

## 旨

`SKILL.md` 215 行 / 6.2 KB，召必全載，token 過耗。本 spec 規一改，行 caveman lite 重寫 + 結構去冗 + example 抽至 `references/`，採 Anthropic progressive disclosure named pattern，期 SKILL.md body 落於 ~95 行，每召載量降約 70%，並守原語意。

## 範

- **改動**：限於 `Daily_Assistant/` 內 `SKILL.md` 與新增 `references/examples.md`。
- **不動**：`templates/daily-plan.md`、`agents/openai.yaml`、`README.md`、`LICENSE`、`THIRD_PARTY_NOTICES.md`、`github-reference-repos.md`、`docs/` 既存檔、`plans/` 既存檔。
- **frontmatter `description`**：一字不改（trigger matcher 仰之）。
- **fixed user-facing prompts**：verbatim 保（agent 實際輸出依此）。
- **examples**：可壓 / 可抽。

## 約

1. 行為等價：原 SKILL.md 之**規定**皆現於新檔，語體可改、語意不可失。
2. caveman lite：drop articles, fragments OK, 短同義詞，technical name / fixed prompts / code blocks / YAML 不動。
3. references one-level：`SKILL.md → references/<file>.md`，references 互不引。

## 檔局

```text
Daily_Assistant/
├── SKILL.md                  # ~95 行，caveman lite，主流程
├── templates/
│   └── daily-plan.md         # 不動
└── references/               # 新增 dir
    └── examples.md           # ~30 行，正英文，例對話集
```

## SKILL.md body 構

含段（依序）：

1. frontmatter（verbatim）
2. Intro（lite 壓，去 description 已言之冗）
3. Plan Location（lite）
4. Runtime Verification（lite，含現 dev 枝 uncommitted 之 RVB +6 行）
5. Opening（fixed prompt verbatim：`What's on your mind?`）
6. Conversation Workflow（7-step list，lite）
7. Clarify Engine（四 moves；Clarify Priority 併入 Test 步；examples → references）
8. Classify Rules（lite，5 類）
9. Place Rules（lite，6 placement）
10. Time Block（fixed prompt verbatim；戒律 dedup）
11. Sections Schema（一行引 `templates/daily-plan.md`）
12. Changes（Small/Large 合一表）
13. Checkpoint（format 例 verbatim；multi-line examples → references）

刪併要點：

- Clarify Engine + Clarify Priority 併：Priority 5 ordering 入 Test 之 question 排序註腳。
- Time Block Rule restate（原 L127-137）合於 Opening 之 reasoning + Conversation Workflow step 5。戒律言一次。
- Daily Plan Sections 31 行 inline schema → 一行引 `templates/daily-plan.md`。
- Small / Large Changes 兩 list → 一表三欄（Type / Examples / Action）。
- Reflect / Commit / Checkpoint examples → `references/examples.md`。
- ALL CAPS 戒律（如「Never write a bare dash」）改 reasoning 句（如「Don't write a bare dash; reason: empty section must remain visibly populated to avoid confusion with truncation or in-progress edits」），依 Anthropic best practice。reasoning 須據實，無真因者保 imperative，不杜撰。

## references/examples.md 構

```text
# Daily Assistant Examples

## Contents
- Reflect example
- Commit example
- Checkpoint examples

## Reflect example
<原 L62-67>

## Commit example
<原 L94-98>

## Checkpoint examples
<原 L201-211，含 "- none" 戒律 example block>
```

正英文，非 caveman。含 TOC，便 Claude scan / grep。

## Pointer 句式

SKILL.md 內 references pointer 採 Anthropic Pattern 1：

```md
**[Trigger context]**: see [references/<file>.md](references/<file>.md). Load when [condition].
```

實 SKILL.md 內兩處：

1. Clarify Engine 末：`**Examples**: see [references/examples.md](references/examples.md). Load when running Reflect or Commit moves.`
2. Checkpoint 末：`**Checkpoint examples**: see [references/examples.md](references/examples.md). Load when writing checkpoint Log entries or validating empty-section handling.`

## Git 工作流

`dev` 枝（已立於 `main` 自 `bd1dcc5`），分三 commit：

| # | Commit message | 內容 |
|---|---|---|
| 1 | `docs: add runtime verification boundary section` | dev 枝 working tree 之 +6 行 RVB section commit 為 baseline。 |
| 2 | `docs(skill): extract examples to references/` | 創 `references/examples.md`，從 SKILL.md 移 Reflect / Commit / Checkpoint examples。SKILL.md 加 pointer 句，餘段未動。 |
| 3 | `docs(skill): rewrite SKILL.md in caveman lite + structural dedup` | caveman lite + Clarify Priority 併入 Engine + Small/Large 合表 + template inline 改引 + 戒律 dedup + ALL CAPS 改 reasoning。 |

## Codex Sync

bare `diff -r` 不可用：source 含 `.git/` + 真實 daily plans（`plans/2026-04-30.md`、`plans/2026-05-01.md` 等），codex install copy 皆無。bare diff 必假失敗。

定 **skill payload**（須兩端一致之 artifact 集）：

```text
SKILL.md
references/                          # 整 dir
templates/                           # 整 dir
agents/                              # 整 dir
README.md
LICENSE
THIRD_PARTY_NOTICES.md
github-reference-repos.md
.gitignore
plans/README.md
plans/2026-04-30.example.md          # 唯一隨 skill 出之 example plan
```

**不**屬 payload：`.git/`、`docs/`（dev-only 設計文件）、`plans/<real-day>.md`（user data，僅留於 source）。

實裝計畫末含 sync verification：

1. **Payload diff**（package-aware，scope 至上列檔／dir）：

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

   改 source 後此 diff 應顯所有有實質改動之檔。
2. 依 `README.md` 載之 install procedure 行同步（Plan 階段先 read README 取確切命令）。
3. 重啟 Codex；召 daily-assistant skill 試 dry-run。
4. 同 payload diff 重跑，預期空輸出。

## 驗

L1 — 結構：

- frontmatter 4 行 verbatim。
- `description` exact match。
- references 互不引（grep）。
- `wc -l SKILL.md references/examples.md`：≤ 100、≤ 40。
- 無 Windows path（無 `\\`）。

L2 — 行為等價（spot-check）：

- Opening / Closing / Time Block fixed prompts verbatim。
- Clarify priority 5 ordering 完整。
- Classify 5 類完整。
- Place 6 規完整。
- Shape 6 case mapping 完整。
- Small 6 + Large 6 條完整（含 deletion-merge 戒律）。
- Checkpoint mid-clarify → Questions 戒律保。
- Empty section `- none` 戒律保。
- **Runtime Verification Boundary 戒律保**：daily intake（build / edit / delete）不跑 `git status`/`diff`/`log` 或他 repo-state command；git 用法限 skill/repo 維護或 user 明求。

L3 — Live trigger（**output assertions**，可觀測）：

對每 case 行二類驗：

- **Output assertion**（必過，pass/fail criteria）：
- **Diagnostic signal**（不過不退，僅警示 risk 1）：

Build case：

- Output: agent 第一句 exact match `What's on your mind?`；intake 起始**不**問 day-level Time Blocks；每 capture 後分類入 `task`/`question`/`thought`/`event`/`maybe` 之一；置於 `Focus`/`Now`/`Later`/`Questions` 之一；checkpoint 觸發於第三 reply 後。
- Diagnostic: tool/read transcript 顯 `references/examples.md` 於 Reflect / Commit move 時被讀（risk 1 訊號；不讀則升 pointer 強度）。

Edit case：

- Output: `Now` ↔ `Later` ↔ `Questions` 之 move 正確；Log 新增一行 `- HH:MM edit: <reason>`；既存 Log/Done/Deleted 史不損。

Delete case：

- Output: 須先 explicit confirm；item 自 `Focus`/`Now`/`Later`/`Questions`/`Time Blocks` 全處移除；append `Deleted` 含 reason；若刪 focused item，`Focus` 設為 `- none`；Log 新增 `- HH:MM delete: <reason>`。

每 case Codex 端重啟後同跑，比 Claude Code 行為一致。

失敗處置：

- L1 fail → 立修。
- L2 fail（語意失）→ patch，re-review。
- L3 fail（Claude 不載 references）→ 升 pointer 句強度（如加 `IMPORTANT: read this file before proceeding`），re-test。

## 風險

| # | 風險 | 解 |
|---|---|---|
| R1 | Pointer 失準：Claude / Codex 不主動讀 references | Anthropic Pattern 1 句式 + condition phrase（"Load when ..."）；one level deep；TOC in references。L3 驗。失則升強度。 |
| R2 | Codex sync：`/home/peter/.codex/skills/daily-assistant/` 未含 `references/` | Implementation plan 末含 sync step：reinstall 重啟 Codex 後 `diff -r` 驗。 |
| R3 | caveman lite 影響非英母語 LLM 解析 | lite 為最輕級（drop articles / fragments OK），且僅作於 SKILL.md body 之 prose，frontmatter / fixed prompts / code 不動，風險小。L3 驗。 |
| R4 | RVB section（dev 枝 uncommitted）丟失 | commit 1 先 commit 為 baseline，後續 refactor 在其上。 |

## 出範

- `~/.codex/skills/daily-assistant/` 之 source-of-truth 改造（仍由 Plan_Build repo 之 install 流程同步）。
- 其他 skill 之 caveman 改寫（不在本 spec）。
- caveman lite 以外之語體（wenyan / ultra）之套用。
- Calendar / Gmail / Drive / Tasks 整合（仍 v1 出範）。

## 來源

- [Anthropic — Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [Anthropic — Equipping agents with Agent Skills (progressive disclosure)](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- [OpenAI Codex — Agent Skills](https://developers.openai.com/codex/skills)
- [OpenAI Codex — AGENTS.md](https://developers.openai.com/codex/guides/agents-md)
- [Karpathy via LangChain — Context Engineering for Agents](https://blog.langchain.com/context-engineering-for-agents/)
- Steinberger（[steipete.me](https://steipete.me/)、[github.com/steipete](https://github.com/steipete)、[agent-rules](https://github.com/steipete/agent-rules)、[oracle skill](https://claude-plugins.dev/skills/@steipete/oracle/oracle)）：未見專論 token-slim rewrite 之文，部分命中。
