---
name: setup-matt-pocock-skills
description: "為此儲存庫設定工程技能：建立其問題追蹤器、分級標籤詞彙以及領域檔案佈局。在首次使用其他工程技能之前執行一次。"
disable-model-invocation: true
---

<a id="setup-matt-pococks-skills"></a>

# 設定 Matt Pocock 的技能

建立工程技能所假設的每個儲存庫設定：

- **問題追蹤器**：問題存放的位置（預設為 GitHub；也原生支援本機 markdown）
- **分級標籤**：用於五個標準分級角色的字串
- **領域檔案**：`GLOSSARY.md` 和 ADR 的存放位置，以及閱讀它們的消費者規則

這是一個提示驅動的技能，而非確定性指令碼。進行探索，呈現您發現的內容，與使用者確認，然後撰寫。

<a id="process"></a>

## 流程

<a id="1-explore"></a>

### 1. 探索

檢視目前的儲存庫以瞭解其起始狀態。閱讀任何已存在的內容；不要假設：

- `git remote -v` 和 `.git/config`：這是否為 GitHub 儲存庫？是哪一個？
- 儲存庫根目錄下的 `AGENTS.md` 和 `CLAUDE.md`：是否存在其中之一？其中是否已有 `## Agent skills` 區段？
- 儲存庫根目錄下的 `GLOSSARY.md` 和 `GLOSSARY-MAP.md`
- `docs/adr/` 以及任何 `src/*/docs/adr/` 資料夾
- `docs/agents/`：此技能先前的輸出是否已存在？
- `.scratch/`：表示已在使用本機 markdown 問題追蹤器慣例的跡象
- `triage` 技能是否已安裝？（與此技能並列的 `triage` 技能資料夾，或您可用技能中的 `triage`。）這將決定是否執行 B 區段。
- Monorepo 訊號：`pnpm-workspace.yaml`、`package.json` 中的 `workspaces` 欄位，或包含自身 `src/` 的已填充 `packages/*`。這些僅存在於真正的大型多套件儲存庫中；若不存在，則表示單一上下文，這適用於幾乎所有儲存庫。

<a id="2-present-findings-and-ask"></a>

### 2. 呈現發現並詢問

總結存在與缺失的內容。然後依序處理各區段。一個區段，一個答案，然後下一個。

每個區段以建議答案開頭，讓使用者可以一個字接受。僅當選擇真正分岔時，才給出一行說明；若探索已確定答案，則完全跳過該區段（若 `triage` 未安裝則跳過 B 區段，若無 monorepo 則跳過 C 區段）。

**A 區段：問題追蹤器。**

> 說明：「問題追蹤器」是此儲存庫中問題存放的位置。像 `to-tickets`、`triage` 和 `to-spec` 這樣的技能會從中讀取並寫入。它們需要知道是呼叫 `gh issue create`、在 `.scratch/` 下撰寫 markdown 檔案，還是遵循您描述的其他工作流程。選擇您實際為此儲存庫追蹤工作的地方。

預設姿態：這些技能是為 GitHub 設計的。如果 `git remote` 指向 GitHub，建議該選項。如果 `git remote` 指向 GitLab（`gitlab.com` 或自架主機），建議 GitLab。否則（或若使用者偏好），提供：

- **GitHub**：問題存放於儲存庫的 GitHub Issues（使用 `gh` CLI）
- **GitLab**：問題存放於儲存庫的 GitLab Issues（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **本機 markdown**：問題作為檔案存放於此儲存庫的 `.scratch/<feature>/` 下（適合個人專案或無遠端儲存庫的專案）
- **其他**（Jira、Linear 等）：請使用者用一段話描述工作流程；技能將將其記錄為自由格式散文

在 `docs/agents/issue-tracker.md` 中記錄選擇。GitHub 和 GitLab 範本包含一個「PR 作為請求介面」旗標，預設為**關閉**。保持關閉且不要提及：若使用者希望將外部 PR 納入分級佇列，之後可在檔案中切換該旗標。

