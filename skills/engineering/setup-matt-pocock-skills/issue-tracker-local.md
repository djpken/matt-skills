<a id="issue-tracker-local-markdown"></a>

# 問題追蹤器：本機 Markdown

此儲存庫的問題與規格以 markdown 檔案形式存放於 `.scratch/`。

<a id="conventions"></a>

## 慣例

- 每個功能對應一個目錄：`.scratch/<feature-slug>/`
- 規格位於 `.scratch/<feature-slug>/spec.md`
- 實作問題採用每張工單一個檔案的方式存放於 `.scratch/<feature-slug>/issues/<NN>-<slug>.md`，編號從 `01` 開始，絕不使用單一合併的工單檔案
- 分級狀態記錄為每個問題檔案頂部附近的 `Status:` 行（角色字串請參見 `triage-labels.md`）
- 留言與對話歷史記錄附加在檔案底部，位於 `## Comments` 標題之下

<a id="when-a-skill-says-publish-to-the-issue-tracker"></a>

## 當 skill 指示「發布至問題追蹤器」時

在 `.scratch/<feature-slug>/` 下建立新檔案（必要時建立目錄）。

<a id="when-a-skill-says-fetch-the-relevant-ticket"></a>

## 當 skill 指示「擷取相關工單」時

讀取參考路徑中的檔案。使用者通常會直接提供路徑或問題編號。

<a id="wayfinding-operations"></a>

## 導航操作

由 `/wayfinder` 使用。**地圖**是一個檔案，每張工單對應一個**子**檔案。

- **地圖**：`.scratch/<effort>/map.md`（Notes / Decisions-so-far / Fog 主體）。
- **子工單**：`.scratch/<effort>/issues/NN-<slug>.md`，編號從 `01` 開始，問題內容位於主體中。`Type:` 行記錄工單型別（`research`/`prototype`/`grilling`/`task`）；`Status:` 行記錄 `claimed`/`resolved`。
- **阻塞**：頂部附近的 `Blocked by: NN, NN` 行。當工單所列出的所有檔案均為 `resolved` 時，該工單即解除阻塞。
- **前沿**：掃描 `.scratch/<effort>/issues/`，找出狀態為開啟、未阻塞且未佔用的檔案；編號最小者優先。
- **佔用**：在任何工作開始前，設定 `Status: claimed` 並儲存。
- **解決**：在 `## Answer` 標題下附加答案，設定 `Status: resolved`，然後在 `map.md` 的地圖 Decisions-so-far 中附加上下文指標（摘要 + 連結）。
