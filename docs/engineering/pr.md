<a id="what-it-does"></a>

## 功能說明

`pr` 定義了 pull request 內文的結構：一個展示變更內容的 **Summary**、證明其運作正常的 **Evidence**，以及評估合併風險的 **Merge Danger**。這是一份格式參考，而非工作流程。它不會推送分支、開啟 PR，或決定內文內容。它告訴 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 在撰寫內文時應該採用什麼樣式。

Summary 是一種視覺呈現，而非段落文字。預設的 PR 內文會以文字描述 diff。此格式會選擇能清楚呈現重點的**最小檢視**（例如偽程式碼、呼叫樹、元件樹、檔案樹、Mermaid 圖表或格式化 diff），並簡短說明周圍的文字。審查者已經開啟了 diff，因此內文會在他們閱讀之前先展示其結構。

<a id="when-to-reach-for-it"></a>

## 適用時機

輸入 `/pr`，或者當 agent 撰寫 PR 內文時會自動使用它。

| 您的情況 | 使用 |
| --- | --- |
| 分支已準備好，需要一份審查者可以快速瀏覽的內文 | `pr` |
| 程式碼已撰寫完成，但尚無人審查 | 先使用 [code-review](https://aihero.dev/skills-code-review)，然後使用 `pr` |
| PR 已開啟，且審查意見正在返回 | 此集合中目前沒有對應功能；`pr` 僅撰寫內文 |

<a id="the-template"></a>

## 範本

三個部分，依此順序排列：

- **Summary**：一個或多個小型視覺元素，每個都放在其支援的簡短文字旁邊。通常使用一個，有時使用幾個，很少全部使用。僅保留審查者需要的呼叫、檔案、props 和邊界。
- **Evidence**：變更前後對比。當變更涉及視覺效果且 [environment](https://www.aihero.dev/ai-coding-dictionary/environment) 可以截圖時，截圖是最有力的證據。否則，請使用之前失敗但現在透過的具體測試（以偽程式碼形式撰寫），或已變更的控制檯輸出。
- **Merge Danger**：變更屬於**單向門**還是**雙向門**，以及其**影響範圍**。雙向門容易反轉；單向門（例如破壞性遷移、移除公開 API、難以反轉的決策）則不然。影響範圍指出如果變更有誤，哪些部分可能會受損：版面設定位移、API 使用者、行動裝置響應性。

門的判斷是核心概念。它將「這是否安全合併？」從直覺感受轉變為審查者可以反對的明確宣告。它也告訴審查者應將 [human review](https://www.aihero.dev/ai-coding-dictionary/human-review) 花在哪裡。快速瀏覽影響範圍小的雙向門，並仔細閱讀單向門。

<a id="common-questions"></a>

## 常見問題

**我可以信任 agent 自己的門判斷嗎？**

不能盲目信任，而明確宣告正是重點。撰寫變更的 agent 也是評分者，因此當它說「雙向，影響範圍小」時，應保持最大懷疑。Diff 也常隱藏變更是否可反轉。正如一位使用者所言：「回滾 commit 無法取消傳送一批電子郵件」，且被標記的發布在第一次寫入新格式之前仍保持雙向。此 skill 提供定義（破壞性操作和難以反轉的決策屬於單向），而非檢查清單，因此請最仔細檢查 Merge Danger 行。兩點有助於判斷：確保 agent 面前有 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket) 或 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)，而不僅是 diff，並記錄您的儲存庫中始終視為單向的變更（schema 遷移、任何向外發布或刪除的內容），讓 agent 在做出判斷前閱讀這些內容。

**它會替我開啟 PR 嗎？**

不會。`pr` 僅涵蓋內文。[implement](https://aihero.dev/skills-implement) 以提交到當前分支結束。要求開啟 PR 的 skill 或選項（例如 `/to-pr`，或 `implement` 開啟 PR 而非提交）仍是開放的提案。一位使用者的變通方法是在 `implement` 中進行一句話的本地覆蓋，指示其開啟 PR。[implement-spec](https://aihero.dev/skills-implement-spec) 是例外：當您的 issue tracker 透過 PR 關閉工作，或當您要求時，它會開啟草稿 PR。由於 `pr` 是由模型呼叫的，當您要求 agent 開啟 PR 時，它會使用此結構作為內文。

**它不會只是產生另一堆文字和圖表嗎？**

此 skill 旨在防止這種情況，但仍可能發生。使用者對 agent PR 內文的抱怨是一致的：「摘要很長，但我只需要知道變更了什麼、如何驗證、什麼可能損壞，以及是否安全合併。」此 skill 告訴 agent 跳過前言、簡短撰寫文字，並選擇最小檢視，通常是一個視覺元素，很少全部使用。如果您仍然得到一堆圖表，表示 agent 忽略了這一點。如果內文巨大是因為 diff 巨大，問題在於 PR 的大小，`pr` 不會替您拆分它。

**我的儲存庫已有 PR 範本。哪個優先？**

預設情況下，兩者都不優先。`pr` 有自己的範本，不會尋找 `.github/pull_request_template.md` 或類似內容。多位使用者要求此 skill 使用儲存庫的範本。如果您不採取任何行動，agent 將對同一份檔案有兩個競爭的指令。請在您的儲存庫 agent 檔案中解決此問題，例如填寫儲存庫範本，並在下方放置 Summary、Evidence 和 Merge Danger。

**輸出是 HTML 嗎？為什麼 Mermaid 圖表沒有渲染？**

輸出是 markdown PR 內文，而非 HTML 頁面。GitHub 和 GitLab 會在 PR 描述中渲染 Mermaid 區塊，但終端機不會，因此在 agent 仍在本地向您展示時，圖表看起來像原始文字。使用 CLI harness 的使用者已透過 ASCII Mermaid 渲染器或讓 agent 將 HTML 版本附加到 PR 來解決此問題。Mermaid 是六種檢視之一。呼叫樹、檔案樹或格式化 diff 在任何地方看起來都一樣。

**它能整理返回的審查意見嗎？**

不能。它撰寫內文後就停止。使用者多次要求一種方式來分類來自其他開發人員或審查 bot 的意見（哪些值得處理，哪些不是問題）。此 skill 不執行此功能。

**當 PR 變更時，它會保持內文最新嗎？**

不會。它在某個時間點撰寫內文，而審查期間變更的 PR 會使該內文過時。在大型變更後，請要求 agent 重寫內文。agent 正在再次撰寫 PR 內文，因此會使用相同的結構。

**我的變更沒有 UI。Evidence 該放什麼？**

除了截圖以外的一切。截圖僅在變更涉及視覺效果時才是最有力的證據。對於遷移、背景工作或重構，證據是之前失敗但現在透過的具體測試，或已變更的控制檯輸出。單獨的「測試是綠色的」是一個宣告，而非前後對比。

**它能標記內文是由 LLM 撰寫的嗎？**

並非如此。某位使用者的做法是在儲存庫的 agent 檔案中設定一則常駐指示，要求 agent 撰寫的每個 issue、comment 和 PR 結尾都必須附上揭露宣告。這條規則應放在儲存庫中，以涵蓋 agent 釋出的所有內容，而非放在單一型別檔案的範本中。

<a id="its-working-if"></a>

## 如果符合以下條件，即表示運作正常：

- 僅憑 Summary 視覺內容，在開啟 diff 之前，就能看出 PR 變更了什麼。
- 正文沒有前言：它直接從 Summary 標題開始。
- Evidence 區塊顯示變更前後的狀態，而非宣稱測試透過。
- 每個 PR 都明確指出門（door）與影響範圍（blast radius），而單向門（one-way doors）正是需要放慢腳步的地方。

<a id="where-it-fits"></a>

## 適用情境

`pr` 介於 review 與 retro 之間，適用於建置以 pull request 形式釋出的情況：`to-spec → to-tickets → implement → code-review → pr → retro`。它由模型觸發，因此只要 agent 在該流程鏈之外撰寫 PR 正文，它也會自動觸發。

- [code-review](https://aihero.dev/skills-code-review) 會先執行，因為 PR 正文應描述已經過審查的 diff。
- [implement](https://aihero.dev/skills-implement) 會產生正文所描述的 commits。

[ask-matt](https://aihero.dev/skills-ask-matt) 會在您不確定情境需要哪個 skill 時，跨整個集合進行路由。
