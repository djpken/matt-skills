<a id="what-it-does"></a>

## 功能說明

`to-tickets` 會接收一份計畫、[spec](https://www.aihero.dev/ai-coding-dictionary/spec)，或您目前的對話內容，並將其拆解為您問題追蹤器上的一系列 **[tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)**。每張 ticket 都會宣告其 **blocking edges**：即必須先完成才能開始此 ticket 的其他 ticket。

每張 ticket 都是一顆 **tracer bullet**：一條狹窄但完整的變更路徑，貫穿所有層級（schema、API、UI、tests），一旦完成即可獨立進行示範。這正是該 skill 與明顯的工作拆分方式（逐層切割並在最後整合）的不同之處。它也會將每張 ticket 的大小調整至適合單一全新的 [context window](https://www.aihero.dev/ai-coding-dictionary/context-window)，因為一個從未見過您 spec 的 [session](https://www.aihero.dev/ai-coding-dictionary/session) 也能接手該 ticket。

<a id="when-to-reach-for-it"></a>

## 適用時機

您透過輸入 `/to-tickets` 來呼叫此功能。[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不會自行主動使用它。

| 您目前的狀態 | 應執行的指令 |
| --- | --- |
| 您有一個 spec issue，且建置跨越多個 session | `/to-tickets`，或 `/to-tickets #<spec_issue>` |
| 計畫僅存在於對話中，從未書面化 | `/to-tickets` 會直接讀取對話串，無需 spec |
| 整個變更適合單一 context window | [implement](https://aihero.dev/skills-implement)，跳過 tickets |
| 尚未決定任何事項 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，然後 [to-spec](https://aihero.dev/skills-to-spec) |
| [wayfinder](https://aihero.dev/skills-wayfinder) 地圖已清除 | 先執行 [to-spec](https://aihero.dev/skills-to-spec) 以收合地圖，然後執行 `/to-tickets` |

由 `to-tickets` 產生的 tickets 在結構上已準備好供 agent 使用。請勿對它們執行 [triage](https://aihero.dev/skills-triage)。Triage 是用於處理來自他人的工作。

<a id="prerequisites"></a>

## 先決條件

`to-tickets` 會發布到追蹤器，因此 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必須已為此儲存庫設定一個追蹤器，以及 triage-label 詞彙。兩種型別皆可：像是 GitHub 或 Linear 的實際追蹤器，或位於 `.scratch/` 下的本地 markdown 檔案，後者無需額外設定即可運作。

<a id="tracer-bullets-not-layers"></a>

## Tracer bullets，而非分層

**水平**切片會發布變更的一層。在每一層都完成之前，什麼都無法運作，且每張 ticket 的驗收標準必須涉及由其他 ticket 負責的工作。**垂直**切片（即 tracer bullet）會一次發布一條貫穿所有層級的細薄路徑，因此它可以獨立驗證，並擁有其評分所涵蓋的所有內容。

這是人們最常違反的規則。有一個團隊執行了一個由 26 張 ticket 組成、按層級切片（corpus、producer、aggregator、selector）的堆疊，結果每張已關閉的 ticket 大約需要 20 次 agent 執行，其中約四分之三是返工。他們自己的事後分析將所有失敗類別都歸因於水平切片，而非實作本身。

在發布任何內容之前，會發生兩件事。`to-tickets` 會尋找 prefactoring（遵循「先讓變更變得容易，再執行容易的變更」原則），並優先安排該工作。然後它會將拆解結果呈現為編號清單，並就此向您提問：粒度是否正確、blocking edges 是否真實、是否有任何專案應合併或拆分。在您核准之前，任何內容都不會進入追蹤器，而該提問環節正是您提出異議的地方。

## Blocking edges

Edges 是該產出物的核心。根據追蹤器的不同，它們以兩種方式運作：

| 追蹤器 | Edges 存放位置 | 運作方式 |
| --- | --- | --- |
| 本地 markdown | `.scratch/<feature>/issues/<NN>-<slug>.md` 下每張 ticket 一個檔案中的文字，編號且 blockers 優先 | 由上至下，手動處理 |
| 實際追蹤器（GitHub、Linear） | 原生 blocking 連結，或追蹤器支援時的子 issue | 任何 blockers 已完成的 ticket 都在 **frontier** 上，可以被領取 |

無論如何，edges 都存放在 ticket 中。追蹤器僅決定是否有任何東西可以並行處理它們。`to-tickets` 負責產生產出物；執行它（一次一個 session，或一支艦隊）是您的工作，而非 skill 的工作。

<a id="the-wide-refactor-exception"></a>

## 大型重構例外

有一種形態會打破 tracer-bullet 規則。**大型重構**（wide refactor）是單一機械式變更（例如重新命名欄位、重新定義共用符號），其 **影響範圍**（blast radius）涵蓋整個程式碼庫。一次編輯會破壞數千個呼叫點，因此沒有垂直切片能成功落地並保持綠色狀態。

`to-tickets` 會將其排序為 **expand–contract** 模式：

- **Expand**：在舊形式旁邊新增新形式，以確保不會破壞任何現有功能。
- **Migrate**：根據影響範圍（每個 package、每個目錄）分批將呼叫點遷移過來，每批一張 ticket，每張 ticket 都受 expand 阻擋。CI 保持綠色，因為舊形式仍然存在。
- **Contract**：當沒有剩餘呼叫者時，刪除舊形式，這是一張受所有 migrate 批次阻擋的 ticket。

即使批次本身無法獨立保持綠色狀態，它們也會共用一個整合分支，並共同阻擋最後一張 integrate-and-verify ticket。CI 只需在那張 ticket 上保持綠色即可。

<a id="common-questions"></a>

## 常見問題

**它為一個三行的變更產生了十二張 tickets。**
過度拆解是此 skill 最常被報告的問題，許多使用者都見過。[model](https://www.aihero.dev/ai-coding-dictionary/model) 預設使用原子單位，並失去了使這些單位有意義的群組結構。提問步驟是修正此問題的地方。要求它合併 tickets，它就會執行。此外還有下限。如果整個變更適合單一 context window，您根本不需要此 skill。直接前往 [implement](https://aihero.dev/skills-implement)。

**Tickets 產出時每層一張：所有 schema 在一張，所有 API 在另一張。**
這是垂直切片規則旨在防止的失敗模式，但該 skill 有時仍會產生這種情況。在提問步驟中，透過對每張 ticket 提問一個問題來捕捉它：當這張 ticket 完成時，我可以示範什麼？沒有答案的 ticket 就是水平切片。有些人因此會在每張 ticket 中加入一行「demo path」，並報告說這能引導 model 朝向垂直切片。

**在 GitHub 上，tickets 未建立為 spec issue 的子 issue。**
這在十幾次執行和多個 model 中都有報告，[最完整的報告在 issue #554](https://github.com/mattpocock/skills/issues/554)。已修復：`to-tickets` 現在會將每張 ticket 建立為其來源 issue 的子 issue，且 GitHub 追蹤器範本提供了指令（`gh issue create --parent <n>`，`gh` 2.94+）。重新執行 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 以更新較舊的 `docs/agents/issue-tracker.md`。

**「Blocked by」被寫入 issue 主體，而非使用真正的 blocking 連結。**
這是同一類問題，[報告於 issue #513](https://github.com/mattpocock/skills/issues/513)，其中 agent 甚至宣告 GitHub 根本沒有原生 blocking 關係。但它有：`gh issue create --blocked-by 12,15`。由於該 skill 優先發布 blockers，它們的編號在建立時總是可用的。主體文字意圖作為沒有原生 edge 的追蹤器的備選方案，而非預設值。Ticket 範本現在在 edges 為原生時會省略 `## Blocked by`。

**本地 tickets 放在哪裡？v1.1 筆記提到根目錄下的 `tickets.md`。**
確實如此，而且這是一個 bug。單一共享檔案也會在並行 agent 寫入時造成競態條件。Local mode 現在會在 `.scratch/<feature-slug>/issues/<NN>-<slug>.md` 下依依賴順序為每個 ticket 寫入一個檔案，這與 local tracker 範本已描述的結構相符。`NN` 字首是真實的 ticket ID，因此 `/implement 03` 可以運作，無需重新輸入長標題。

**當它嘗試讀取我的 spec 時，內容一直被截斷。**
非常大的 spec 可能超出 tracker issue 能乾淨回傳的範圍。由於沒有本機副本可供備援，agent 會花費 [tool calls](https://www.aihero.dev/ai-coding-dictionary/tool-call) 反覆擷取片段，卻永遠無法讀到結尾。請勿在 `/to-spec` 與 `/to-tickets` 之間執行 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) 或 [compact](https://www.aihero.dev/ai-coding-dictionary/compaction)。請在相同的 context window 中執行它們，這樣 agent 就不必重新擷取 spec。

**驗收標準什麼都沒評分：有些標準在尚未進行任何工作前就已透過。**
範本要求提供標準，但未說明這些標準是否可能失敗，因此會出現這種情況。有三種常見形態：在 base commit 時已為真的標準、只能由其他 ticket 的工作滿足的標準，以及重述請求而非從產出物推匯出來的標準。垂直切片可防止大多數情況，因為交付新行為的切片在 base commit 時依設計就會失敗。手動檢查仍然值得執行。對於每個標準，請指出能顯示其為假的觀察結果，並確認它在實作者開始的 commit 上會失敗。

**Tickets 已發布。我實際該如何執行它們？**
該 skill 止於產出物，且沒有自動排程模式。排程是手動的：檢視看板，計算沒有未解決阻擋項的 ticket 數量，並開啟相同數量的 agent 工作階段。為每個 ticket 提供全新的 context，並在它們之間清除。[implement](https://aihero.dev/skills-implement) 在 GitHub 或本機 markdown 中完成時，並不能可靠地關閉或勾選 ticket，因此您需要自行更新 ticket 的狀態。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示運作正常

- 每個 ticket 都有「完成時我可以示範什麼？」的答案，且答案是行為，而非某一層。
- 清單會以編號形式回傳給您，每個專案都附有「Blocked by」行，這發生在任何內容發布之前。
- 最上方的 ticket 沒有阻擋項，可以立即開始。
- Ticket 內容中沒有任何檔案路徑或行號，原型產生的片段除外。
- 每個 ticket 讀起來都像是一個全新工作階段可以在您不在場時完成的工作。
- 如果發現任何預先重構，它們會排在順序的前端，而不是混入功能 ticket 中。

<a id="where-it-fits"></a>

## 適用位置

`to-tickets` 是主要建置鏈中的一個步驟：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

上游是 [to-spec](https://aihero.dev/skills-to-spec)，它提供一個已確定的 spec 供切片使用。請將兩者保持在同一個 context window 中，中間不要清除。下游是 [implement](https://aihero.dev/skills-implement)，它為每個全新工作階段建置一個 ticket，驅動 [tdd](https://aihero.dev/skills-tdd) 進行測試，並以 [code-review](https://aihero.dev/skills-code-review) 收尾。[implement-spec](https://aihero.dev/skills-implement-spec) 是另一種下游方式。它將相同的阻擋邊視為任務圖，並在單一整合分支上並行建置所有就緒的 ticket。當您不確定哪個 skill 或流程適用時，[ask-matt](https://aihero.dev/skills-ask-matt) 會為您導向。
