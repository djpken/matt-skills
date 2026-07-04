---
name: setup-matt-pocock-skills
description: 為本 repo 設定 engineering skills——設定 issue 追蹤工具、triage 標籤詞彙和領域文件格局。在首次使用其他 engineering skill 前，每個 repo 執行一次。
disable-model-invocation: true
---

# Setup Matt Pocock's Skills（設定 Matt Pocock 的 Skills）

為 engineering skills 所需的每個 repo 設定腳手架：

- **Issue 追蹤工具** — issues 存放的地方（預設為 GitHub；本地 markdown 也直接支援）
- **Triage 標籤** — 五個規範 triage 角色所用的字串
- **領域文件** — `CONTEXT.md` 和 ADR 存放的位置，以及讀取它們的消費者規則

這是一個提示驅動的 skill，不是一個確定性腳本。探索、呈現你找到的東西、與使用者確認，然後寫入。

## 流程

### 1. 探索

查看目前的 repo 以了解其起始狀態。讀取存在的東西；不要假設：

- `git remote -v` 和 `.git/config` — 這是 GitHub repo 嗎？哪個？
- 在 repo 根目錄的 `AGENTS.md` 和 `CLAUDE.md` — 兩者存在嗎？其中任何一個已有 `## Agent skills` 部分嗎？
- 在 repo 根目錄的 `CONTEXT.md` 和 `CONTEXT-MAP.md`
- `docs/adr/` 和任何 `src/*/docs/adr/` 目錄
- `docs/agents/` — 這個 skill 的先前輸出是否已存在？
- `.scratch/` — 表示本地 markdown issue 追蹤慣例已在使用中

### 2. 呈現發現並詢問

總結存在的和缺少的。然後**逐一**引導使用者做三個決策——呈現一個部分，得到使用者的答案，然後移到下一個。不要一次丟出全部三個。

假設使用者不知道這些術語的意思。每個部分以簡短的說明開始（它是什麼、為什麼這些 skills 需要它、如果他們選擇不同會有什麼變化）。然後展示選項和預設。

**A 部分 — Issue 追蹤工具。**

> 說明：「Issue 追蹤工具」是這個 repo 的 issues 所在的地方。像 `to-issues`、`triage`、`to-prd` 和 `qa` 這樣的 skills 從它讀取並寫入——它們需要知道是否要呼叫 `gh issue create`、在 `.scratch/` 下寫一個 markdown 檔案，或者遵循你描述的其他工作流程。選擇你實際為這個 repo 追蹤工作的地方。

預設立場：這些 skills 是為 GitHub 設計的。如果 `git remote` 指向 GitHub，提議那個。如果 `git remote` 指向 GitLab（`gitlab.com` 或自托管），提議 GitLab。否則（或如果使用者偏好），提供：

- **GitHub** — issues 存在於 repo 的 GitHub Issues 中（使用 `gh` CLI）
- **GitLab** — issues 存在於 repo 的 GitLab Issues 中（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **本地 markdown** — issues 以檔案存在於此 repo 的 `.scratch/<feature>/` 下（適合單人專案或沒有遠端的 repos）
- **其他**（Jira、Linear 等）— 請使用者用一段話描述工作流程；skill 將以自由格式散文記錄它

如果——且只有在——使用者選擇了 **GitHub** 或 **GitLab** 時，詢問一個後續問題：

> 說明：開源 repos 通常以 pull requests 而非 issues 的形式收到功能請求——PR 是帶有附加程式碼的 issue。如果你開啟這個，`/triage` 會把*外部* PR 拉進同樣的佇列，並對它們執行和 issues 相同的標籤和狀態（協作者的進行中 PR 不受影響）。如果 PR 對你來說不是請求表面，就關閉它。

- **PR 作為請求表面** — 是 / 否（預設：否）。將答案記錄在 `docs/agents/issue-tracker.md`。對本地 markdown 和其他追蹤器，跳過這個問題——沒有 PR。

**B 部分 — Triage 標籤詞彙。**

