<a id="issue-tracker-gitlab"></a>

# 問題追蹤器：GitLab

此儲存庫的問題與規格以 GitLab 問題的形式存在。所有操作請使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI。

<a id="conventions"></a>

## 慣例

- **建立問題**：`glab issue create --title "..." --description "..."`。多行描述請使用 heredoc。傳遞 `--description -` 以開啟編輯器。
- **讀取問題**：`glab issue view <number> --comments`。使用 `-F json` 以取得機器可讀取的輸出。
- **列出問題**：`glab issue list -O json`，並搭配適當的 `--label` 篩選器。
- **對問題留言**：`glab issue note <number> --message "..."`。GitLab 將留言稱為「notes」。
- **套用 / 移除標籤**：`glab issue update <number> --label "..."` / `--unlabel "..."`。多個標籤可以逗號分隔，或重複使用旗標。
- **關閉**：`glab issue close <number>`。`glab issue close` 不接受關閉時的留言，因此請先使用 `glab issue note <number> --message "..."` 發布說明，再執行關閉。
- **合併請求**：GitLab 將 PR 稱為「merge requests」。使用 `glab mr create`、`glab mr view`、`glab mr note` 等，其結構與 `gh pr ...` 相同，僅以 `mr` 取代 `pr`，並以 `note`/`--message` 取代 `comment`/`--body`。

從 `git remote -v` 推斷儲存庫；在克隆中執行時，`glab` 會自動執行此操作。

<a id="merge-requests-as-a-triage-surface"></a>

## 將合併請求作為分診介面

**將 MR 作為請求介面：否。** _(若此儲存庫將外部合併請求視為功能請求，請設為 `yes`；`/triage` 會讀取此旗標。)_

當設為 `yes` 時，MR 會經過與問題相同的標籤與狀態流程，使用 `glab mr` 的對應專案：

- **讀取 MR**：`glab mr view <number> --comments`，以及 `glab mr diff <number>` 以檢視 diff。
- **列出外部 MR 以供分診**：`glab mr list -F json`，然後僅保留作者非專案成員或擁有者的 MR（即貢獻者的 MR，而非維護者進行中的工作）。
- **留言 / 標籤 / 關閉**：`glab mr note`、`glab mr update --label`/`--unlabel`、`glab mr close`。

與 GitHub 不同，GitLab 對問題和 MR 使用獨立的編號，因此一旦知道維護者指的是哪個介面，`#42` 便不會產生歧義。

<a id="when-a-skill-says-publish-to-the-issue-tracker"></a>

## 當 skill 說「發布到問題追蹤器」時

建立一個 GitLab 問題。

<a id="when-a-skill-says-fetch-the-relevant-ticket"></a>

## 當 skill 說「取得相關工單」時

執行 `glab issue view <number> --comments`。

<a id="wayfinding-operations"></a>

## 導航操作

由 `/wayfinder` 使用。**地圖** 是一個單一問題，其 **子** 問題作為工單。

- **地圖**：一個標記為 `wayfinder:map` 的單一問題，包含 Notes / Decisions-so-far / Fog 內容。`glab issue create --label wayfinder:map`。（在支援原生 epics 的 GitLab 層級中，epic 可以取代地圖；但標記問題在所有環境中皆可用。）
- **子工單**：一個在描述頂部包含 `Part of #<map>`，並帶有 `wayfinder:<type>` 標籤（`research`/`prototype`/`grilling`/`task`）的問題。一旦被領取，該工單即指派給負責開發者。
- **阻擋**：GitLab 的 **原生阻擋連結**，是標準且 UI 可見的表示方式。使用 `/blocked_by #<n>` 快速操作新增，並以 note 形式發布（`glab issue note <child> --message "/blocked_by #<blocker>"`）。原生阻擋連結是 Premium/Ultimate 功能；在免費層級（或不可用時），請改在描述頂部使用 `Blocked by: #<n>, #<n>` 行。當所有阻擋專案都關閉時，工單即解除阻擋。
- **前沿查詢**：`glab issue list -O json` 限定於地圖的子問題，排除任何有未關閉阻擋專案的工單：即指向未關閉問題的原生 `blocked_by` 連結（`glab api projects/:id/issues/<child-iid>/links`），或 `Blocked by` 行中的未關閉問題，或已指派者；依地圖順序，第一個符合者為勝。
- **領取**：`glab issue update <n> --assignee @me`，即該 session 的第一次寫入。
- **解決**：`glab issue note <n> --message "<answer>"`，接著 `glab issue close <n>`，然後在地圖的 Decisions-so-far 中附加上下文指標（gist + 連結）。
