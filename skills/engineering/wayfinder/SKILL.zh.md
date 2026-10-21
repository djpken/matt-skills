---
name: wayfinder
description: "將大量工作（超過單一 agent 會話所能容納的範圍）規劃為問題追蹤器上的一張決策工單共享地圖，並逐一解決這些工單，直到通往目的地的路徑變得清晰為止。"
disable-model-invocation: true
---

一個模糊的構想已經出現，它太大，無法在單一 agent 會話中處理，而且籠罩在霧氣之中：從這裡到**目的地**的路徑目前還看不見。導航的重點在於找到那條路，而不是直衝向目的地。此技能會在儲存庫的問題追蹤器上繪製一張**共享地圖**，然後逐一處理其**決策工單**（其解決結果是決策，而非待執行的建置切片），直到路線清晰為止。

目的地會因專案而異，為其命名是繪製地圖的第一步，它會影響每一張工單。它可能是一份用於交接並反覆迭代的規格說明、一個在規劃開始前必須確定的決策，或是一項就地進行的變更，例如資料結構遷移。地圖與領域無關：工程工作、課程內容，任何符合此形態的事物皆可。

<a id="plan-dont-do"></a>

## 規劃，而非執行

Wayfinder 預設為**規劃**模式：每張工單解決一個決策，當路徑清晰且沒有剩餘決策需要處理時，地圖即告完成，此時某人可以開始執行該事項。想要直接執行工作的衝動，通常是你已抵達地圖邊緣、該進行交接的訊號。專案可以在其**備註**中覆寫此行為，將執行工作納入地圖本身，但除非有此設定，否則應產出決策，而非交付物。

<a id="refer-by-name"></a>

## 以名稱指稱

每張地圖和工單都是一個問題，因此它有一個**名稱**：即其標題。在人類閱讀的所有內容中（敘述、地圖的「目前決策」部分），請以該名稱指稱它，絕不使用裸 id、數字或 slug。一牆的 `#42, #43, #44` 難以閱讀；名稱則可一目瞭然。id 和 URL 並不會消失；名稱會包裹其連結，但它們位於名稱_內部_，絕不取代名稱。

<a id="the-map"></a>

## 地圖

地圖是此儲存庫問題追蹤器上的一個單一問題，標記為 `wayfinder:map`，是正規產出物。其工單是地圖的子問題。

地圖是一個**索引**，而非儲存庫。它列出已做出的決策，並指向包含詳細資訊的工單；一個決策只存在於一個地方，即其工單，因此地圖絕不重述決策，僅提供摘要並附上連結。

**地圖、其子工單、阻擋關係以及前沿查詢在物理上存放於哪裡，取決於追蹤器。** 問題追蹤器應該已經提供給你。如果沒有，請告訴使用者執行 `/setup-matt-pocock-skills`。查閱追蹤器檔案的「導航操作」章節，瞭解_此_儲存庫如何表達這些操作。如果未提供追蹤器，預設使用 local-markdown 追蹤器。

<a id="the-map-body"></a>

### 地圖主體

地圖的低解析度全貌，每個會話載入一次。未開啟的工單**不**會列出：它們是未開啟的子問題，可透過查詢找到。

```markdown
## Destination

<what reaching the end of this map looks like: the spec, decision, or change this effort is finding its way to. One or two lines; every session orients to it before choosing a ticket.>

## Notes

<domain; skills every session should consult; standing preferences for this effort>

## Decisions so far

<!-- the index: one line per closed ticket, enough to judge relevance, then zoom the link for the detail the ticket holds -->

- [<closed ticket title>](link): <one-line gist of the answer>

## Not yet specified

<!-- see "Fog of war": in-scope fog you can't ticket yet; graduates as the frontier advances -->

## Out of scope

<!-- see "Out of scope": work ruled beyond the destination; closed, never graduates -->
```

<a id="tickets"></a>

### 工單

每張工單都是地圖的**子問題**；追蹤器的問題 id 是其識別碼。其主體是問題，規模適合單一 100K token 的 agent 會話：

```markdown
## Question

<the decision or investigation this ticket resolves>
```

