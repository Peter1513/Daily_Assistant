# Daily Assistant 參考 repo / plugin / skill

日期：2026-04-29

目的：彙整可參考之 Codex plugin、skill、GitHub repo，用以設計「以 brainstorming 問答方式，協助釐清今日 task、排程、優先順序，並支援 Google 生態系」之 Daily Assistant。

## 判斷摘要

- 尚未見一顆成熟、官方、直名為「personal daily assistant」且完整覆蓋 brainstorming 問答、今日 task 整理、Google Calendar/Gmail/Tasks/Drive 排程者。
- 最佳路徑似是：以 Google 工具層 repo/plugin 取資料與執行，再另寫一個薄 skill：`daily-planning-brainstorm`。
- 參考重點：
  - Google Calendar / Gmail / Drive / Tasks 等作資料與工具層。
  - brainstorming-like skill 作對話流程層。
  - 寫入 Calendar / Tasks 前須明確確認。

## 高度相關

### googleworkspace/cli

- URL: https://github.com/googleworkspace/cli
- 類型：Google Workspace CLI；含多個 `SKILL.md`。
- 涵蓋：Gmail、Calendar、Drive、Docs、Sheets 等 Google Workspace 工具。
- 可借之處：
  - Google 生態支援面廣。
  - 可研究其 workflow skills，例如 standup report、meeting prep、email-to-task、weekly digest 類型流程。
- 限制：
  - 偏工具與 workflow 執行層；非專為「一問一答釐清今日任務」而設。
- 適配度：高。

### openai/plugins

- URL: https://github.com/openai/plugins
- 類型：OpenAI 官方 Codex plugins repo。
- 涵蓋：
  - Google Calendar
  - Gmail
  - Google Drive
  - Notion
  - Slack
  - Teams
  - Outlook Calendar / Outlook Email
- 可借之處：
  - Codex-native plugin 形狀。
  - Google Calendar / Gmail / Drive 可作日常助理工具底座。
  - 可參考 `.codex-plugin/plugin.json`、skills、MCP tool wiring。
- 限制：
  - 主要提供工具能力，不直接提供「每日 brainstorming 規劃」主流程。
- 適配度：高。

### bestlux/mail-agent

- URL: https://github.com/bestlux/mail-agent
- 類型：Codex plugin + local MCP daemon。
- 涵蓋：
  - Gmail API
  - Google Calendar API
  - Google People API
  - mail / calendar / contacts workflows
- 可借之處：
  - 很接近「個人助理」；含 inbox triage、calendar brief、contacts lookup 等工作流。
  - 可參考其 local daemon 與 Google OAuth / API 串接方式。
- 限制：
  - 主軸偏 mail/calendar/contacts，不是 task brainstorming 主流程。
- 適配度：高。

### 6Delta9/task-scheduler-codex-plugin

- URL: https://github.com/6Delta9/task-scheduler-codex-plugin
- 類型：Codex plugin / MCP / task planning skill。
- 涵蓋：
  - task list -> realistic schedule
  - `.codex-plugin`
  - `skills/task-planner/SKILL.md`
- 可借之處：
  - 任務排程邏輯與 Codex plugin 結構。
  - 可作「任務清單轉 time blocks」參考。
- 限制：
  - 不明顯聚焦 Google 生態；需另接 Calendar / Tasks。
- 適配度：中高。

## Google / Apple / 本機生產力工具層

### matk0shub/apple-productivity-mcp

- URL: https://github.com/matk0shub/apple-productivity-mcp
- 類型：MCP server / Codex plugin adapters。
- 涵蓋：
  - Apple Calendar
  - Apple Reminders
- 可借之處：
  - 本機 calendar/reminders 的 assistant 模式。
  - 可參考 MCP tool boundary 與讀寫提醒事項之安全模型。
- 限制：
  - Apple 生態，非 Google-first。
- 適配度：中。

### openclaw/skills: gcalcli-calendar

