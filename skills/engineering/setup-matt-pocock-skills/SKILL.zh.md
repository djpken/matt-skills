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
- `triage` skill 是否已安裝？（在這個 skill 旁邊有 `triage` skill 資料夾，或你的可用 skills 中有 `triage`。）這決定 B 部分是否會執行。
- Monorepo 訊號 — `pnpm-workspace.yaml`、`package.json` 中的 `workspaces` 欄位，或有自己 `src/` 的 `packages/*`。只有在真正的大型多套件 repo 才會出現；沒有這些訊號就代表單一 context，幾乎每個 repo 都是這樣。

### 2. 呈現發現並詢問

總結存在的和缺少的。然後依序處理各部分——一個部分、一個答案，再進到下一個。

每個部分先給出建議答案，讓使用者一個字就能接受。只有在選擇真的會分岔時才給一行說明；如果探索階段已經確定答案，就整段跳過（`triage` 未安裝時跳過 B 部分，沒有 monorepo 時跳過 C 部分）。

**A 部分 — Issue 追蹤工具。**

> 說明：「Issue 追蹤工具」是這個 repo 的 issues 所在的地方。像 `to-tickets`、`triage`、`to-spec` 和 `qa` 這樣的 skills 從它讀取並寫入——它們需要知道是否要呼叫 `gh issue create`、在 `.scratch/` 下寫一個 markdown 檔案，或者遵循你描述的其他工作流程。選擇你實際為這個 repo 追蹤工作的地方。

預設立場：這些 skills 是為 GitHub 設計的。如果 `git remote` 指向 GitHub，提議那個。如果 `git remote` 指向 GitLab（`gitlab.com` 或自托管），提議 GitLab。否則（或如果使用者偏好），提供：

- **GitHub** — issues 存在於 repo 的 GitHub Issues 中（使用 `gh` CLI）
- **GitLab** — issues 存在於 repo 的 GitLab Issues 中（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **本地 markdown** — issues 以檔案存在於此 repo 的 `.scratch/<feature>/` 下（適合單人專案或沒有遠端的 repos）
- **其他**（Jira、Linear 等）— 請使用者用一段話描述工作流程；skill 將以自由格式散文記錄它

把選擇記錄在 `docs/agents/issue-tracker.md`。GitHub 和 GitLab 的範本帶有一個「PR 作為請求表面」旗標，預設為**關閉**——保持關閉、不要主動提起；想把外部 PR 納入 triage 佇列的使用者，之後可以自己在檔案裡把旗標打開。

**B 部分 — Triage 標籤詞彙。** 如果 `triage` skill 沒有安裝（探索階段已告訴你），整段跳過——沒安裝的 skill 不需要標籤。

如果已安裝，只問一個問題：

> 你想保留預設的 triage 標籤嗎？（建議：**是**）

預設是五個規範角色，每個標籤字串等於其名稱：`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`。選**是**就原樣寫入。只有使用者選否時——通常是因為他們的追蹤器已經用了其他名稱（例如用 `bug:triage` 代替 `needs-triage`）——才收集覆蓋值，讓 `triage` 套用既有標籤而不是建立重複的。

**C 部分 — 領域文件。** 預設為**單一 context**——在 repo 根目錄有一個 `CONTEXT.md` + `docs/adr/`。這適合幾乎所有 repo；不用詢問直接寫入。

只有在探索階段發現 monorepo 訊號時，才提供**多 context**——根目錄的 `CONTEXT-MAP.md` 指向每個 context 各自的 `CONTEXT.md` 檔案。接著確認他們想要哪種格局。

### 3. 確認並編輯

向使用者展示草稿：

- 要添加到正在編輯的 `CLAUDE.md` / `AGENTS.md` 中的 `## Agent skills` 區塊（見步驟 4 的選擇規則）
- `docs/agents/issue-tracker.md`、`docs/agents/domain.md`、`docs/agents/triage-labels.md`（只有在 `triage` 已安裝時才有最後一項）的內容

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

[一行摘要說明 issues 在哪裡追蹤]。見 `docs/agents/issue-tracker.md`。

### Triage labels

[一行摘要說明標籤詞彙]。見 `docs/agents/triage-labels.md`。

### Domain docs

[一行摘要說明格局——「單一 context」或「多 context」]。見 `docs/agents/domain.md`。
```

只有在 `triage` 已安裝且 B 部分有執行時，才納入 `### Triage labels` 子區塊並寫入 `docs/agents/triage-labels.md`。沒有的話，兩者都省略。

然後使用此 skill 資料夾中的種子模板寫入 docs 檔案：

- [issue-tracker-github.md](./issue-tracker-github.md) — GitHub issue 追蹤器
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md) — GitLab issue 追蹤器
- [issue-tracker-local.md](./issue-tracker-local.md) — 本地 markdown issue 追蹤器
- [triage-labels.md](./triage-labels.md) — 標籤映射（只有在 `triage` 已安裝時）
- [domain.md](./domain.md) — 領域文件消費者規則 + 格局

對於「其他」issue 追蹤器，使用使用者的描述從頭寫 `docs/agents/issue-tracker.md`。

### 5. 完成

告訴使用者設定完成，以及哪些 engineering skills 現在將從這些檔案讀取。提到他們可以之後直接編輯 `docs/agents/*.md`——只有在想切換 issue 追蹤器或從頭開始時才需要重新執行這個 skill。
