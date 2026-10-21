<a id="what-it-does"></a>

## 功能說明

`to-spec` 會將您剛進行的對話轉化為 **[spec](https://www.aihero.dev/ai-coding-dictionary/spec)**，並將其發布到您的問題追蹤系統中，作為單一 issue。

它不會向您提問。當您使用此功能時，決策已經完成。因此，它會綜合已知資訊（來自對話串、程式碼庫、您的 `GLOSSARY.md` 以及 ADR），而不會開啟新一輪的提問。該 spec 記錄的是您已經做出的決策，而非制定新決策的地方。

<a id="when-to-reach-for-it"></a>

## 何時使用

您透過輸入 `/to-spec` 來呼叫此功能；[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不會自行使用它。

當建置規模過大，無法在單一 [session](https://www.aihero.dev/ai-coding-dictionary/session) 中完成，且必須拆分到多個 session 時，請使用此功能。這就是唯一的觸發條件：

| 目前狀態 | 執行指令 |
| --- | --- |
| 尚未做出任何決策 | 先執行 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 已決策，且工作適合單一 [context window](https://www.aihero.dev/ai-coding-dictionary/context-window) | [implement](https://aihero.dev/skills-implement)：略過 spec |
| 已決策，且工作跨越多個 session | `/to-spec`，然後執行 [to-tickets](https://aihero.dev/skills-to-tickets) |
| [wayfinder](https://aihero.dev/skills-wayfinder) 地圖已清除 | `/to-spec #<map_issue>` |

<a id="prerequisites"></a>

## 先決條件

`to-spec` 會將 spec 發布為 issue，因此 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必須先為此儲存庫設定追蹤系統以及 triage-label 詞彙。兩種型別的追蹤系統皆可：像是 GitHub 的實際追蹤系統，或位於 `.scratch/` 下的本機 markdown 檔案，後者無需額外設定即可使用。

<a id="the-spec-is-a-decision-record"></a>

## Spec 是決策記錄

Spec 的存在是因為 context window 會結束。您在 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 期間解決了許多事項：解決方案的形式、您辯論後做出的選擇，以及您刻意拒絕的內容。這些都在一個即將清除的對話中。Spec 會保留這些內容。

因此，spec 不會驗證或決定任何事項。它以專案自身的詞彙記錄您所做的決策，讓新的 session 可以接手工作，而無需您再次解釋。如果 spec 陳述了您從未說過的話，那就是缺陷。

<a id="seams-before-prose"></a>

## 先定義接縫，再撰寫文字

在撰寫任何內容之前，`to-spec` 會勾勒出功能將被測試的 **seams**（接縫），並與您確認。它優先選擇現有的 seams 而非新的，並選擇它能找到的最高層級 seam。對於變更而言，理想的 seam 數量為一個。

其他 skills 稍後會使用這些已同意的 seams。[tdd](https://aihero.dev/skills-tdd) 僅在您事先同意的 seams 上運作。[code-review](https://aihero.dev/skills-code-review) 會根據 spec 審查 diff，因此任何未經同意的 seam 都會顯示為審查發現。這兩個關聯都透過此檔案進行。這就是為什麼您應在此處認真對待 seam 對話，而不要留到實作階段。

<a id="common-questions"></a>

## 常見問題

**`/to-prd` 去哪了？**
這是同一個 skill，在 v1.1 中重新命名。"Spec" 現在是全程使用的唯一術語，舊的 `to-prd` slug 不再有效，因此請以新名稱重新安裝。舊詞彙已被 *spec* 和 *tickets* 這對詞彙取代。Spec 是目的地以及固定它的決策。[tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 是到達該目的地的步驟。如果您改變方向，請刪除未完成的 tickets 並保留 spec。

**為什麼 spec 會獲得 `ready-for-agent` 標籤？我不希望 agent 根據它進行實作。**
該標籤表示「無需進一步 triage」：檔案已足夠完整，可供 agent 使用。它標記的是輸入，而非工作指令。但 [AFK](https://www.aihero.dev/ai-coding-dictionary/afk) agent 在輪詢 `ready-for-agent` 時無法看出這種差異。它們會嘗試在一次執行中建置整個 spec，而不是接手 ticket 切片。這是該 skill 最常被回報的問題。在情況改變之前，請在 AFK agent 的 prompt 中明確排除父級 spec，或在 `/to-tickets` 執行後移除該標籤。

**為什麼不直接從 grilling 到 `/to-tickets`，而略過 spec？**
通常您應該這麼做。Spec 僅在多 session 工作中才值得其步驟。其價值在於 tickets 是可拋棄的，而 spec 不是。每個 ticket 的大小適合一個新的 context window，然後被刪除或關閉，而 spec 則保留為記錄背後推理的唯一地方。在單一 session 變更中，這不會為您帶來任何好處，且您需為額外的綜合步驟付出代價，而 [model](https://www.aihero.dev/ai-coding-dictionary/model) 可能會在此過程中偏離。請從 grilling 直接到 `/implement`。

**我剛完成一個 wayfinder 地圖。我該餵給它什麼？**
給它主要的地圖 issue，`/to-spec #<map_issue>`，而不是個別的決策 tickets。[wayfinder](https://aihero.dev/skills-wayfinder) 產生的是分散在地圖上的決策，而非交付物。`to-spec` 將它們合併為一份您可以據以建置的檔案。如果您將地圖直接迴圈到 `/implement`，就會失去該步驟。

**Spec 是給我審查的，還是隻給 agent 用的？**
主要是給 agent 用的，讀起來也是：完整、密集且充滿參考。請閱讀 seams 和 out-of-scope 部分。在這兩個地方，錯誤的決策現在捕捉起來最便宜，而日後發現時最昂貴。人們確實會抱怨閱讀整份檔案，且沒有摘要模式。但如果 spec 讓您感到驚訝，那表示 grilling 不夠深入；spec 並非太長。

**一旦 tickets 開始，我是否應保持 spec 凍結，還是讓 agent 重寫它？**
沒有任何機制能保持同步。實際上，它是您當時所知內容的快照，並在實作教您新事物的第一次時過時。請在工作發布後將其視為可拋棄的。您的 `GLOSSARY.md` 和 ADR 才是旨在長久儲存的檔案。如果您在實作期間學到應長久儲存的內容，請放在那裡，而不是在編輯過的 spec 中。

**我的工作是一個重構或模組邊界，而非功能。模板適合嗎？**
較不適合，這是已知的限制。模板大量依賴使用者故事，這不適合架構工作。您最終會圍繞著關於介面和不變數的決策，撰寫沒人要求的故事。請改用 implementation-decisions 和 testing-decisions 部分。透過 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 將長久的架構決策記錄為 ADR，而不是放在 spec 中。

**它會檢查追蹤系統中的相關工作，或引用它遵循的 ADR 嗎？**
兩者皆否。它會讀取並遵循其所觸及領域的 ADR，但不會連結它們。它也不會在撰寫前搜尋追蹤系統中重疊的 issues，因此 spec 可能會重複他人已提交的工作，且沒有任何警告。如果該領域繁忙，請先自行搜尋追蹤系統。

**`/to-tickets` 無法讀取我的 spec：它不斷截斷。**
追蹤器中的問題可能無法完整回傳非常大的規格說明，而且也沒有本機副本可供使用。要解決這個問題，請勿在 `/to-spec` 和 `/to-tickets` 之間執行 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) 或 [compact](https://www.aihero.dev/ai-coding-dictionary/compaction)。請在同一個視窗中執行這些操作，這樣 `/to-tickets` 就永遠不需要再次擷取規格說明。

<a id="its-working-if"></a>

## 如果出現以下情況，即表示運作正常

- 它開始撰寫內容，而不是向你提出新一輪的問題。
- 它在撰寫之前先向你展示接縫處，並提出盡可能少的建議。
- 它使用你專案中的術語，而非通用的產品管理模板。
- 你記得自己做出了其中的每一個決策。它沒有為了填滿章節而虛構任何內容。
- 範圍外章節列出了實際存在的事項。你拒絕的那些事項通常是頁面上最有用的內容。

<a id="where-it-fits"></a>

## 適用位置

`to-spec` 是主要建置鏈中的一個步驟，但僅適用於其多工作階段分支：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

在上游，[grill-with-docs](https://aihero.dev/skills-grill-with-docs) 做出此 skill 僅負責記錄的決策，而完成的 [wayfinder](https://aihero.dev/skills-wayfinder) 地圖在此處加入鏈路。在下游，[to-tickets](https://aihero.dev/skills-to-tickets) 將規格說明切割為 tracer-bullet 工單，供 [implement](https://aihero.dev/skills-implement) 進行建置。當你不確定哪個 skill 或流程適用時，[ask-matt](https://aihero.dev/skills-ask-matt) 會為你導向。