每張工單都帶有 `wayfinder:<type>` 標籤，為 `research`、`prototype`、`grilling` 或 `task` 之一（見 [工單型別](#ticket-types)）。`wayfinder:` 標籤是地圖及其工單唯一攜帶的標籤，絕不使用如 `ready-for-agent` 等分類標籤：它們是決策，而非實作工作。

會話透過將工單指派給驅動地圖的開發人員來**宣稱**該工單，這必須在**任何工作開始前**進行，以便並行會話跳過它。該指派者_即_為宣稱：未開啟且未指派的工單視為未宣稱。

阻擋使用追蹤器的**原生**相依關係：這至關重要，因為它能在追蹤器自身的 UI 中_視覺化_呈現前沿，讓使用者無需開啟地圖即可看到哪些工單可處理。只有缺乏原生阻擋功能的追蹤器才會退回至主體慣例。當阻擋某工單的所有工單都關閉時，該工單即為**未阻擋**；**前沿**是未開啟、未阻擋且未宣稱的子問題，即已知領域的邊緣。

答案不是主體的一部分；它在解決時記錄（見 [透過地圖工作](#work-through-the-map)）。在解決工單期間建立的資產會從問題中連結，而非貼上。

<a id="ticket-types"></a>

## 工單型別

每張工單要嘛是 **HITL**（人類在環，與代表自己發言的人類_共同_處理），要嘛是 **AFK**，由 agent 單獨驅動。HITL 工單只能透過該即時互動來解決；agent 絕不代替人類的角色（一個回答自己問題的嚴厲 agent 已違反此規則）。

- **研究**（AFK）：閱讀檔案、第三方 API 或本地資源（如知識庫），以揭示決策所等待的事實。由呼叫 Skill 工具並輸入 "research" 的子 agent 解決。當需要目前工作目錄以外的知識時使用。
- **原型**（HITL）：透過呼叫 Skill 工具並輸入 "prototype"，製作一個廉價、粗糙且具體的產出物（大綱、粗略構想、存根或 UI/邏輯程式碼）來反應，以提高討論的保真度。將原型作為資產連結。當「它應該長什麼樣」或「它應該如何運作」是關鍵問題時使用。
- **嚴審**（HITL）：對話。預設情況。務必兩次呼叫 Skill 工具，分別輸入 "grilling" 和 "domain-modeling"。
- **任務**（HITL 或 AFK）：在做出_決策_前必須發生的手動工作：沒有需要決策、原型化或研究的事項，但在完成前討論會被阻擋。註冊服務以評估其 API、設定存取許可權、行動資料以檢視其結構。這是唯一_執行_而非決策的型別，它透過解除決策的阻擋來證明其存在價值，而非透過交付目的地。Agent 在可能的情況下單獨驅動它（AFK）；否則，它會向人類提供精確的檢查清單（HITL）。當工作完成時即解決；答案記錄所完成的工作以及後續工單所依賴的任何結果事實（憑證位置、新 URL、列數）。

<a id="fog-of-war"></a>

## 戰爭迷霧

地圖是_刻意_不完整的：不要繪製你還看不見的東西。在活躍工單之外是**戰爭迷霧**：你能看出即將到來但還無法確定的決策和調查的模糊視野，因為它們取決於尚未解決的問題。解決一張工單會清除其前方的迷霧，將現在可以具體化的內容逐一轉化為新的工單，直到通往目的地的路徑清晰且沒有剩餘工單為止。

地圖上的 **Not yet specified** 區塊是用來記錄該模糊視角的：疑似的問題，以及稍後需要重新檢視的領域。這是通往目的地 _方向_ 上尚未發現的邊界：這裡的一切都在範圍內，只是還不夠清晰，無法建立 ticket。請根據視角的清晰度，以鬆散或完整的方式撰寫；這也同時作為路標，讓協作者瞭解工作方向。

**是霧還是 ticket？** 判斷標準在於你現在能否精確地陳述該問題，_而非_ 你現在能否回答它。

- **建立 ticket 的時機** 是當問題已經清晰時，即使它被阻擋且你目前無法處理。
- **Not yet specified 的時機** 是你還無法如此清晰地陳述時。不要預先將霧切割成 ticket 大小的片段：它比 ticket 更粗略，一旦邊界推進到該處，一個片段可能升級為多個 ticket，或者一個都沒有。

**Not yet specified** 不包含已決定的事項（Decisions so far）、已存在的活躍 ticket，以及範圍外的事項（下一節）。

<a id="out-of-scope"></a>

## 超出範圍

霧只會聚集在 _通往_ 目的地的方向上。目的地決定了範圍，因此超出目的地的工作是 **out of scope**：它不是霧，也不屬於 **Not yet specified**。它在地圖上有自己的 **Out of scope** 區塊：你有意排除在 _本次_ 工作之外的內容。是範圍，而非清晰度，將它歸類於此。

範圍外的工作永遠不會升級（邊界止於目的地），因此只有當目的地被重新繪製時，它才會回來，而且是以新的工作形式，而非延續。

將某事排除在範圍外是一種範圍界定行為，而非路線上的步驟。當一個已存在的 ticket 被發現位於目的地之後（繪圖時範圍界定錯誤，或因解決方案而暴露），請 **關閉它**（已關閉的 ticket 明確不在邊界上），並在 **Out of scope** 區塊留下一行：摘要及其範圍外的原因，連結到該已關閉的 ticket。它不屬於 **Decisions so far**，後者記錄實際走過的路線；範圍邊界不是路線上的步驟。

<a id="invocation"></a>

## 呼叫

兩種模式。無論如何，**每個 session 最多隻解決一個 ticket**，研究類 ticket 除外。

<a id="chart-the-map"></a>

### 繪製地圖

使用者以模糊的想法啟動。

1. **命名目的地。** 呼叫 Skill 工具兩次，分別為 "grilling" 和 "domain-modeling"，以確定這張地圖要尋找的目標：規格、決策或變更。目的地決定了範圍，因此最先確定。
2. **繪製邊界。** 再次進行 grilling，這次採用 **breadth-first**：在整個空間中展開，而非深入任何單一線索，以揭示未決定的事項和現在可採取的第一步。 **如果這沒有揭示任何霧**（通往目的地的路徑已經清晰，整個旅程小到可以在一個 session 內完成），你不需要地圖。停止並詢問使用者希望如何繼續。
3. **建立地圖**（標籤 `wayfinder:map`）：填入 Destination 和 Notes，Decisions-so-far 留空，將霧草繪於 **Not yet specified** 中。
4. **建立現在可以規格的 ticket** 作為地圖的子 issue，然後在 **第二遍** 中連線阻擋邊（issue 需要先有 id 才能互相參考）。在那一遍中也撰寫交叉參考，使用真實的 id：佔位符 `#<n>` 會自動連結到不相關的 issue。連線會將它們排序到邊界和阻擋區；所有你還無法規格的內容都留在霧中：**Not yet specified** 區塊。
5. **啟動研究 subagent。** 對於你剛建立的每個 `research` ticket，啟動一個 subagent，呼叫 Skill 工具並使用 "research" 來並行解決它，在一次性 `research/<name>` 分支上記錄其發現，並從 ticket 指向該分支的上下文指標。推送該分支但不要開啟 PR：它永遠不會被合併。
6. 停止：繪圖是一個 session 的工作；它不手動解決任何問題。

<a id="work-through-the-map"></a>

### 處理地圖

使用者以地圖（URL 或編號）啟動。ticket 是 **可選的**：如果沒有指定，由你選擇下一個決策，而非使用者。

1. 載入 **地圖**：低解析度視角，而非每個 ticket 的內容。
2. 選擇 ticket。如果使用者指定了一個，使用它。否則，按順序選擇第一個邊界 ticket。 **宣告它**：在任何工作開始前，將它指派給自己。
3. 根據其 `wayfinder:<type>` 標籤所命名的型別來解決它（見 [Ticket Types](#ticket-types)）。閱讀標籤，而不只是內容：內容從未陳述型別。 **按需放大**：按需獲取任何相關或已關閉 ticket 的完整內容；呼叫 Skill 工具，使用 `## Notes` 區塊所命名的任何 skills。如果猶豫不決，呼叫 Skill 工具兩次，分別為 "grilling" 和 "domain-modeling"。
4. 記錄解決方案：將答案發布為 **resolution comment**，**關閉** issue，並 **附加上下文指標** 到地圖的 Decisions-so-far。
5. 新增新出現的 ticket（先建立再連線）；將答案使其可規格化的任何霧升級，將每個升級的片段從 **Not yet specified** 中清除，使其僅作為新的 ticket 存在。如果答案揭示某個 ticket（這個或其他）位於目的地之後，請 **將其排除在範圍外**，而非在路線中解決它。如果決策使地圖的其他部分無效，更新或刪除那些 ticket。

使用者可能並行執行未阻擋的 ticket，因此預期其他 session 會同時編輯 tracker。
