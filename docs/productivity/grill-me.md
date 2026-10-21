<a id="what-it-does"></a>

## 功能說明

`grill-me` 會接收一個**模糊的想法**，並透過提問引導你，直到你能對該想法做出承諾為止。你不需要事先擬定詳細計畫才能開始，因為 [session](https://www.aihero.dev/ai-coding-dictionary/session) 的存在目的正是為了產出計畫。它會以**輪次**進行提問。每一輪涵蓋整個**前沿**，也就是所有前提條件已確定的問題。因此，它絕不會詢問任何依賴尚未獲得答案的問題。

它是 **[stateless](https://www.aihero.dev/ai-coding-dictionary/stateless) 的。它不會寫入任何檔案，也不會留下工作區。唯一的結果是你腦中更清晰的想法版本。

<a id="when-to-reach-for-it"></a>

## 何時使用

你透過輸入 `/grill-me` 來啟動此功能；[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不會自行啟動它。請在**新的對話**中啟動，而不是在 agent 已為你撰寫的計畫之上繼續。

一旦你有一個值得認真對待的想法（例如功能、產品方向、商業決策或寫作內容），就應立即使用它，遠早於你弄清其涉及事項之前。模糊不清不是等待的理由，因為該 session 的存在目的正是為了消除模糊。如果你已經能精確描述該事物，就不需要再深入追問。

你希望使用三種追問技巧中的哪一種，取決於你面前的情況：

- **任何事物，任何地方。** 使用 `grill-me`。它不需要儲存庫，也不寫入檔案，且主題不必是程式碼。
- **需要對齊的程式碼庫。** 使用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)。這是相同的訪談，但 [stateful](https://www.aihero.dev/ai-coding-dictionary/stateful)：它會讀取你的程式碼，並將所學內容記錄在 `GLOSSARY.md` 和 ADR 中。
- **規模過大，無法在單一 session 完成。** 使用 [wayfinder](https://aihero.dev/skills-wayfinder)。它會將工作繪製成地圖，並在地圖內執行追問 session。

請關閉 [plan mode](https://www.aihero.dev/ai-coding-dictionary/agent-mode)。Plan mode 會讓 agent 急於產出計畫，而你的需求是讓它持續提問。

<a id="its-a-conversation-not-an-interview"></a>

## 這是對話，而非訪談

該技巧負責提問，但**你**掌握範圍。人們常忽略這一點。這是將想法轉化為決策的 session，與產出自信卻無意義內容的 session 之間的差異。

失敗模式是**被動**：對四十個問題回答「同意、同意、同意」，最後得到一份由 agent 撰寫、你只是點頭同意的計畫。因為過程漫長，感覺很有生產力。但你並未做出任何決策，且結果看起來比實際情況更確定。

主動意味著引導。對細節不足的問題提出反駁。指出範圍正在偏移。回答「我不知道」並認真對待。此技巧協助工程師，但不能取代工程師。結果的品質取決於你回答的品質，而非問題的數量。

相反的錯誤確實存在但較少見：停留在訪談中太久，從未進入程式碼階段。

<a id="grillable-and-ungrillable"></a>

## 可追問與不可追問

有些問題可以透過討論回答。有些則不能，無論如何追問都無法達成。

「一個長表格還是三個頁面？」以及「這個互動應該感覺如何？」是**不可追問**的。你需要有具體物件才能回答這些問題。遇到這類問題時，停止追問。使用 [prototype](https://aihero.dev/skills-prototype) 建立一次性版本，檢視後再回來用一行字回答。

當試圖透過討論解決不可追問的問題時，session 會變得過長。Agent 不斷重述問題，你不斷猜測，範圍隨不確定性擴大。

<a id="its-working-if"></a>

## 運作正常的跡象

- 你對某些內容持異議。沒有你反駁的 session 是你不需要的 session。
- 問題在幾輪內出現，而非長時間一次一個，且後續輪次明顯建立在先前回答之上。
- 你最終到達了意想不到的地方，因為某個問題讓你意識到一個你一直未察覺的決策。
- 結束時，你能向不在場的人辯護每個選擇。

<a id="common-questions"></a>

## 常見問題

**我應該預期多少問題，以及如何知道何時結束？**
計算輪次，而非問題數量。四輪共四十六個問題是普通的 session。當前沿為空時結束：它已遍歷每個分支，沒有留下未明確的假設。

**它問了我兩百個問題。哪裡出錯了？**
通常是範圍過大。先請 agent 將工作分解為較小的部分，然後逐一追問。非常長的 session 也會進入 **[dumb zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone)**，此時 [context window](https://www.aihero.dev/ai-coding-dictionary/context-window) 已滿，導致問題品質下降。

**我可以回到一次問一個問題嗎？**
可以。將此加入你的全域 `CLAUDE.md`：

```
When grilling, ask one question at a time.
```

**如果我真的不知道答案怎麼辦？**
直接說明。「我不知道」是真實的答案，而無法回答的問題通常是原型設計而非猜測的訊號。

**在撰寫規格前，我應該開始新的 session 嗎？**
不需要。Session 的價值在於你剛建立的 [context](https://www.aihero.dev/ai-coding-dictionary/context)。直接將同一對話交給 [to-spec](https://aihero.dev/skills-to-spec)。

**模型重要嗎？**
比大多數技巧更重要。追問依賴 [model](https://www.aihero.dev/ai-coding-dictionary/model) 自身對系統如何故障的知識，因此請使用你最好的模型。實作主要遵循 context，因此較便宜的模型在那裡沒問題。

<a id="where-it-fits"></a>

## 適用位置

`grill-me` 是一個**可獨立執行、適用於任何地方和任何事物**的工具。因為它是 stateless 的，所以具有可攜性。它不需要儲存庫、工作區或設定，也不假設想法與軟體相關。人們用它來做商業決策、寫作，以及決定下一步：任何他們無法獨自清晰思考的事物。

可攜性是與 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 的唯一差異。該技巧執行相同的訪談，但會讀取程式碼庫以進行對齊，並將所學內容記錄為 `GLOSSARY.md` 和 ADR。兩者底層都使用 [grilling](https://aihero.dev/skills-grilling) 技巧。`grill-me` 是使用者啟動的入口點，不保留任何狀態。

如果你追問的內容最終被證明是軟體，你可以將同一對話交給 [to-spec](https://aihero.dev/skills-to-spec) 並繼續進入建置流程（這是一個選項，而非該技巧的目的）。當你不確定哪個流程適合時，[ask-matt](https://aihero.dev/skills-ask-matt) 會為你導向。
