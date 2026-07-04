# 情境技能路由

> [← 返回中文說明](../中文.md) · [English README](../README.md)

根據你**現在遇到的情境**，快速找到對應的 skill。

標記說明：🚧 開發中（in-progress）· 🔒 個人設定（personal）· ~~刪除線~~ 已棄用（deprecated）。無標記者為正式 skill。

技能分兩類：**User-invoked（使用者呼叫）** 只有你輸入時才觸發；**Model-invoked（模型呼叫）** 你可呼叫、agent 也會在情境符合時自動觸發。

---

## 開始之前

| 情境 | 使用技能 |
|------|---------|
| 不知道目前情境該用哪個 skill | [`/ask-matt`](../skills/engineering/ask-matt/SKILL.md) |
| 第一次用這套 skills，需要設定 repo | [`/setup-matt-pocock-skills`](../skills/engineering/setup-matt-pocock-skills/SKILL.md) |
| 不知道自己要建什麼、需求還很模糊 | [`/grill-me`](../skills/productivity/grill-me/SKILL.md) |
| 有程式碼相關問題、想同時建立領域模型與文件 | [`/grill-with-docs`](../skills/engineering/grill-with-docs/SKILL.md) |

---

## 寫程式途中

| 情境 | 使用技能 |
|------|---------|
| 要開始實作某個功能或修 bug | [`/tdd`](../skills/engineering/tdd/SKILL.md) |
| 已有 PRD / issues，要照著實作 | [`/implement`](../skills/engineering/implement/SKILL.md) |
| 要把計畫 / 討論轉成 issues | [`/to-issues`](../skills/engineering/to-issues/SKILL.md) |
| 要產出 PRD（產品需求文件） | [`/to-prd`](../skills/engineering/to-prd/SKILL.md) |
| 需要快速建立原型來驗證想法 | [`/prototype`](../skills/engineering/prototype/SKILL.md) |

---

## 出問題了

| 情境 | 使用技能 |
|------|---------|
| Bug 很難追、或有效能退化 | [`/diagnosing-bugs`](../skills/engineering/diagnosing-bugs/SKILL.md) |
| 卡在 git merge / rebase 衝突 | [`/resolving-merge-conflicts`](../skills/engineering/resolving-merge-conflicts/SKILL.md) |
| Issue 堆積如山，需要分類整理 | [`/triage`](../skills/engineering/triage/SKILL.md) |

---

## 程式碼品質與架構

| 情境 | 使用技能 |
|------|---------|
| 程式碼庫越來越亂、架構開始腐敗 | [`/improve-codebase-architecture`](../skills/engineering/improve-codebase-architecture/SKILL.md) |
| 要設計深層模組（小介面、乾淨接縫、可測試） | [`/codebase-design`](../skills/engineering/codebase-design/SKILL.md) |
| 要建立 / 精煉專案的領域模型與術語 | [`/domain-modeling`](../skills/engineering/domain-modeling/SKILL.md) |
| 要 review 某個 branch / PR 是否符合規格與慣例 | [`/code-review`](../skills/engineering/code-review/SKILL.md) |
| 想為 dangerous git 指令設防護網 | [`/git-guardrails-claude-code`](../skills/misc/git-guardrails-claude-code/SKILL.md) |
| 要設定 pre-commit hooks（Prettier、型別、測試） | [`/setup-pre-commit`](../skills/misc/setup-pre-commit/SKILL.md) |

---

## Agent 行為調整

| 情境 | 使用技能 |
|------|---------|
| 要把工作交接給另一個 Agent 繼續 | [`/handoff`](../skills/productivity/handoff/SKILL.md) |

---

## 學習與擴充

| 情境 | 使用技能 |
|------|---------|
| 想學某個新技術 / 概念（多 session 學習） | [`/teach`](../skills/productivity/teach/SKILL.md) |
| 想自己寫一個新的 skill | [`/writing-great-skills`](../skills/productivity/writing-great-skills/SKILL.md) |
| 想把一個鬆散的想法拆成可調查的票券並逐一推進 | 🚧 [`/wayfinder`](../skills/in-progress/wayfinder/SKILL.md) |

