<a id="what-it-does"></a>

## 功能說明

`grill-with-docs` 會就某個計畫或設計向你提問，直到你與 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 對其達成共識為止，並在過程中將術語與關鍵決策寫入你的儲存庫。它執行的是與 [grill-me](https://aihero.dev/skills-grill-me) 相同的訪談流程（一輪提問，然後等待，接著下一輪），但目標指向程式碼庫。

它是 **[stateful](https://www.aihero.dev/ai-coding-dictionary/stateful)**。其他所有 grilling skill 都會將 [session](https://www.aihero.dev/ai-coding-dictionary/session) 留在你的腦中；這個 skill 則會在磁碟上留下檔案。當一個術語被釐清時，skill 會立即將其寫入 `GLOSSARY.md`，而不是在最後批次處理。當一個決策透過三道關卡時，skill 會將其寫成 ADR。這就是全部差異，也是導致人們在使用此 skill 時遇到大多數問題的原因。這些產出物是真實儲存庫中的真實檔案，因此當你預期它們存在時，它們可能缺失；當有多人撰寫時，它們可能產生偏差。

<a id="when-to-reach-for-it"></a>

## 何時使用

你透過輸入 `/grill-with-docs` 來呼叫它，agent 不會自行使用它。

在變更開始時，於儲存庫中，當計畫仍模糊且相關事物的用詞尚未確定時，請使用它。這是單次 session 的工具。你需要哪種 grilling skill 取決於你面前有什麼：

| 你擁有的 | 使用 |
| --- | --- |
| 你完全沒有在 working directory 中工作 | [grill-me](https://aihero.dev/skills-grill-me) |
| 一個儲存庫，以及一個可以在單次 session 中解決的變更 | `grill-with-docs` |
| 一個大到無法在單次 session 中處理的工作（全新專案建置、大型功能） | [wayfinder](https://aihero.dev/skills-wayfinder) |
| 一個完全沒有領域檔案的儲存庫，且沒有特定功能目標 | `grill-with-docs`，針對儲存庫而非變更 |
| 一個決策被阻擋在他人腦中的知識上 | [to-questionnaire](https://aihero.dev/skills-to-questionnaire) |

wayfinder 的區分在於 session 數量：`/grill-with-docs` 用於單次 session 規劃，`/wayfinder` 用於多次 session 規劃。

<a id="prerequisites"></a>

## 先決條件

此 skill 會寫入你的儲存庫，因此你需要在一個安全可寫入的位置。已釐清的術語會進入根目錄的 `GLOSSARY.md` 詞彙表，或進入相關 context 的 `GLOSSARY.md`，如果根目錄的 `GLOSSARY-MAP.md` 將儲存庫標記為多 context。決策會進入 `docs/adr/`。skill 僅在需要時建立這兩者。在第一個術語或決策被確定之前，什麼都不存在，因此你無需提前設定任何東西。

它也需要另外兩個 skill 存在，因為它自己的 `SKILL.md` 只有一行，用於委派給它們。[grilling](https://aihero.dev/skills-grilling) 提供訪談，而 [domain-modeling](https://aihero.dev/skills-domain-modeling) 提供撰寫。單獨安裝 `grill-with-docs` 只會得到一個無法運作的 skill。

<a id="the-paper-trail"></a>

## 檔案軌跡

一次 session 會產出三樣東西，它們並不等同。

| 已釐清內容 | 存放位置 |
| --- | --- |
| 一個術語：專案對某事物的專屬用詞 | `GLOSSARY.md`，內嵌，在釐清的瞬間 |
| 一個難以反轉、缺乏背景時令人驚訝、且涉及真實取捨的決策 | `docs/adr/` 下的 ADR |
| 你決定的其他所有事情 | 對話中，且不在其他地方 |

第三行是讓人措手不及的地方。`GLOSSARY.md` 只是一個詞彙表。它不包含實作細節、不包含 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)，也不包含草稿筆記。ADR 需要同時滿足三個條件，因此大多數決策不符合資格，大多數 session 不會產生任何 ADR。一個產出更精確詞彙表且零 ADR 的 session 是依設計運作的，但這意味著你同意的大部分內容只存在於你達成共識的 [context window](https://www.aihero.dev/ai-coding-dictionary/context-window) 中。將該對話交給 [to-spec](https://aihero.dev/skills-to-spec)，而不是 [clearing](https://www.aihero.dev/ai-coding-dictionary/clearing) 它。

詞彙表是主要產出。此 skill 建立領域語言：專案的專屬用詞，一次達成共識，這樣你、agent 和同事就不必再次推敲它們。並非所有人都認為這能提升 agent 效能。最強的異議是，術語及其英文解釋對 [model](https://www.aihero.dev/ai-coding-dictionary/model) 來說結果相同，而詞彙主要縮短了共享該詞彙的人類之間的溝通。從這個觀點來看，詞彙表仍有價值，但價值在於人類。

<a id="common-questions"></a>

## 常見問題

**我應該使用這個還是 `/wayfinder`？**
範圍決定一切。對於任何可以在單次 session 中解決的事情，使用這個；當工作大到無法在單次 session 中處理時，使用 [wayfinder](https://aihero.dev/skills-wayfinder)，它會先將工作繪製為決策 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 的地圖。Wayfinder 較慢且較密集，在範圍明確的功能上使用它是常見錯誤。它不取代此 skill，且可以為地圖中適合的部分啟動 grilling session。

**它執行了，但沒有出現 `GLOSSARY.md` 也沒有 ADR。**
有兩個已知原因。第一個是沒有內容符合資格。ADR 需要透過三道關卡，而一個關於沒有新詞彙的變更的 session 沒有東西可寫。第二個是真實的 bug。當 skill 在另一個編排層內執行時（spec-driven-development 包裝器、多 agent 框架、將其作為他人 pipeline 中步驟的規則），使用者回報檔案撰寫部分靜默地未發生，而訪談仍在執行。此 bug 已提交且未修復。如果你處於該設定中，在信任 session 輸出前，請檢查 working directory。

**它一次問了所有問題，沒有建議，且從未提及 `GLOSSARY.md`。**
這是 skill 未能載入其兩個依賴項。因為 `SKILL.md` 是一行委派，如果 agent 未能拾取 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling)，它會猜測 grilling 的含義，你會得到所有問題一次提出且沒有結構。部分載入更令人困惑。`grilling` 載入，`domain-modeling` 未載入，你會得到良好的訪談但沒有檔案軌跡。發生的頻率取決於 model 和 [effort](https://www.aihero.dev/ai-coding-dictionary/effort) 等級，這是此 skill 最常被回報的問題。如果你懷疑是這個問題，直接問 agent 它載入了哪些 skills。

**我所有的其他決策去哪了？**
只在對話中。這是關於此 skill 最嚴重的未解決抱怨。詞彙表不是 spec，大多數答案不值得 ADR，且沒有記錄將每個已解決的答案連結到 spec、ticket 和測試。後續步驟會將精確的答案（排序保證、負面需求、數值預設值）軟化為較弱的文字，結果可能看起來完整，但缺少你決定的內容。目前，請保留 session 並直接將其餵給 [to-spec](https://aihero.dev/skills-to-spec)。然後將 spec 與你自己的答案重新對照，而不是假設它已捕捉到它們。

**我可以將它指向一個完全沒有檔案的現有儲存庫嗎？**
是的。對於沒有 ADR、沒有領域語言且沒有設計原則的程式碼庫，這是正確的 skill：呼叫它並說「幫我記錄我的儲存庫」。使用者常將它與 [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 搭配使用，以建立或修復 `GLOSSARY.md`。預期需要引導它。它會閱讀程式碼並詢問您關於發現的內容，由您決定程式碼庫中已有的哪些詞彙是正確的。

**當 session 結束時我該做什麼？**
該 skill 的結尾訊息通常是開放式的，這是一個已知問題。在主流程中，答案是 [to-spec](https://aihero.dev/skills-to-spec)，在同一個對話中。如果變更小到可以立即建立，則直接改用 [implement](https://aihero.dev/skills-implement)。

**為什麼叫這個名字？**
沒有人對這個名字滿意。有一個公開的建議要將其重新命名為 `grill-domain-model`，這能更準確地描述其行為。目前尚無進展。如果重新命名最終落實，檔案頁面會隨之移動，URL 也會改變。

<a id="its-working-if"></a>

## 如果出現以下情況，則表示它運作正常

- `GLOSSARY.md` 在 session 期間逐詞變更，而不是在最後一次性出現。
- 詞彙表讀起來像是純詞彙（您的專案詞彙及其精確定義），且不包含任何實作細節或類似規格說明（spec）的文字。
- 程式碼庫能回答的問題會透過閱讀程式碼庫來回答，而不是向您詢問。
- 您獲得的 ADR 很少或沒有，而獲得的那些 ADR 是那些如果您必須再次爭論就會感到煩躁的決策。
- 它對您使用的詞彙提出質疑，因為您現有的詞彙表對其定義不同。

<a id="where-it-fits"></a>

## 適用範圍

`grill-with-docs` 是主要建立鏈的開頭：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

它在任何內容被記錄為規格說明（spec）之前執行。它產生共享的理解和確立的詞彙，供 [to-spec](https://aihero.dev/skills-to-spec) 隨後綜合整理，而無需再次採訪您。其緊鄰的 skill 是 [grill-me](https://aihero.dev/skills-grill-me)，即沒有儲存庫和檔案的相同採訪，以及 [domain-modeling](https://aihero.dev/skills-domain-modeling)，即它所驅動的詞彙表與 ADR 紀律；兩者都使用 [grilling](https://aihero.dev/skills-grilling) 原語進行採訪。在其上游，[wayfinder](https://aihero.dev/skills-wayfinder) 規劃過大以在單一 session 中完成的任務，並可將地圖的部分內容向下傳回給它。當您不確定哪個 skill 或流程適用時，[ask-matt](https://aihero.dev/skills-ask-matt) 會為您導向。