- URL: https://github.com/openclaw/skills/tree/main/skills/lstpsche/gcalcli-calendar
- 類型：skill。
- 涵蓋：
  - 透過 `gcalcli` 操作 Google Calendar。
- 可借之處：
  - Google Calendar CLI tool 使用規範。
  - 可參考 calendar read/write safety pattern。
- 限制：
  - 工具操作層，非每日規劃對話流程。
- 適配度：中。

### badlogic/pi-skills

- URL: https://github.com/badlogic/pi-skills
- 類型：skills collection。
- 涵蓋：
  - `gccli`：Google Calendar
  - `gmcli`：Gmail
  - `gdcli`：Google Drive
- 可借之處：
  - 輕量 Google 工具技能。
  - 可參考 CLI adapter 風格。
- 限制：
  - 非專為 Codex daily planning assistant。
- 適配度：中。

## Brainstorming-like / ritual 流程參考

### openclaw/skills: daily-briefing

- URL: https://github.com/openclaw/skills/blob/main/skills/antgly/daily-briefing/SKILL.md
- 類型：skill。
- 涵蓋：
  - daily briefing
  - weather / calendar / reminders / important emails 類資訊聚合
  - missing-info prompt
- 可借之處：
  - 「晨間 brief」輸出結構。
  - 可參考在資訊不足時如何互動式補問。
- 限制：
  - 偏 briefing，不是 task brainstorming / prioritization 主流程。
- 適配度：中高。

### openclaw/skills: daily-review-ritual

- URL: https://github.com/openclaw/skills/tree/main/skills/itsflow/daily-review-ritual
- 類型：skill。
- 涵蓋：
  - daily review
  - reflection
  - completion / blockers / insight / next top tasks
- 可借之處：
  - 一問一答 ritual flow。
  - 可改造成 morning planning 或 end-of-day review。
- 限制：
  - 未直接接 Google 工具。
- 適配度：中高。

### ComposioHQ/awesome-codex-skills

- URL: https://github.com/ComposioHQ/awesome-codex-skills
- 類型：Codex skills collection。
- 可借之 skill 類型：
  - `meeting-notes-and-actions`
  - `meeting-insights-analyzer`
  - `email-draft-polish`
  - `notion-knowledge-capture`
  - `file-organizer`
  - `connect`
- 可借之處：
  - skill metadata / workflow 寫法。
  - meeting action extraction 可補 daily planning 之「從會議/信件抽待辦」。
- 限制：
  - 非 Google-specific；多為通用 workflow。
- 適配度：中。

## 第二腦 / 知識整理

### greg-asher/codex-obsidian

- URL: https://github.com/greg-asher/codex-obsidian
- 類型：Codex + Obsidian workflow repo。
- 涵蓋：
  - Obsidian vault workflows
  - daily bootstrap
  - tasks rollup
  - workspace focus
- 可借之處：
  - daily note / task rollup 結構。
  - 可作 Google Tasks / Calendar 以外之 long-term memory / second brain 參考。
- 限制：
  - Obsidian-first，非 Google-first。
- 適配度：中。

### huytieu/COG-second-brain

- URL: https://github.com/huytieu/COG-second-brain
- 類型：second brain / assistant workflow。
- 涵蓋：
  - daily brief
  - braindump
  - weekly checkin
  - people CRM
- 可借之處：
  - 個人知識與每日節奏設計。
  - 可參考 daily / weekly ritual 的 artifact 結構。
- 限制：
  - Codex 主要走 `AGENTS.md` fallback，不是純 Codex-native plugin。
  - 非 Google-first。
- 適配度：中。

### octo-patch/MorningAI

- URL: https://github.com/octo-patch/MorningAI
- 類型：daily AI/news report。
- 涵蓋：
  - morning AI news / report
- 可借之處：
  - 晨間資訊摘要形式。
- 限制：
  - 偏 AI news，不是個人任務/排程助理。
- 適配度：低中。

## 索引 / marketplace

### hashgraph-online/awesome-codex-plugins

