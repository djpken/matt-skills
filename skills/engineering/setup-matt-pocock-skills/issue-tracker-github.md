<a id="issue-tracker-github"></a>

# 問題追蹤器：GitHub

此儲存庫的問題與規格以 GitHub issues 的形式存在。所有操作請使用 `gh` CLI。

<a id="conventions"></a>

## 慣例

- **建立 issue**：`gh issue create --title "..." --body "..."`。多行內容請使用 heredoc。
- **讀取 issue**：`gh issue view <number> --json number,title,body,labels,comments`。
- **列出 issues**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，並加上適當的 `--label` 與 `--state` 篩選器。
- **將 issue 設為父 issue 的子 issue**：`gh issue create --parent <parent> ...`，或事後執行 `gh issue edit <parent> --add-sub-issue <child>`（`gh` 2.94 以上版本）。舊版 `gh`：`gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<child-db-id>`（資料庫 id，如下方 **Blocking** 所述）。若未啟用子 issue，請在子 issue 內容頂端加上 `Part of #<parent>`。
- **在 issue 上留言**：`gh issue comment <number> --body "..."`
- **套用 / 移除標籤**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **關閉**：`gh issue close <number> --comment "..."`

從 `git remote -v` 推斷儲存庫；在 clone 內執行時，`gh` 會自動執行此操作。

<a id="pull-requests-as-a-triage-surface"></a>

## 將 pull requests 作為分診介面

**將 PR 作為請求介面：否。** _（若此儲存庫將外部 PR 視為功能請求，請設為 `yes`；`/triage` 會讀取此旗標。）_

當設為 `yes` 時，PR 會使用與 issues 相同的標籤與狀態，並採用 `gh pr` 的對應專案：

- **讀取 PR**：`gh pr view <number> --comments`，以及用於 diff 的 `gh pr diff <number>`。
- **列出待分診的外部 PR**：`gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`。
- **留言 / 標籤 / 關閉**：`gh pr comment`、`gh pr edit --add-label`/`--remove-label`、`gh pr close`。

GitHub 在 issues 與 PRs 之間共用同一個編號空間，因此單獨的 `#42` 可能是其中任一者：請使用 `gh pr view 42` 解析，並回退至 `gh issue view 42`。

<a id="when-a-skill-says-publish-to-the-issue-tracker"></a>

## 當 skill 說「釋出到問題追蹤器」時

建立一個 GitHub issue。

<a id="when-a-skill-says-fetch-the-relevant-ticket"></a>

## 當 skill 說「取得相關工單」時

如同上方 **讀取 issue** 所述進行讀取。

<a id="wayfinding-operations"></a>

## 導航操作

由 `/wayfinder` 使用。**地圖** 是一個單一 issue，其 **子** issues 作為工單。

- **地圖**：一個標記為 `wayfinder:map` 的單一 issue，包含 Notes / Decisions-so-far / Fog 內容。`gh issue create --label wayfinder:map`。
- **子工單**：一個連結到地圖的 GitHub 子 issue（見 **將 issue 設為父 issue 的子 issue**）。若未啟用子 issue，請將子項加入地圖內容中的任務清單，並在子 issue 內容頂端加上 `Part of #<map>`。標籤：`wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）。一旦被領取，該工單即指派給負責開發者。
- **阻擋**：GitHub 的 **原生 issue 相依性**，是標準且 UI 可見的表示方式。使用 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>` 新增邊，其中 `<blocker-db-id>` 是阻擋者的數值 **資料庫 id**（`gh api repos/<owner>/<repo>/issues/<n> --jq .id`，_而非_ `#number` 或 `node_id`）。GitHub 會回報 `issue_dependencies_summary.blocked_by`（僅限未關閉的阻擋者，即即時閘門）。若相依性不可用，請回退至在子 issue 內容頂端加上 `Blocked by: #<n>, #<n>` 行。當所有阻擋者都關閉時，該工單即解除阻擋。
- **前沿查詢**：列出地圖中未關閉的子項（`gh issue list --state open`，範圍限定於地圖的子 issues / 任務清單），移除任何具有未關閉阻擋者（`issue_dependencies_summary.blocked_by > 0`，或 `Blocked by` 行中的未關閉 issue）或指派者的專案；地圖順序中的第一個專案優先。
- **領取**：`gh issue edit <n> --add-assignee @me`，即該 session 的第一次寫入。
- **解決**：`gh issue comment <n> --body "<answer>"`，接著 `gh issue close <n>`，然後在地圖的 Decisions-so-far 中附加一個情境指標（gist + 連結）。
