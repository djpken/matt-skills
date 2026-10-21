<a id="what-it-does"></a>

## 功能說明

`wayfinder` 處理的工作量超過單一 agent [session](https://www.aihero.dev/ai-coding-dictionary/session) 的負載。這是一個你能說出**目的地**，卻還看不見路線的構想。Wayfinder 會將其繪製為 issue tracker 上共享的**決策工單** **地圖**，然後逐一解決工單，直到路線清晰為止。

它負責規劃，但不負責建置。每個工單都提出一個問題，而答案是一項決策，而非建置的一小部分。當在有人開始建置之前，已無任何待決事項時，地圖即告完成。這條規則將 wayfinder 工單與一般的實作 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket) 區分開來，也是 agent 最常違反的規則。當地圖清空後，wayfinder 會進行交接，不會繼續進入程式碼階段。

<a id="when-to-reach-for-it"></a>

## 何時使用

你透過輸入 `/wayfinder` 來啟動此功能；[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不會自行主動使用它。

這是該系列中最繁重的流程，因此觸發條件較為嚴格。工作量必須大於單一 agent session 所能容納的範圍，且通往目的地的路線必須不明確。區分標準是 session 數量：`/grill-with-docs` 用於單一 session 規劃，`/wayfinder` 用於多 session 規劃。

| 你目前面對的情況 | 應執行的指令 |
| --- | --- |
| 範圍明確的功能，可在一次會議中解決 | [grill-me](https://aihero.dev/skills-grill-me)，若存在程式碼庫則使用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 全新專案，或橫跨多個 session 的建置，且路線仍不明確 | `/wayfinder` |
| 決策已完成的討論串 | [to-spec](https://aihero.dev/skills-to-spec)：直接跳過地圖 |
| 已清空的 wayfinder 地圖 | [to-spec](https://aihero.dev/skills-to-spec)，接著 [to-tickets](https://aihero.dev/skills-to-tickets) 和 [implement](https://aihero.dev/skills-implement) |
| 已變得過大的現有 session | 輸入「hand off to `/wayfinder`」（[handoff](https://aihero.dev/skills-handoff) 既可從地圖進入，也可從地圖退出） |

全新專案並非必要條件。人們常規性地將 wayfinder 用於遺留系統和半成品程式碼庫，在那裡它可能更有用，因為許多迷霧是「這裡目前已經存在什麼」，而非「我們該做什麼」。

<a id="prerequisites"></a>

## 先決條件

地圖及其工單位於儲存庫的 issue tracker 上，因此 wayfinder 需要 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 中的 tracker 設定。該步驟會寫入一個「Wayfinding operations」章節。該章節描述如何在 GitHub、GitLab 或本地 markdown 上表達地圖、其子工單、阻擋邊界以及前沿查詢。Wayfinder 透過 `CLAUDE.md` / `AGENTS.md` 中的指標找到該檔案，而非固定路徑。若未設定 tracker，它會回退到本地 markdown 檔案。

Tracker 承擔實際工作。其原生阻擋連結會在 tracker 自身的 UI 中顯示前沿。在沒有原生相依性連結的 tracker（例如自架的 Gitea）上，wayfinder 會從地圖文字中推斷阻擋項。這可行，但你必須更密切地監督它。

<a id="the-map-the-fog-and-the-frontier"></a>

## 地圖、迷霧與前沿

**地圖**是一個標記為 `wayfinder:map` 的單一 issue，其工單為其子 issue。它是**索引，而非儲存庫**。決策只存在於一個地方，即其工單，而地圖只提供一行摘要和連結。Session 以低解析度載入地圖，並在需要時開啟個別工單。這讓地圖可以持續增長，而無需每個 session 載入其完整歷史。

地圖包含四個章節：

- **目的地。** 此地圖終點的模樣。你必須先命名它，在任何工單存在之前，因為目的地決定了你用來衡量每個工單的範圍。
- **目前的決策。** 每個已關閉的工單一行，每行都附有指向詳細內容位置的連結。
- **尚未指定。** 這是**戰爭迷霧**：你能察覺即將到來但還無法清晰表述的決策。判斷迷霧與工單的標準是，你現在是否能精確陳述問題，而非你能否回答它。當你解決一個工單時，其前方的迷霧會消散，任何現在可以指定的事項都會升級為新工單。
- **範圍之外。** 超出目的地的工作。迷霧只朝目的地方向聚集，因此範圍外的工作保持關閉，永遠不會升級。

**前沿**是開放、未阻擋且未佔用的工單集合（已知領域的邊緣）。Session 在進行任何工作前，會透過將工單指派給自己來佔用該工單。指派者*即*為佔用，因此並行 session 會跳過該工單。Session 以名稱引用工單，絕不使用裸的 `#42`，因為一牆的 issue 編號在敘述中難以閱讀。

<a id="the-four-decision-ticket-types"></a>

## 四種決策工單型別

每個工單都帶有 `wayfinder:<type>` 標籤。每個工單要麼是 **[HITL](https://www.aihero.dev/ai-coding-dictionary/human-in-the-loop)**（與能代表自己發言的人類協作），要麼是 **[AFK](https://www.aihero.dev/ai-coding-dictionary/afk)**（由 agent 單獨驅動）。HITL 工單僅透過即時互動解決。若 agent 自行回答其 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 問題，即違反了規則。

| 型別 | 模式 | 何時使用 | 解決方式 |
| --- | --- | --- | --- |
| `grilling` | HITL | 預設選項。問題可以透過討論解決。 | 在新一個 session 中進行 [grilling](https://aihero.dev/skills-grilling) 加上 [domain-modeling](https://aihero.dev/skills-domain-modeling) |
| `prototype` | HITL | 「這應該長什麼樣」或「這應該如何運作」：討論無法解決的問題。 | [prototype](https://aihero.dev/skills-prototype)，並從工單連結到建置的產物 |
| `research` | AFK | 工作目錄外的事實阻擋了決策。 | 一個 [research](https://aihero.dev/skills-research) [subagent](https://www.aihero.dev/ai-coding-dictionary/subagent)，在繪製地圖時啟動，並在 `research/<name>` 分支上並行執行 |
| `task` | 任一 | 無決策事項，但手動工作阻擋了決策，例如設定存取許可權、註冊服務，或行動資料以檢視其結構。 | agent 在可行時單獨處理，否則為人類提供精確的檢查清單 |

`task` 是唯一*執行*而非決策的型別。它之所以屬於地圖，僅因為它解除了決策的阻擋，絕非因為它交付了目的地的一部分。此型別在實務中最常出錯。Agent 會將其解讀為實作步驟，並開始在地圖內撰寫產品程式碼。

研究是*每個 session 一個工單*的唯一例外。

<a id="common-questions"></a>

## 常見問題

**這與 `/grill-with-docs` 有何不同？我應該從哪個開始？**
這是會話數量，而非專案規模。`/grill-with-docs` 是單一會話規劃；wayfinder 是多會話規劃。如果你能在一次對話中處理完所有內容，grilling 會更便宜且效果更好，而在那種情況下 wayfinder 會更慢且更密集。社群的簡短說法是，只有當工作無法在單一會話中完成時，wayfinder 才有意義。這是目前被問得最多的 wayfinder 問題。人們不斷詢問，是因為技能描述沒有告訴你自己的工作處於這條線上的哪個位置。你自己必須判斷會話數量。

**當它要求提供「目的地」時，是指這個會話的結束，還是所有事情的結束？**
是指整張地圖的終點，而不只是第一個會話。這個問題讀起來有歧義，但 wayfinder 定義上是一個多會話工具，因此僅限於會話範圍的答案永遠沒有意義。典型的目的地包括：一份 [spec](https://www.aihero.dev/ai-coding-dictionary/spec) 以便交接、一個在規劃開始前必須鎖定的決策、一個概念驗證，或一個就地變更，例如資料遷移。

**地圖已清除。wayfinder 不是已經寫好規格並建立工單了嗎？為什麼我還是需要 `/to-spec` 和 `/to-tickets`？**
沒有。Wayfinder 的工單是決策工單，而在地圖關閉時，它們也全都已關閉。剩下的是充滿了連結決策的地圖，這並非建置計畫。[to-spec](https://aihero.dev/skills-to-spec) 會將這些連結的決策合併為一份規格（`/to-spec #<map_issue>`），而 [to-tickets](https://aihero.dev/skills-to-tickets) 會將該規格切分為曳光彈式的實作工單。如果你直接將地圖傳入 [implement](https://aihero.dev/skills-implement)，就會跳過合併步驟並失去連結的細節。只有當工作量被證明較小時，才直接進入實作。有些人執行較短的管線並回報說它有效。多出的兩個步驟會給你一份明確的規格，讓審查者或同事可以閱讀，這在你不是獨自工作時更為重要。

**我的 agent 在 wayfinder 會話中途開始撰寫生產環境程式碼。**
這是使用此技能時回報最多的失敗案例，且技能中確實存在一個導致此問題的真實缺口。你可以在地圖的 **Notes** 中覆蓋 wayfinder 的「只規劃，不執行」預設值。但 agent 會撰寫 Notes，因此限制條件及其豁免條款都存放在一個由受限制 agent 擁有的檔案中。有一位使用者看到 agent 在自己的 Notes 中寫下「此地圖包含執行」。在後續會話中，agent 將該行讀取為許可，並在一個執行的伺服器上進行建置。該技能對於「我指的是預設值」沒有硬性停止機制。在該機製出現之前，請閱讀任何非你自己繪製的地圖上的 Notes，將實作保留在獨立的會話中，並將任何看起來像建置切片的 `wayfinder:task` 視為輸入錯誤。

**我繪製了 27 個工單，但當我進行到第十三個時，剩下的工單已經沒有意義了。**
這個問題是逐字引用自一份使用者報告，其他人也回報了相同的結果。預設情況下，wayfinder 會進行全面規劃。當後續工單依賴的假設被先前工單否決時，地圖就會陷入批評者指控該技能所具有的瀑布式陷阱。有兩件事有助於改善。首先，將地圖範圍限定在一個有界的目的地，而不是整個產品。使用者回報說，範圍限定在一個定義明確的 epic 的地圖，比漫無邊際的「實作 V1」表現更好。目標是發布小型增量，而不是規劃一個非常大的東西。其次，積極使用 [prototype](https://www.aihero.dev/ai-coding-dictionary/prototyping)。因為廉價的具體產出物會在實作依賴不確定性之前將其暴露出來，所以路線能保持最新。Wayfinder 是「原型最大化」，而不是「規劃最大化」。

**我可以並行處理多個工單嗎？**
前沿（frontier）會顯示你可以領取哪些工單，而阻塞邊（blocking edges）在理論上使並行工作變得安全。實際上，一次處理一個工單是更安全的預設值。如果你同時處理兩個 grilling 工單，一個會話可能會問你一個你剛在另一個會話中回答過的問題，因為這些會話之間不共享 [context](https://www.aihero.dev/ai-coding-dictionary/context)。Prototype 工單也有已知的缺口。有一位使用者回報說，一個 agent 建置了三個 UI 變體，自行選擇了一個，並關閉了工單。這個選擇權在你，而該技能目前尚未足夠清楚地說明這一點。如果你確實要並行執行工單，請先自行審查依賴關係圖。

**我必須使用 GitHub Issues 嗎？**
不需要。任何問題追蹤器都可以。GitHub 的支援最好，因為其原生子問題和阻塞關係讓前沿在不需要開啟地圖的情況下即可見。人們也使用 GitLab、Linear、Jira 和本地 markdown。有兩個注意事項。在沒有原生阻塞功能的追蹤器上，wayfinder 會從文字中推斷依賴關係圖，你必須手動修正它。本地 markdown 會將產出物放在你的儲存庫中，這不建議使用，因為儲存在儲存庫中的資料往往會意外地長期保留。開源維護者遇到了相反的問題（公開追蹤器填滿了 agent 產生的規劃工單），他們通常還是選擇本地 markdown。

**Grilling 令人筋疲力盡。每個問題都有三段長。**
這是關於 wayfinder 最尖銳的未解決抱怨，目前還沒有人修復它。一位使用者這樣分析：冗長本身導致決策疲勞，而且長度隱藏了 agent 為什麼要問這個問題，因此隨著地圖增長，你失去了從一個決策到另一個決策的鏈條。這種冗長看起來像是目前 [models](https://www.aihero.dev/ai-coding-dictionary/model) 集合的特性，而不是技能的特性。使用者嘗試兩種緩解措施：較低的 [reasoning effort](https://www.aihero.dev/ai-coding-dictionary/effort)，以及在你全域 `CLAUDE.md` 中的白話指令。無論如何，請預期在這裡需要深入思考。Wayfinder 要求你進行大量思考，這是其主要目的之一，而非缺陷。

**一個我已關閉的決策後來被證明是錯誤的。我應該編輯舊工單還是建立新工單？**
沒有官方指引，且 agent 的預設行為無助於解決問題。它傾向於圍繞錯誤的決策進行設計，而不是挑戰它，因此你必須自行引導它。有效的方法是明確告訴 wayfinder 什麼改變了。然後它會更新地圖、修訂受影響的工單，並在已關閉的工單上留言。你可以在地圖中途從範圍變更中恢復。但如果你*設計*了一個會變化的地圖，那是範圍錯誤的訊號。

**`decision-mapping` 去哪了？**
就是這個 skill。v1.1 將其重新命名為 `wayfinder`，您以 `/wayfinder` 來呼叫它。「決策地圖」是行話，而且也不準確，因為四種 ticket 型別中只有一種本身就是決策。新名稱賦予該 skill 一套一致的詞彙（目的地、戰爭迷霧、邊界、地圖），而不是在頂層再發明一個術語。該單元保留了「decision」一詞：wayfinder ticket 是一張 **decision ticket**，以免人們將其誤讀為實作 ticket。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示它運作正常

- 目的地在建立任何 ticket 之前就已書面記錄並達成共識。
- 每一張未關閉的 ticket 都應讀起來像一個問題。任何讀起來像「建立 X」的 ticket，要麼是型別標註錯誤，要麼屬於地圖下游的範疇。
- 您可以檢視追蹤器，無需開啟地圖即可看出哪些 ticket 可以接手，因為原生阻塞顯示了邊界。
- 一個 session 解決一張 ticket，將答案作為解決評論發布，關閉該 ticket，並在地圖的 *Decisions so far* 部分新增一行。然後它停止。
- **尚未指定** 部分會隨時間縮小。當迷霧升級為 ticket 時，它會離開該部分，不會同時出現在兩個地方。
- 當開場廣度優先的 grill 完全找不到迷霧時，該 skill 會停止並告訴您工作量小到可以跳過地圖。
- 完成地圖的 session 會引導您走向 spec，而不是 pull request。

<a id="where-it-fits"></a>

## 適用位置

`wayfinder` 是一個**情境式入口**，而非預設起點。大多數工作仍然從 grill 主導的 idea → ship 鏈開始。當 idea 大到無法在一個 session 中容納時，您使用 wayfinder。它在 [to-spec](https://aihero.dev/skills-to-spec) 處重新加入該鏈，因為已清空的圖表是移交而非建立。

大部分工作在 wayfinder 排程的其他 skill 中進行。[grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling) 解決預設 ticket 型別，[prototype](https://aihero.dev/skills-prototype) 解決談話無法解決的 ticket，而 [research](https://aihero.dev/skills-research) 作為 subagent 執行，使其閱讀內容不佔用您的 session。[handoff](https://aihero.dev/skills-handoff) 負責工作的進出：從變得過大的對話進入地圖，以及在 session 中途出現支線任務時從地圖移出。對於其他情況，[ask-matt](https://aihero.dev/skills-ask-matt) 會路由到整個集合。