---

## 寫作

| 情境 | 使用技能 |
|------|---------|
| 腦中有一堆想法，想先把素材挖出來、不急著成文 | 🚧 [`/writing-fragments`](../skills/in-progress/writing-fragments/SKILL.md) |
| 有一堆原始素材，想逐步塑形成文章 | 🚧 [`/writing-shape`](../skills/in-progress/writing-shape/SKILL.md) |
| 想像敘事一樣、beat by beat 地拼出一篇文章 | 🚧 [`/writing-beats`](../skills/in-progress/writing-beats/SKILL.md) |
| 有草稿，想改得更清晰、段落更緊實 | 🔒 [`/edit-article`](../skills/personal/edit-article/SKILL.md) |

---

## 雜項工具

| 情境 | 使用技能 |
|------|---------|
| 要把 `as` 型別斷言遷移到 shoehorn | [`/migrate-to-shoehorn`](../skills/misc/migrate-to-shoehorn/SKILL.md) |
| 要建立練習題目的目錄結構 | [`/scaffold-exercises`](../skills/misc/scaffold-exercises/SKILL.md) |
| 管理 Obsidian 筆記（個人 vault） | 🔒 [`/obsidian-vault`](../skills/personal/obsidian-vault/SKILL.md) |

---

## 已棄用（deprecated）

以下 skills 已停止維護，列出供參考，了解其功能現在由哪個 skill 取代：

| 已棄用 skill | 曾做什麼 | 現在改用 |
|------------|---------|---------|
| ~~`/design-an-interface`~~ | 用平行 sub-agent 生成多種 API / 介面設計方案 | [`/codebase-design`](../skills/engineering/codebase-design/SKILL.md) + [`/prototype`](../skills/engineering/prototype/SKILL.md) |
| ~~`/qa`~~ | 對話式 QA 回報 bug，自動開 issue | [`/triage`](../skills/engineering/triage/SKILL.md) |
| ~~`/request-refactor-plan`~~ | 訪談後產出 refactor 計畫並開 issue | [`/grill-with-docs`](../skills/engineering/grill-with-docs/SKILL.md) + [`/to-prd`](../skills/engineering/to-prd/SKILL.md) |
| ~~`/ubiquitous-language`~~ | 從對話萃取 DDD 術語表，存入 `UBIQUITOUS_LANGUAGE.md` | [`/domain-modeling`](../skills/engineering/domain-modeling/SKILL.md) |

---

## 快速決策樹

```
你想做什麼？
│
├─ 不知道用哪個 → /ask-matt
│
├─ 釐清需求
│   ├─ 非程式碼 → /grill-me
│   └─ 有程式碼 → /grill-with-docs
│
├─ 寫程式
│   ├─ 從需求開始 → /to-prd → /to-issues → /implement 或 /tdd
│   └─ 直接 debug → /diagnosing-bugs
│
├─ 改善現有程式碼
│   ├─ 架構問題 → /improve-codebase-architecture
│   ├─ 模組設計 → /codebase-design
│   ├─ 領域模型 → /domain-modeling
│   ├─ 要 review 變更 → /code-review
│   └─ merge 衝突 → /resolving-merge-conflicts
│
├─ 管理工作流程
│   ├─ 整理 issues → /triage
│   └─ 交接工作 → /handoff
│
├─ 寫作
│   ├─ 挖素材 → /writing-fragments 🚧
│   ├─ 塑形成文 → /writing-shape 🚧
│   ├─ 敘事結構 → /writing-beats 🚧
│   └─ 改草稿 → /edit-article 🔒
│
└─ 學習 / 擴充
    ├─ 學新東西 → /teach
    └─ 建新 skill → /writing-great-skills
```

---

> 完整技能說明請見 [中文說明](../中文.md)
