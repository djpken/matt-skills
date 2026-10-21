<a id="what-it-does"></a>

## 功能說明

`research` 透過閱讀擁有答案的來源來回答問題，然後在儲存庫中留下一份附有引用的 Markdown 檔案。它僅從 **[主要來源](https://www.aihero.dev/ai-coding-dictionary/primary-source)** 運作：官方檔案、原始碼、規格說明、第一方 API。它會將每一項宣告追溯回擁有該宣告的來源，因此當 API 自身的檔案可存取時，它不會重複部落格文章對該 API 的描述。

它不會在對話中直接回答你。輸出是一個檔案，寫入儲存庫中已經存放此類筆記的位置，且每項宣告都附有連結。你得到一份可以回應、交給另一個 agent 或丟棄的檔案，而不是一個在 [session](https://www.aihero.dev/ai-coding-dictionary/session) 結束時就消失的答案。

<a id="when-to-reach-for-it"></a>

## 何時使用

輸入 `/research`，或者當任務轉為閱讀工作時，[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 會自動使用它。

當下一步是*從工作目錄外部找出資訊*（例如第三方 API 的行為、規格說明的內容、版本宣告是否成立），且你不想讓自己的執行緒因閱讀而停滯時，就使用它。你的需求決定使用哪個 skill：

| 你需要什麼 | 使用 |
| --- | --- |
| 決策所依賴的外部事實 | `research` |
| 透過訪談與你共同做出的決策 | [grilling](https://aihero.dev/skills-grilling) |
| 寫入 `GLOSSARY.md` 和 ADR 的永續性架構決策 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 找出某種方法在你的程式碼庫中是否可行 | [prototype](https://aihero.dev/skills-prototype) |
| 一個大到無法在單一 session 中處理的計畫 | [wayfinder](https://aihero.dev/skills-wayfinder) |

`research` 與 `grill-with-docs` 之間的界線在於**返回內容的儲存期限**。研究產生短生命週期的事實，例如本週該函式庫的驗證機制如何運作。ADR 記錄的是你保留的決策。如果你產出的是決策而非事實，那你是在 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling)，而不是在做研究。

<a id="delegated-legwork"></a>

## 委派的跑腿工作

閱讀過程作為**背景 agent** 執行。當它將每項宣告追溯至主要來源、撰寫一個 Markdown 檔案並回報時，你可以繼續工作。研究是你委派出去的跑腿工作，而不是外包的思考。你得到一份可用於審問、規劃或設計的檔案，而決策仍由你做出。

沒有任何機制阻止背景 agent 自行生成另一個背景 agent。這是該 skill 檔案記錄最完善的問題。

檔案存放位置由儲存庫決定，而非 skill。它遵循筆記已有的慣例。如果沒有慣例，它會選擇一個合理的位置並告知你。每次執行只寫入一個檔案。

<a id="common-questions"></a>

## 常見問題

**它生成了第二個研究 agent。這是預期行為嗎？**

不是。這是一個未修復的 bug，[issue #530](https://github.com/mattpocock/skills/issues/530)。該 skill 指示其呼叫者啟動一個背景 agent，但未限制 agent 型別。因此呼叫者生成一個 `general-purpose` agent，該 agent 擁有 `Agent` 工具和相同的指示，並再次遵循這些指示。一位回報者測量到單一研究任務在三次重疊執行中花費約 450k [tokens](https://www.aihero.dev/ai-coding-dictionary/token)，且重複的執行在使用者看不見的地方半小時後才完成。這也會發生在 Claude Code 之外。使用者確認在 Codex 搭配 GPT-5.6-sol 時也出現相同的巢狀問題。目前沒有已發布的修復方案。使用者已自行補丁安裝的副本，加入一行指示，告訴已經是 [subagent](https://www.aihero.dev/ai-coding-dictionary/subagent) 的 agent 自行完成工作。這有幫助，但這只是指示，而非結構性修復。在呼叫該 skill 後，請監控你的背景任務列表並停止重複的執行。

相反的失敗也會發生。如果你自己的全域指示禁止 agent 重新委派工作，背景 agent 會拒絕該任務，且該 skill 不會通知你就什麼都不做。

**檔案應該放在哪裡，我應該提交它嗎？**

該 skill 將檔案放在儲存庫已經存放筆記的位置，沒有其他意見。社群觀點大致已定：保留 ADR，而非研究檔案。一個關於此問題的 Discord 討論串說得最清楚：「保留 ADR。其他一切在完成後歸檔或刪除。否則它會變成工作的廢棄物，如果你已偏離規格/研究，可能會汙染未來的儲存庫閱讀。」研究檔案記錄的是撰寫當天為真的內容，因此過時的檔案比沒有檔案更糟。總體而言，這些檔案不屬於 git，且沒有標準的存放位置。人們改用 Obsidian、獨立的知識儲存庫或問題追蹤器。

**什麼算作「高信任度」的主要來源，由誰決定？**

由 [model](https://www.aihero.dev/ai-coding-dictionary/model) 決定。該 skill 指定了符合資格的來源*型別*（官方檔案、原始碼、規格說明、第一方 API），沒有允許清單、沒有網域閘門，也沒有驗證步驟。這是該 skill 最初提出時最激烈的反對意見，且沒有人公開回答：「五個指向垃圾的研究 subagent 只會讓你更快得到五個自信但錯誤的答案。你如何把什麼算作高信任度來源？」你唯一的保護是每項宣告上的引用。追蹤其中兩三個。如果它們落在該事物的摘要上，而非事物本身，則該執行失敗了。

**後續 session 會重用先前執行找到的內容嗎？**

不會。沒有任何機制自動載入過去的研究檔案。該檔案會留在儲存庫中，直到人類或 skill 指向它。這是早期對該設計最強的挑戰：「價值在於 Markdown 成為 agent 稍後重讀的上下文，而非抓取本身。一個只寫一次的死檔案只是一種花哨的搜尋。」已發布的 skill 並未解決此問題。實際上，該檔案只有在你自己將其輸入下一步時才有用：將其附加到規格說明、在 grilling session 中引用它，或讓 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket) 指向它。

**為什麼不直接要求 agent 去閱讀檔案？**

你可以，而一個明確說明該做法的兩行提示詞正是此 skill 取代的慣例。該 skill 提供了提示詞沒有的兩樣東西。它在背景執行，因此你 session 的 [context](https://www.aihero.dev/ai-coding-dictionary/context) 保持乾淨。而且主要來源限制和附引用檔案的輸出每次都是一樣的，而不是取決於你如何措辭提示詞。與 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 自身的深度研究模式相比，差異在於檔案和主要來源規則，而非搜尋。如果一個兩行提示詞能在小問題上滿足你的需求，就使用兩行提示詞。

**它何時停止閱讀？**

此 skill 沒有停止條件。這會表現為兩種看似相反但根源相同的抱怨：agent 鑽研得過深，以及 agent 廣泛涵蓋主題卻漏掉了關鍵細節。一位實務者如此形容：「deep-research skills 有時過於深入。而指示 agent 進行研究通常會導致遺漏重要細節。」你必須設定範圍。一個狹窄且可回答的問題（一個 API、一個行為、一個版本宣告）的回傳結果，遠比「研究 X」要好得多。

**`/wayfinder` 建立了研究工單。我需要自己解決它們嗎？**

不需要，現在它會為你觸發。在 v1.1 之後的未發布變更中，charting session 會為每個研究工單生成一個 `/research` subagent，並並行執行它們。每個 subagent 會在一個一次性的 `research/<name>` 分支上記錄其發現，並附帶來自工單的 [context pointer](https://www.aihero.dev/ai-coding-dictionary/context-pointer)。研究工單是 wayfinder「每 session 一個工單」規則的唯一例外，因為它們是 [AFK](https://www.aihero.dev/ai-coding-dictionary/afk)：沒有任何事情在等待你。一個已知問題：稍後刪除該分支會導致工單中的 context pointers 失效。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示運作正常

- 你自己的 session 持續進行。如果你坐在那裡看著它閱讀，代表委派沒有發生。
- 恰好出現一個新的背景任務。出現第二個名稱幾乎相同的任務，就是巢狀 bug。
- 出現一個新的 Markdown 檔案，位於該 repo 已用於筆記的資料夾中，且 agent 會告知你路徑。
- 其中的每個宣告都附有連結，隨機追蹤兩個連結會讓你到達官方檔案、規格或原始檔案本身，而不是某人的摘要。
- 你可以僅憑該檔案做出你之前卡住的那個決定，而無需自己回到原始資料。

<a id="where-it-fits"></a>

## 適用情境

`research` 是一個隨時可用的獨立工具。它為思考類 skills 提供輸入，並非建置鏈中的一個步驟。你將它的檔案*帶入*流程中。[grilling](https://aihero.dev/skills-grilling) 和 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 在已擁有事實時會提出更尖銳的問題，而 [to-spec](https://aihero.dev/skills-to-spec) 可以針對它進行綜合。[wayfinder](https://aihero.dev/skills-wayfinder) 是唯一直接呼叫它的 skill。它使用 `/research` subagent 來解決地圖上的每個研究工單。關於完整地圖，請參閱 [ask-matt](https://aihero.dev/skills-ask-matt)。
