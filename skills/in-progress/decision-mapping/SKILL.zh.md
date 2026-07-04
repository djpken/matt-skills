---
name: decision-mapping
description: 將鬆散想法轉成有順序的調查票券地圖，然後一次推進一張票券直到解決。
disable-model-invocation: true
---

當鬆散想法需要超過一個 agent session 才能形成計畫時，使用這個 skill。它會在 Markdown 檔中建立有狀態的 decision map，並帶使用者依序解決票券中的開放問題；這可能需要原型、研究或討論。

## Decision Map

Decision map 是單一精簡 Markdown 檔，每個規劃工作一份，跟專案一起進 git。它是權威成品：**整張地圖會在每個 session 載入 context**，所以必須保持精簡。票券過程建立的資產應從地圖連結，不要複製進地圖。

### 結構

編號條目（「tickets」），每個條目都有自己的 section，並以編號為 key：

```markdown
## #1: Relational Or Non-Relational Database?
Blocked by: #<ticket-number>, #<ticket-number>
Type: Research | Prototype | Grilling

Question
<question-here>

Answer
<answer-here>
```

每張 ticket 都必須能在一個 100K token agent session 內完成。

## Ticket Types

三種 tickets：

- **Research**：閱讀文件、第三方 API、本地知識庫等資源。產出 Markdown 摘要資產。需要目前工作目錄以外的知識時使用。
- **Prototype**：撰寫 UI 或邏輯程式碼來測試假設，或探索設計空間。使用 `/prototype` skill。產出 prototype 資產。當關鍵問題是「應該長什麼樣」或「應該如何運作」時使用。
- **Grilling**：與 agent 對話。使用 `/grilling` 和 `/domain-modeling` skills。一次問一個問題。這是預設類型。

## Fog of war

地圖在 frontier 之外會刻意保持不完整。你的工作是調查 frontier，依序解決 tickets，並把 frontier 往前推。一個 node 一個 node 地推開 fog of war。到了某個時點，fog of war 應該已經退到足以看清抵達終點的路徑；那時就不需要更多 tickets，decision map 可以視為完成。

## Invocation

這個 skill 有兩種呼叫方式：**bootstrap** 和 **resume**。

### Bootstrap

使用者帶著鬆散想法呼叫。

1. 執行 `/grilling` + `/domain-modeling` session，浮出開放決策。一次問一個問題。
2. 寫一份新的 decision map：大多是 fog，frontier 已識別，容易決定的條目直接內嵌解決。
3. 停下來。建立地圖就是一個 session 的工作；不要同時解 tickets。

### Resume

使用者帶著既有 map 路徑和 ticket 編號呼叫。

1. 將**整張 map**載入 context。
2. 執行 session 解決該 ticket，視需要呼叫 skills。不確定時，使用 `/grilling` 和 `/domain-modeling`。
3. 將本 session 解決的內容記錄到 ticket body。
4. 加入新發現的 tickets（帶正確的 `blocked_by` 邊）。
5. 停下來。如果做出的決策讓 map 其他部分失效，更新或刪除那些 nodes。

## Parallelism

預期其他 agents 可能同時修改 map。

## Skipping Decision Map

很多時候，初始 grilling 會顯示沒有 fog of war：沒有未解 tickets。這時除了實作以外沒事要做。

在這種情況下，應該讓使用者有機會跳過 decision map，因為只有多 session 決策需要被做出時才需要 decision map。如果跳過，建議直接實作，或使用 `/to-prd` 安排多 session 實作。
