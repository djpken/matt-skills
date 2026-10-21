<a id="issue-tracker-integrations-github-gitlab-and-local-markdown-only"></a>

# 問題追蹤器整合：僅支援 GitHub、GitLab 和本地 markdown

`setup-matt-pocock-skills` 為 `skills/engineering/setup-matt-pocock-skills/` 中的每個種子範本提供恰好三個問題追蹤器的一級支援：

- **GitHub**（`issue-tracker-github.md`，透過 `gh`）
- **GitLab**（`issue-tracker-gitlab.md`，透過 `glab`）
- **本地 markdown**（`issue-tracker-local.md`，位於 `.scratch/` 下的檔案）

要求為任何其他追蹤器新增一級後端的功能不在範圍內。這包括 Jira、Linear、Azure DevOps、Trello、YouTrack，以及新的以 agent 為中心的追蹤器。

<a id="why-this-is-out-of-scope"></a>

## 為何此功能不在範圍內

每個問題追蹤器後端都會將 CLI 結構硬編碼到 skills 中（指令、旗標、輸出解析）。每個新後端都是永久性的維護負擔，因為它必須隨著工具的 CLI 演進而持續運作，並必須持續針對 `/to-spec`、`/to-tickets`、`/triage` 等工具進行測試。我使用 GitHub，因此那是我實際能保持正確的後端。GitLab 的結構足夠接近，可以順帶支援，而本地 markdown 則完全不需要 CLI。

流行度不會改變這一點。Jira 被廣泛使用，並擁有官方 CLI（`acli`），但它仍然不會獲得範本：它是一個我不執行的後端，具有自己的連結語意、工作流程狀態和 CLI 怪癖，需要持續測試。無論該工具是否主流，成本都由我承擔。

所有其他追蹤器都透過 `/setup-matt-pocock-skills` 中的 **Other** 選項處理：你用一段文字描述你的工作流程，skill 會將其記錄為 `docs/agents/issue-tracker.md` 中的散文。該檔案屬於你。如果你已經完善了一個可行的 Jira 或 Linear 工作流程，請將其保留在你自己的儲存庫中或發布出來，並讓設定指向它。

<a id="prior-requests"></a>

## 先前的請求

- #99：「新增 dex 作為問題追蹤器後端」（提出請求時，dex 約有 3 個月歷史，約 300 顆星）
- #258：「透過 Rovo MCP 新增 Jira 作為一級問題追蹤器」
- #1137：「透過 Atlassian 官方 CLI（`acli`）新增 Jira 作為一級問題追蹤器」
