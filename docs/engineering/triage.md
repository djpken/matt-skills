<a id="what-it-does"></a>

## 功能說明

`triage` 會處理專案追蹤器上的問題。它會將每個問題透過一個小型狀態機，依序經過**分診角色**（一個類別角色與一個狀態角色）。每個問題最終會成為一份可供 agent 執行的簡報、一個提問給回報者的具體問題，或是一個已關閉且記錄了原因的問題。

這僅適用於**非您建立**的問題。這意味著原始的錯誤回報、新進的功能請求，或是未經預告而到達的外部 pull request：這些是從外部進入追蹤器的工作，無論回報者留下的是什麼樣貌。[Tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 來自 [to-tickets](https://aihero.dev/skills-to-tickets) 已經是可供 agent 執行的狀態，因此如果您對它們執行 `triage`，就會浪費工作。規則很簡單：`/triage` 僅適用於新進的問題，不適用於您自己建立的問題。

它與手動貼標籤也不同，因為它會提出建議然後等待。它會給出類別與狀態的判斷及理由，加上它在程式碼庫中發現的內容，並在您指示之前不會套用任何變更。

<a id="when-to-reach-for-it"></a>

## 何時使用

您透過輸入 `/triage` 並用自然語言描述您的需求來呼叫此功能。[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不會自行使用它。範例：「顯示任何需要我注意的事項」、「我們來看看 #42」、「將 #42 移至 ready-for-agent」。

| 您擁有的情況 | 應前往的工具 |
| --- | --- |
| 追蹤器中充滿來自他人的原始回報 | `/triage` |
| 您自己的一個粗略想法，尚未寫下來 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 一個已確定的對話，要轉為 [spec](https://www.aihero.dev/ai-coding-dictionary/spec) | [to-spec](https://aihero.dev/skills-to-spec) |
| 一份要拆分為可供 agent 執行工單的 spec | [to-tickets](https://aihero.dev/skills-to-tickets) |
| 一個已確認的錯誤，需要根本原因分析而非標籤 | [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs) |

<a id="prerequisites"></a>

## 先決條件

`triage` 會讀取和寫入您的問題追蹤器，因此 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必須先設定該追蹤器及其標籤詞彙。以下角色名稱是**標準**的。您追蹤器中的標籤字串可能不同，而設定會提供對應關係。如果您的追蹤器已經精確使用標準名稱，則您無需對應也無需設定。

追蹤器設定也決定外部 pull request 是否算作請求介面，以及誰算作外部人員。該旗標預設為關閉，且設定不再詢問此項。若要將 PR 納入範圍，請在 `docs/agents/issue-tracker.md` 中開啟它。

<a id="the-state-machine"></a>

## 狀態機

每個已分診的專案最終都恰好擁有一個類別角色和一個狀態角色。有兩個類別：`bug`（某處壞掉了）和 `enhancement`（新功能或改進）。有五個狀態：

| 狀態 | 含義 |
| --- | --- |
| `needs-triage` | 您需要評估它。未貼標籤的問題通常會先落在這裡。 |
| `needs-info` | 等待回報者。當他們回覆時，會返回 `needs-triage`。 |
| `ready-for-agent` | 已完全規格化，並附有 agent 簡報。[AFK](https://www.aihero.dev/ai-coding-dictionary/afk) agent 可以接手。 |
| `ready-for-human` | 相同的簡報，加上為什麼 agent 無法執行：需要判斷、外部存取或手動測試。 |
| `wontfix` | 已關閉，並記錄了原因。 |

這就是完整的詞彙。每個專案恰好一個狀態角色的規則讓查詢保持簡單。這些狀態也是 [skill](https://www.aihero.dev/ai-coding-dictionary/skill) 最受歡迎的領域。使用者曾要求一個用於已規格化但被其他問題阻擋的工作的狀態、一個 `deferred` 狀態用於等待未來觸發的工作，以及一個終止 `implemented` 狀態。這些都尚未發布。請參閱下方的問題。

`wontfix` 有三種情況。差異很重要，因為只有一種會寫入知識庫：

| 關閉原因 | 發生什麼事 |
| --- | --- |
| 已實作 | 一條指向該功能已存在位置的註解。不會放入 `.out-of-scope/`，因為這是已建置的功能，而非被拒絕的功能，且在那裡的檔案會破壞去重檢查。 |
| 被拒絕的錯誤 | 禮貌的解釋，然後關閉。 |
| 被拒絕的增強 | `.out-of-scope/` 中的一個檔案，從關閉註解中連結，然後關閉。 |

`.out-of-scope/` 每個被拒絕的**概念**持有一個 markdown 檔案，而非每個問題。每個檔案都是一份簡短的設計檔案，而非資料庫列。它說明被拒絕的內容、原因，並列出所有要求該概念的問題。`triage` 在評估任何內容之前會讀取整個目錄。它依概念而非關鍵字進行匹配，因此「night theme」會匹配 `dark-mode.md`。當它找到匹配時，會向您顯示舊的決定並詢問您是否仍然同意，而不是從頭開始再次辯論該請求。

<a id="verify-before-you-brief"></a>

## 簡報前先驗證

在任何 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 之前，`triage` 會檢查該宣告是否成立。對於錯誤，它會從回報者的步驟中重現它。對於 PR，它會簽出分支並執行相關的測試。然後它會報告三種結果之一：

- 已確認，並附上程式碼路徑。
- 無法重現。
- 細節不足以嘗試。這是最強的 `needs-info` 訊號。

在同一遍中，它會對程式碼庫執行另外兩項檢查。**冗餘**檢查詢問該功能是否已實作，並依領域概念而非回報者的措辭進行搜尋。**先前拒絕**檢查詢問 `.out-of-scope/` 是否已經說不。兩項檢查都很輕量，且任一項命中都會產生 `wontfix`。

所有這些工作都為了讓一個產物變得好：**agent 簡報**。這是 `triage` 在問題移至 `ready-for-agent` 時發布的結構化註解。在 `triage` 發布後，簡報即為合約，而原始回報僅為背景資訊。簡報是**持久**而非精確的，因為問題可能在 `ready-for-agent` 中停留數週，而程式碼會改變。因此簡報會命名型別、簽名和行為合約，絕不使用檔案路徑或行號。已確認的重現比猜測能產生強得多的簡報。

<a id="a-pr-is-an-issue-with-attached-code"></a>

## PR 是附有程式碼的問題

如果追蹤器將外部 pull request 視為請求介面，它們會經過相同的機器，具有相同的類別、狀態和轉換。狀態適用於 diff。`ready-for-agent` 表示已附上簡報，且 agent 應對程式碼採取下一步。`ready-for-human` 表示人員可以合併它。PR 上的簡報描述對現有 diff 還剩什麼要做，而非如何從無到有建置該事物。

發現功能僅顯示*外部* PR，因為協作者的進行中分支不是分診工作。該過濾器僅適用於發現功能。如果您明確指定一個 PR，`triage` 會處理它，無論是由誰撰寫。

<a id="common-questions"></a>

## 常見問題

**我執行了 `/to-spec` 和 `/to-tickets`，現在那些工單就在那裡未分診。我要對它們執行 `/triage` 嗎？**
不會。它們已經處於 agent-ready 狀態。`to-tickets` 在發布時會套用 `ready-for-agent` 標籤，因此 AFK runner 可以直接接手，無需再次處理。遇到這個問題的使用者先前執行過 spec flow，並在輸出中看到 `needs-triage`，但他們的 AFK runner 忽略了所有內容。`triage` 是處理來自外部工作的入口。spec flow 是處理你自己啟動的工作的通道。兩者會在 `ready-for-agent` 匯合，在此之前則各自獨立。

**既然現在有 `to-spec` → `to-tickets` → `implement` 的流程，`triage` 還有意義嗎？**
只有在你有進站工作時才有。`triage` 比該流程更早出現，且執行不同的任務：它處理其他人提交的報告。如果你追蹤器中的所有內容都來自你自己的規劃，你很少會使用它。如果你維護任何公開專案，或你的團隊向你提交 bug，那就是這些工作的起點。主要用途是接收外部貢獻者 issue 的開源儲存庫。

**Agent 嘗試套用 `ready-for-agent`，但 `gh` 顯示標籤不存在。**
這是一個已知的未修復 bug（[#616](https://github.com/mattpocock/skills/issues/616)）。`setup-matt-pocock-skills` 會將標籤詞彙寫入 `docs/agents/triage-labels.md`，但不會在你的追蹤器中建立這些標籤。請自行使用 `gh label create` 或追蹤器的 UI，一次性建立五個狀態標籤和兩個類別標籤，錯誤就會停止。該 issue 連結到一個尚未合併的社群修復分支。

**五個狀態不夠。那 blocked、deferred 或 implemented 怎麼辦？**
這是該 skill 被回報最多的缺失。它有三種形式：

- 一個已完全規範化但需等待另一個 issue 關閉的 issue（[#139](https://github.com/mattpocock/skills/issues/139)）。回報者表示在那裡 `ready-for-agent` 是「技術上正確」但具有誤導性，導致 agent 接手後卡住。
- 預定的未來工作，需等待觸發條件，因此目前尚不可執行（[#297](https://github.com/mattpocock/skills/issues/297)）。
- 一個用於「已實作，等待驗證」的終止狀態。沒有它，AFK runner 可能會再次排隊已完成的工單。

blocked 情況被認為是真實存在的，但名稱尚未決定（`blocked` 對 `paused`）。這些都尚未發布。作為變通方法，人們會在類別旁邊新增一個儲存庫本地的額外標籤。這樣狀態列位就能持有準確的值，但 skill 不知道這個額外標籤。某個社群 fork 走得更遠，新增了 `needs-slicing`、`tracking` 和 effort 標籤。這可行，但它屬於該 fork，而非 skill 本身。

**這與 `/diagnosing-bugs` 有何不同？**
此處的驗證步驟刻意保持淺層。它回答「這是否真實，以及大致位於哪裡」，而不尋找根本原因。如果 bug 無法在幾分鐘內根據回報者的步驟重現，請使用 `needs-info`，或如果你現在想調查，請使用 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs)。目前兩個 skill 的文字都未提及對方。有使用者回報了這個缺口，且該 issue 仍未關閉。

**我可以將它指向我的整個 backlog 並讓它執行嗎？**
你可以要求，但請注意它讀取的內容。「顯示需要關注的內容」這一步是針對*選擇*的低成本列表。你選擇一個 issue，然後 `triage` 會收集該 issue 的完整 [context](https://www.aihero.dev/ai-coding-dictionary/context)。如果你一次對二十個 issue 執行，agent 可能會將該低成本列表作為唯一證據使用，而不告知你。該列表返回 issue 主體但不包含評論。有一位使用者正好遇到這個問題。三個 issue 已有評論說「已修復，建議關閉」，但這三個都獲得了新的 agent brief。對於批次處理，請明確說明它必須讀取每個 issue 的評論。

**它能與 Linear 或其他非 GitHub Issues 的工具配合使用嗎？**
可以。追蹤器是設定，而非硬編碼的假設。人們透過 `linear` CLI 對 Linear、GitLab 以及 `.scratch/` 下的純 markdown 檔案執行它。常見的分工是：Linear 用於 issue 和規劃，GitHub 用於程式碼和 PR。說「issue tracker」的 skill 會對應到 Linear，說「PR」的 skill 會對應到 GitHub。本地 markdown 追蹤器有一個未修復的範本 bug：產生的檔案可能包含兩次驗收標準，一次在頂層，一次在 agent brief 內（[#200](https://github.com/mattpocock/skills/issues/200)）。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示它運作正常

- 它處理的每個專案最終都恰好具有一個類別角色和一個狀態角色，絕非零個，也絕非兩個衝突的狀態。
- 它提供帶有推理的建議並停止。它不會重新標記 issue 然後繼續。
- 在內容到達 `ready-for-agent` 之前，它已重現 bug，或簽出並執行了 PR。
- 它撰寫的 brief 會指明型別和行為，且不包含任何檔案路徑或行號。
- 當你六個月前拒絕的請求再次出現時，它會告知你並引用舊原因，而不是再次進行 triage。
- 它發布的每則評論都以 `> *This was generated by AI during triage.*` 開頭

<a id="where-it-fits"></a>

## 適用位置

`triage` 是一個**入口**，而非主鏈中的一個步驟。主流程從你的一個想法開始（grill、spec、tickets、implement、review）。`triage` 是處理來自他人工作的平行通道。兩條通道最終都到達同一個地方：一個標有 `ready-for-agent` 標籤並附有 brief 的 issue。[implement](https://aihero.dev/skills-implement) 以與它從 [to-tickets](https://aihero.dev/skills-to-tickets) 接手工單相同的方式接手該 issue。當請求在 `triage` 能撰寫 brief 之前需要更多細節時，`triage` 會同時執行 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling)，一次一輪問題，以便在你做出決定時將決策記錄在 `GLOSSARY.md` 和 ADR 中。當你不確定自己處於哪條通道時，[ask-matt](https://aihero.dev/skills-ask-matt) 會為你導航。