- URL: https://github.com/hashgraph-online/awesome-codex-plugins
- 類型：Codex plugins / skills 索引。
- 可借之處：
  - 持續尋找 community plugin / skill。
  - 可作後續掃描入口。
- 限制：
  - 索引本身非可用 assistant。
- 適配度：中。

### Codex Marketplace plugin pages

- URL: https://www.codex-marketplace.com/plugins
- 類型：非官方 marketplace browser / plugin index。
- 曾查得相關 plugin：
  - Google Calendar: https://www.codex-marketplace.com/plugins/google-calendar
  - Gmail: https://www.codex-marketplace.com/plugins/gmail
  - Google Drive: https://www.codex-marketplace.com/plugins/google-drive
  - Notion: https://www.codex-marketplace.com/plugins/notion
  - Slack: https://www.codex-marketplace.com/plugins/slack
  - Teams: https://www.codex-marketplace.com/plugins/teams
  - Outlook Calendar: https://www.codex-marketplace.com/plugins/outlook-calendar
  - Outlook Email: https://www.codex-marketplace.com/plugins/outlook-email
- 可借之處：
  - 快速看 plugin 類型、能力摘要、官方/社群標記。
- 限制：
  - 非 OpenAI 官方。實際採用前須回到 GitHub repo / manifest / MCP scopes 查驗。
- 適配度：中。

## 建議萃取方向

擬作 `daily-planning-brainstorm` skill 時，可分三層：

1. 問答流程層
   - 仿 `superpowers:brainstorming`：一次一問，先釐清目的、限制、成功條件。
   - 今日 planning 問題可含：硬性會議、必交事項、精力狀態、時間窗、風險、可延後項。

2. Google 工具層
   - Calendar：讀今日事件、空檔、通勤/緩衝。
   - Gmail：找 urgent threads / waiting replies / action items。
   - Drive / Docs：找今日相關文件。
   - Tasks：讀/寫 task list。

3. 排程決策層
   - 先產 priority order，再產 time blocks。
   - 寫入 Google Calendar / Tasks 前，必須顯示 diff 並取得確認。

## 安全與採用注意事項

以下涉及 Gmail、Calendar、Drive、Contacts 等私人資料；安裝或啟用第三方 repo 前，請先檢查：

- OAuth scopes 是否過寬。
- 是否有寫入、刪除、寄信、改日曆事件能力。
- credentials 存放位置與權限。
- MCP server 是否連外或開本機 port。
- plugin manifest 是否明確列出 tools / skills。
- 初版建議採 read-only；寫入 Calendar / Tasks 前，每次要求明確確認。

## 候選整合方案

### 方案 A：官方 Google plugins + 自寫 planning skill

- 底座：`openai/plugins` 中 Google Calendar / Gmail / Drive。
- 自寫：`daily-planning-brainstorm` skill。
- 優點：Codex-native、官方來源、風險較低。
- 缺點：需自行補 Google Tasks 或 task 寫入流程。

### 方案 B：googleworkspace/cli + 自寫 planning skill

- 底座：`googleworkspace/cli`。
- 自寫：`daily-planning-brainstorm` skill。
- 優點：Google Workspace 覆蓋廣，workflow skill 多。
- 缺點：需確認與 Codex plugin / skill 整合方式。

### 方案 C：mail-agent + planning skill

- 底座：`bestlux/mail-agent`。
- 自寫：task brainstorming 與 priority flow。
- 優點：mail/calendar/contacts 已接近 personal assistant。
- 缺點：第三方 repo，需嚴審 Google API scopes 與本機 daemon。

## 初版 MVP 建議

- 先讀 Calendar + 手動輸入 tasks。
- Gmail 只做 read-only urgent scan。
- 不自動寫入任何 Google 服務。
- 輸出：
  - 今日 Top 3
  - Must / Should / Could
  - Time blocks
  - Risk / dependency
  - 可延後清單
  - 若需寫入 Calendar / Tasks，列出 proposed changes 並等待確認。
