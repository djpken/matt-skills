<a id="what-it-does"></a>

## 功能說明

`grilling` 是一個訪談迴圈，用於在任何人採取行動之前，對計畫、決策或想法進行壓力測試。它將主題對映為一棵**設計樹**：每個決策都會分支出依附於它的其他決策，並逐分支地訪談您，直到沒有假設仍未被明確陳述為止。

它不會一次只問一個問題，也不會一次問完所有問題。每個**輪次**會詢問整個**前沿**：即所有前置條件已確定的決策，且不包含其他內容。如果兩個問題中有一個依賴另一個，它們絕不會出現在同一輪次中；一個取決於尚未確定答案的問題，屬於後續的輪次。您的回答會確定決策，前沿會向外擴充套件，下一輪次會詢問因此解鎖的內容。通常，十三個問題大約會在三個輪次內完成，而不是十三個輪次。

<a id="when-to-reach-for-it"></a>

## 何時使用

輸入 `/grilling`，或 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 會在任務符合條件時自行呼叫它。它是 grilling 家族中唯一由模型呼叫的 [skill](https://www.aihero.dev/ai-coding-dictionary/skill)，因此您很少需要手動輸入。通常，您*確實*輸入的某個 skill 會為您執行它。

直接輸入 `/grilling` 會得到純訪談，沒有其他內容。如果您想要更多功能：

| 您擁有的情況 | 使用 |
| --- | --- |
| 您不在工作目錄中 | [grill-me](https://aihero.dev/skills-grill-me)：相同的 [session](https://www.aihero.dev/ai-coding-dictionary/session)，但使用一個 agent 不會自行觸發的名稱 |
| 您在工作目錄中 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs)：相同的 session，並在過程中寫入 `GLOSSARY.md` 和 ADR |
| 一個大到無法在單一 session 中處理的工作量 | [wayfinder](https://aihero.dev/skills-wayfinder)：它繪製地圖並在決策工單中執行 grilling |
| 一個無法透過討論解決的問題：某事物應該看起來或感覺起來如何 | [prototype](https://aihero.dev/skills-prototype)：建立一次性版本，然後回來 |
| 您自己的 skill 需要訪談 | 從中呼叫 `/grilling`，而不是撰寫另一個訪談 |

<a id="the-round-the-frontier-and-who-decides"></a>

## 輪次、前沿與決策者

此 skill 基於三個概念。

**設計樹**是主題的模型：決策以及依附於它們的決策。**前沿**是所有前置條件均已確定的決策集合：即 agent 目前唯一可以詢問的問題。**輪次**是一個前沿，完整詢問並完整回答。

在輪次內部，每個問題都有固定的格式：在 `❓` 之後是編號和標題，然後是正文，最後是 agent 的建議答案單獨佔一行 `➡️`。此格式讓您可以透過編號回答一個輪次（例如「1 是，2 第二個選項，3 否，原因如下」），而不是引用問題來回答。此格式有一個已知問題。建議有時會*反對*問題原本的措辭，因此同意建議意味著對問題回答「否」。當這種情況發生時，請回答建議並說明這一點。

設計的另一半是事實與決策的分離。事實是 skill 自己的職責。當前沿問題需要 [environment](https://www.aihero.dev/ai-coding-dictionary/environment) 可以確定的資訊時，agent 會派遣一個 [sub-agent](https://www.aihero.dev/ai-coding-dictionary/subagent) 去查明，而不是詢問您。輪次不會等待該研究；只有依賴該研究的問題才會等待。決策屬於您，agent 必須等待您的決定。執行 `grilling` 的 agent 如果自行回答自己的決策，就破壞了 skill。當前沿為空時，session 結束。然後 agent 會等待您確認共同理解，才對您同意的事項採取行動。

前沿有一個限制。agent 透過判斷力選擇它；它不會從圖中計算出來。因此，它可能在同一輪次中放入兩個問題，後來才發現一個答案本應改變另一個。唯一的防護措施是告訴它。這會在下一輪次中重新開啟受影響的分支。

<a id="what-lives-here-and-what-lives-in-the-wrappers"></a>

## 此處的內容與包裝器中的內容

此頁面涵蓋機制。包裝器 skill 的頁面回答人們最常問的問題。

| 問題 | 回答位置 |
| --- | --- |
| 樹、前沿、輪次、問題格式、事實與決策 | 此處 |
| session 應該執行多久、對於無法透過討論回答的問題該怎麼辦、如何避免隨聲附和 | [grill-me](https://aihero.dev/skills-grill-me) |
| 什麼內容會寫入 `GLOSSARY.md`、什麼會成為 ADR | [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |

<a id="common-questions"></a>

## 常見問題

**我可以回到一次只問一個問題嗎？**
可以，且很大一部分使用者會這樣做。將此內容加入您的全域 `CLAUDE.md`：

```
When grilling, ask one question at a time.
```

並非所有人都偏好基於輪次的預設值。閱讀速度較慢的使用者、使用第二語言工作的使用者，或使用順序格式來幫助集中注意力的使用者，都回報說一次一個問題對他們來說效果更好。選擇退出是一個受支援的設定，而非變通方法。

**`/batch-grill-me` 去哪了？**
移入此 skill。基於輪次的問詢曾短暫地作為一個獨立的 skill 發布，然後移入 `grilling` 本身，因此所有基於該原語構建的功能（`grill-me`、`grill-with-docs`、`triage`、`wayfinder`）都立即獲得了它。沒有 `batch-grill-me` 可安裝，也沒有獨立的順序 skill；上述的 `CLAUDE.md` 行是回到一次一個問題的方式。

**一次詢問整個輪次必然會遺失我先前答案會引發的問題。不是嗎？**
這是對輪次最常見的異議，而前沿回答了它。輪次只包含互不依賴的問題，因此輪次中的任何答案都不會使該輪次中的另一個問題無效。您的答案仍然會改變後續的內容，因為 agent 會從頭開始計算每個下一輪次。您確實會失去一些東西，但比「一次問完所有問題」所暗示的要少。請參閱上述的前沿限制。

**它問完問題後就開始建置了。**
確認閘門就是為此而存在的。當您說理解已共享時，skill 才完成，而不是當前沿變空時。較弱且較快的 [models](https://www.aihero.dev/ai-coding-dictionary/model) 仍會跳過閘門。使用者最常回報此問題發生在低努力或非前沿模型上，這些模型會將「訪談直到共享理解」簡化為幾個問題和一個大綱。如果您的模型這樣做，可靠的修復方法是在您自己的 `AGENTS.md` 或 `CLAUDE.md` 中加入一行，告訴 agent 未經許可不要實作。

**它回答了自己的問題，而不是問我。**
這是執行過程中的 bug，也是 skill 的文字將事實與決策分開的原因。當另一個 skill 執行 `grilling` 來解決 ticket 時，這種情況最常發生，而 agent 會將該任務視為繼續前進的許可。相同的限制也是沒有非同步模式的原因。有人要求一個變體，該變體會讀取 GitHub issue 並發布一份整合的決策備忘錄，但那是另一個 skill，因為沒有人回答的嚴謹審問會產生 agent 的意見，而不是你的意見。

**我可以限制問題數量嗎？**
不可以，而且刻意將限制排除在範圍之外。有些計畫需要三個問題，有些需要五十個；固定的上限要嘛截斷困難案例，要嘛在簡單案例中顯得任意。預設的控制方式是使用普通語言。告訴它結束，或停止並接受目前的計畫。如果 session 執行時間非常長，原因通常是範圍太大；將工作拆分並審問各個部分。

**我單獨安裝了 `grill-me`，但什麼都沒發生。**
`grill-me` 是一個單行 skill，其整個內容是「執行 `/grilling` session」，因此也需要安裝這個 skill。`grill-with-docs` 也是如此，它還需要 [domain-modeling](https://aihero.dev/skills-domain-modeling)。安裝整套可以避免這個問題；選擇性安裝意味著也要安裝基礎元件。

**`grill-with-docs` 執行了，但它從未載入 `grilling`。**
這是一個真實的 bug，尚未修復，使用者在 [harnesses](https://www.aihero.dev/ai-coding-dictionary/harness) 和模型中都有報告。一個命名另一個 skill 的 skill 並不能可靠地導致該 skill 載入，而 `grill-with-docs` 命名了兩個。跡象是一個 session 一次問所有問題且沒有建議。這表示模型正在即興發揮訪談，而不是執行這個 skill。直接詢問 agent 是否載入了 `grilling` 和 `domain-modeling` 通常可以恢復。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示它運作正常

- 一輪以編號清單的形式到達，每個問題及其建議在單獨的 `➡️` 行上，你可以透過編號回答整輪。
- 一輪中的任何問題都不需要先回答同一輪中的另一個問題。
- 後續輪次會問第一輪無法問的問題。
- 它會去查閱事實（讀取檔案、派遣 sub-agent），而不是問你一些它本可以找到的資訊。
- 背景中執行的研究不會使該輪停滯；只有依賴它的問題會等待。
- 它在結束時停止，並要求你確認理解是共通的，而不是開始工作。
- 問題數量保持高，而輪次數量保持低。

<a id="where-it-fits"></a>

## 適用位置

`grilling` 是一個 **primitive**，而不是你排程的步驟：訪談技術的唯一真實來源，保留在一個地方，以便每個需要訪談的 skill 都使用它，而不是發明一個。[grill-me](https://aihero.dev/skills-grill-me) 和 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 是它的兩個使用者呼叫入口，而 `grill-with-docs` 是主要建置鏈的開始，在 [to-spec](https://aihero.dev/skills-to-spec) 之前。[wayfinder](https://aihero.dev/skills-wayfinder) 執行它以解決決策 ticket，[triage](https://aihero.dev/skills-triage) 以審問模糊報告使其可操作，[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 以在你選擇要深入研究的候選者後走過樹。當你不確定哪個入口點適合時，[ask-matt](https://aihero.dev/skills-ask-matt) 會為你導航。
