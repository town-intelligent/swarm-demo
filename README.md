# Swarm — AI Multi-Agent 辦公室

> 展示多個 AI Agent 協作的自動化工作流程

## 系統簡介

Swarm 是一個 **Multi-Agent 協作系統**，模擬真實辦公室的分工協作模式。每個 Agent 擁有獨立的身份、職責與性格，透過 GitHub Issues、Pull Requests 和 Discord 進行非同步協作。

### 解決什麼問題？

- **自動化日常任務** — 程式碼審查、文件維護、服務監控、進度追蹤
- **24/7 運作** — Agent 依排程或事件觸發自動喚醒，不需人工介入
- **可追溯的協作** — 所有工作記錄在 GitHub Issues，決策過程透明
- **人機協作** — Agent 執行重複性工作，人類做最終決策（如 merge PR）

---

## Agent 列表

| Agent | 綽號 | 角色 | 主要職責 |
|-------|------|------|----------|
| **devops** | AI 貓頭鷹 🦉 | 維運工程師 | 服務健康檢查、異常告警、自動部署、E2E 測試 |
| **rd** | AI 小豬 🐷 | 研發工程師 | 從 Issue 領任務、寫 code、開 PR、回應 code review |
| **reviewer** | AI 啄木鳥 🪶 | PR 審查員 | 自動審查 PR、提出改進建議、檢查潛在 bug |
| **tech-writer** | AI 狐狸 🦊 | 技術文件員 | 撰寫/更新技術文件、維護 HackMD 與 repo 同步 |
| **pm** | AI 小雞 🐥 | 專案經理 | 掃描 Issue、派任務、每日站會、每週週報 |
| **marketing** | AI 麻雀 🐦 | 內容與市場研究 | 撰寫行銷內容、競品調研、客戶回饋整理 |
| **bd** | AI 小蜜蜂 🐝 | 商務開發 | 搜尋研討會機會、潛在合作、社群討論、競品動態 |

---

## 協作流程

### Agent 醒來流程（多專案版）

每個 Agent 醒來時會依序執行：

```
1. 讀 CLAUDE.md（共用守則）
2. 讀 projects/{專案ID}/CLAUDE.md（專案規則）
3. 讀 agents/{自己}/SOUL.md（核心身份）
4. 讀 projects/{專案ID}/agents/{自己}/SOUL.md（專案操作範圍）
5. 讀 projects/{專案ID}/agents/{自己}/WORKING.md（上次進度）
6. 執行工作
7. 更新任務追蹤（GitHub/GitLab Issue）
8. 寫 projects/{專案ID}/issues/{自己}/（本地詳細紀錄）
9. Discord 通知（白話，附 Issue URL）
10. 更新 WORKING.md
11. 休眠
```

### Issue 驅動的工作流程

```
devops/所有人 發現問題或有成果 → 開 Issue
         ↓
pm 掃描所有 open issue → Discord 提醒 assignee → 每週發摘要
         ↓
rd 從 issue 領任務 → 寫 code → 開 PR
         ↓
reviewer 自動 code review PR → 留建議（不 merge）
         ↓
人類 審查 PR → 決定 merge
         ↓
tech-writer 從 issue 領文件任務 → 更新文件
         ↓
marketing 從 issue 領任務 / 主動提案
```

### Git Workflow

- **絕對不能直接 push 到 main/master** — 所有變更必須走 PR 流程
- **Branch 命名**: `{agent名稱}/{描述}`（例：`rd/add-readme`）
- **Commit format**: `[{agent名稱}] {type}: {description}`
  - **Type**: `feat` / `fix` / `docs` / `chore` / `refactor`
- **PR Review**: reviewer 自動審查，人類做最終 merge 決策

---

## 技術棧

### 核心技術

- **AI 模型**: Claude 3.5 Sonnet（via Anthropic API）
- **程式語言**: Python 3.11+
- **Agent 框架**: Custom scheduler + webhook handlers
- **版本控制**: Git + GitHub API / GitLab API
- **通訊**: Discord Bot API
- **文件協作**: HackMD API

### 整合服務

| 服務 | 用途 |
|------|------|
| **GitHub API** | Issue 管理、PR 審查、webhook 事件 |
| **GitLab API** | 多專案支援（部分專案使用 GitLab） |
| **Discord API** | 即時通知、團隊溝通 |
| **HackMD API** | 共享文件、週報發布 |

### 基礎設施

- **排程系統**: Cron-based scheduler（支援 webhook 觸發）
- **資料儲存**: 本地檔案系統（`.claude/`, `projects/`）
- **金鑰管理**: `.claude/keys/`（每個 Agent 獨立 token）

---

## 快速開始

### 前置需求

- Python 3.11+
- Git
- GitHub CLI (`gh`) 或 GitLab CLI (`glab`)
- Discord Bot Token
- Anthropic API Key

### 目錄結構

```
swarm/
├── CLAUDE.md                    # 共用守則
├── agents/                      # Agent 核心身份設定
│   ├── devops/SOUL.md
│   ├── rd/SOUL.md
│   ├── pm/SOUL.md
│   └── ...
├── projects/                    # 各專案設定
│   ├── swarm-demo/
│   │   ├── CLAUDE.md           # 專案規則
│   │   ├── project.yaml        # 排程 + SCM + Discord 設定
│   │   ├── agents/             # 專案特定 agent 設定
│   │   └── issues/             # 本地 issue 紀錄
│   └── {其他專案}/
├── scheduler/                   # 排程中心（待實作）
└── .claude/
    ├── keys/                    # 各 Agent 的 API tokens
    └── roles/                   # 人類角色定義
```

### 本地執行（TBD）

> 💡 本專案目前為展示用途，完整的 scheduler 與 webhook handler 實作尚在進行中。

未來將支援：

```bash
# 手動觸發單一 Agent
python scheduler/run_agent.py --project swarm-demo --agent rd

# 啟動排程服務
python scheduler/start.py

# 啟動 webhook server（接收 GitHub/GitLab 事件）
python scheduler/webhook_server.py
```

---

## 設計原則

### 三層回報機制

| 層級 | 位置 | 給誰看 | 詳細度 |
|------|------|--------|--------|
| 即時通知 | Discord | 團隊 | 白話摘要 |
| 任務追蹤 | GitHub/GitLab Issues | PM/團隊 | 中等 |
| 技術紀錄 | local `projects/{專案ID}/issues/` | 自己/Agent | 最詳細 |

### 鐵則：有成果必有 Issue

1. 所有 Agent 的工作成果，必須開 Issue 記錄
2. Discord 通知**必須附上 Issue URL**
3. 沒有 issue = 沒有發生過

### 人機協作邊界

- **Agent 負責**: 重複性工作、資料蒐集、初步審查、記錄整理
- **人類負責**: 最終決策（merge PR、發送 email、上線部署）
- **共同合作**: Issue 討論、需求釐清、問題排查

---

## 專案狀態

🚧 **本專案為演講 Demo 用途** — 展示 Multi-Agent 協作概念與工作流程

- ✅ Agent 身份與職責設計完成
- ✅ 協作流程規範建立
- 🚧 Scheduler 與 webhook handler 開發中
- 📅 預計於 2026 Q2 開源完整實作

---

## 相關資源

- [CLAUDE.md](CLAUDE.md) — Swarm 辦公室共用守則
- [Issue Tracker](https://github.com/town-intelligent/swarm-demo/issues) — 工作任務追蹤
- [Projects](https://github.com/town-intelligent/swarm-demo/projects) — 專案看板

---

## License

MIT License — 歡迎參考與改進

---

**Built with ❤️ by AI Agents & Humans**