> 說明：當 `triage` skill 處理傳入的 issue 時，它會通過狀態機移動——需要評估、等待報告者、準備好讓 AFK agent 接手、準備好給人類，或不會修復。要做到這點，它需要應用標籤（或你的 issue 追蹤器中的等效物），這些標籤匹配*你實際設定的*字串。如果你的 repo 已使用不同的標籤名稱（例如 `bug:triage` 而不是 `needs-triage`），在這裡映射它們，讓 skill 應用正確的標籤而不是建立重複的。

五個規範角色：

- `needs-triage` — 維護者需要評估
- `needs-info` — 等待報告者回應
- `ready-for-agent` — 完全指定，AFK 就緒（agent 可以在沒有人類背景的情況下接手）
- `ready-for-human` — 需要人類實作
- `wontfix` — 不會被處理

預設：每個角色的字串等於其名稱。詢問使用者是否想覆蓋任何一個。如果他們的 issue 追蹤器沒有現有標籤，預設就很好。

**C 部分 — 領域文件。**

> 說明：一些 skills（`improve-codebase-architecture`、`diagnosing-bugs`、`tdd`）讀取 `CONTEXT.md` 檔案來學習專案的領域語言，並從 `docs/adr/` 讀取過去的架構決策。它們需要知道 repo 是有一個全局 context 還是多個（例如，具有分離前端/後端 context 的 monorepo），以便在正確的地方查找。

確認格局：

- **單一 context** — 在 repo 根目錄有一個 `CONTEXT.md` + `docs/adr/`。大多數 repos 是這樣。
- **多 context** — 根目錄有 `CONTEXT-MAP.md`，指向每個 context 的 `CONTEXT.md` 檔案（通常是 monorepo）。

### 3. 確認並編輯

向使用者展示草稿：

- 要添加到正在編輯的 `CLAUDE.md` / `AGENTS.md` 中的 `## Agent skills` 區塊（見步驟 4 的選擇規則）
- `docs/agents/issue-tracker.md`、`docs/agents/triage-labels.md`、`docs/agents/domain.md` 的內容

在寫入前讓他們編輯。

### 4. 寫入

**選擇要編輯的檔案：**

- 如果 `CLAUDE.md` 存在，編輯它。
- 否則如果 `AGENTS.md` 存在，編輯它。
- 如果兩者都不存在，詢問使用者要建立哪一個——不要替他們選擇。

當 `CLAUDE.md` 已存在時，絕不建立 `AGENTS.md`（反之亦然）——務必編輯已存在的那個。

如果所選檔案中已存在 `## Agent skills` 區塊，就地更新其內容，而不是附加重複的內容。不要覆蓋使用者對周圍部分的編輯。

區塊：

```markdown
## Agent skills

### Issue tracker

[一行摘要說明 issues 在哪裡追蹤，以及外部 PR 是否是 triage 表面]。見 `docs/agents/issue-tracker.md`。

### Triage labels

[一行摘要說明標籤詞彙]。見 `docs/agents/triage-labels.md`。

### Domain docs

[一行摘要說明格局——「單一 context」或「多 context」]。見 `docs/agents/domain.md`。
```

然後使用此 skill 資料夾中的種子模板寫入三個 docs 檔案：

- [issue-tracker-github.md](./issue-tracker-github.md) — GitHub issue 追蹤器
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md) — GitLab issue 追蹤器
- [issue-tracker-local.md](./issue-tracker-local.md) — 本地 markdown issue 追蹤器
- [triage-labels.md](./triage-labels.md) — 標籤映射
- [domain.md](./domain.md) — 領域文件消費者規則 + 格局

對於「其他」issue 追蹤器，使用使用者的描述從頭寫 `docs/agents/issue-tracker.md`。

### 5. 完成

告訴使用者設定完成，以及哪些 engineering skills 現在將從這些檔案讀取。提到他們可以之後直接編輯 `docs/agents/*.md`——只有在想切換 issue 追蹤器或從頭開始時才需要重新執行這個 skill。