**B 區段：分級標籤詞彙。** 若 `triage` 技能未安裝（探索已告知您），則完全跳過此區段，因為未安裝的技能不需要標籤。

若已安裝，精確詢問一個問題：

> 您是否要保留預設的分級標籤？（建議：**是**）

預設值為五個標準角色，每個標籤字串等於其名稱：`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`。若回答**是**，原樣寫入。僅當使用者說否時（通常因為其追蹤器已使用其他名稱，例如用 `bug:triage` 代表 `needs-triage`），收集覆蓋設定，以便 `triage` 套用現有標籤而非建立重複專案。

**C 區段：領域檔案。** 預設為**單一上下文**（儲存庫根目錄下的一個 `GLOSSARY.md` + `docs/adr/`）。這適用於幾乎所有儲存庫；無需詢問即可寫入。

僅當探索發現 monorepo 訊號時，才提供**多上下文**（根目錄的 `GLOSSARY-MAP.md` 指向每個上下文的 `GLOSSARY.md` 檔案）。然後確認他們想要的佈局。

<a id="3-confirm-and-edit"></a>

### 3. 確認並編輯

向使用者展示以下草稿：

- 要新增至 `CLAUDE.md` / `AGENTS.md` 中正在編輯的那個檔案的 `## Agent skills` 區塊（選擇規則見步驟 4）
- `docs/agents/issue-tracker.md`、`docs/agents/domain.md` 和 `docs/agents/triage-labels.md` 的內容（最後一個僅在 `triage` 已安裝時）

讓使用者在寫入前進行編輯。

<a id="4-write"></a>

### 4. 寫入

**選擇要編輯的檔案：**

- 若 `CLAUDE.md` 存在，編輯它。
- 否則，若 `AGENTS.md` 存在，編輯它。
- 若兩者都不存在，詢問使用者要建立哪一個；不要替他們選擇。

當 `CLAUDE.md` 已存在時，絕不建立 `AGENTS.md`（反之亦然）；始終編輯已存在的那個。

若選定的檔案中已存在 `## Agent skills` 區塊，請就地更新其內容，而非附加重複專案。不要覆蓋使用者對周圍區段的編輯。

該區塊：

```markdown
## Agent skills

### Issue tracker

[one-line summary of where issues are tracked]. See `docs/agents/issue-tracker.md`.

### Triage labels

[one-line summary of the label vocabulary]. See `docs/agents/triage-labels.md`.

### Domain docs

[one-line summary of layout: "single-context" or "multi-context"]. See `docs/agents/domain.md`.
```

包含 `### Triage labels` 子區塊，並寫入 `docs/agents/triage-labels.md`，僅在 `triage` 已安裝且 B 區段已執行時。若未執行，則兩者皆省略。

當 B 區段在 GitHub 或 GitLab 上執行時，建立追蹤器缺少的每個已設定標籤（`gh label create` / `glab label create`）。

然後使用此技能資料夾中的種子範本作為起點，寫入檔案檔案：

- [issue-tracker-github.md](./issue-tracker-github.md)：GitHub 問題追蹤器
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md)：GitLab 問題追蹤器
- [issue-tracker-local.md](./issue-tracker-local.md)：本機 markdown 問題追蹤器
- [triage-labels.md](./triage-labels.md)：標籤對應（僅在 `triage` 已安裝時）
- [domain.md](./domain.md)：領域檔案消費者規則 + 佈局

對於「其他」問題追蹤器，使用使用者的描述從頭撰寫 `docs/agents/issue-tracker.md`。

<a id="5-done"></a>

### 5. 完成

告知使用者設定已完成，以及哪些工程技能將從這些檔案讀取。提及他們之後可以直接編輯 `docs/agents/*.md`；僅當他們想要切換問題追蹤器或從頭開始時，才需要重新執行此技能。
