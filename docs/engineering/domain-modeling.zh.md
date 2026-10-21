<a id="what-it-does"></a>

## 功能說明

`domain-modeling` 會在您設計期間建立並精煉專案的**通用語言**。當術語與詞彙表衝突時，它會提出質疑；當您使用模糊詞彙時，它會要求精確的措辭；它會針對具體情境測試關係，直到邊界變得精確為止。

這是一種**主動**的紀律，而非被動的。任何 skill 都可以讀取 `GLOSSARY.md` 來借用其詞彙。這個 skill 適用於您正在*變更*模型的情況，這就是它會中斷您的原因。當您在對話中途確定某個術語時，它會立即將該術語寫入 `GLOSSARY.md`，而不是在最後產生一份整齊的詞彙表。在最後撰寫的詞彙表只是 [session](https://www.aihero.dev/ai-coding-dictionary/session) 的摘要；內嵌版本才是該 session 的輸出。

<a id="when-to-reach-for-it"></a>

## 何時使用

輸入 `/domain-modeling`，或者當任務符合條件時，agent 會自動呼叫它。實際上，自動呼叫是這個 skill 最薄弱的環節。當 `grill-with-docs` 或 `wayfinder` 指示載入它時，[models](https://www.aihero.dev/ai-coding-dictionary/model) 通常會載入 `grilling` 而跳過這個。如果 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) session 執行完畢，而 `GLOSSARY.md` 在最後沒有改變，那就是發生了這種情況。請與其他 skill 並列，透過名稱明確呼叫它。

當*詞彙*本身是問題時，請使用它：

| 情境 | 動作 |
| --- | --- |
| 兩個人對「取消」有不同的理解 | `domain-modeling`：選擇標準術語，將其他術語列在 `_Avoid_` 之下 |
| 「Account」在三個檔案中承擔了三種角色 | `domain-modeling`：將其拆分為 Customer 和 User |
| 您剛做了一個難以逆轉的架構決策 | `domain-modeling`：如果該決策達標，它會提供一份 ADR |
| 模組的*形狀*是問題所在：縫合點在哪裡，介面有多深 | [codebase-design](https://aihero.dev/skills-codebase-design) |
| 您希望在建立之前對整個計畫進行審問 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，它在底層驅動這個 skill |
| 您只想查詢術語，而非變更它 | 什麼都不用做。讀取 `GLOSSARY.md`。它是一個檔案。 |

<a id="prerequisites"></a>

## 先決條件

事先沒有要求。這個 skill 會寫入兩個位置，並按需建立它們：

- **`GLOSSARY.md`** 位於儲存庫根目錄。當您確定第一個術語時，skill 會建立它。在根目錄有 `GLOSSARY-MAP.md` 的儲存庫中，術語會寫入該地圖指向的每個 context 的 `GLOSSARY.md`。
- **`docs/adr/`**。當第一份達標的 ADR 出現時，skill 會建立它。

開始之前不需要存在任何東西，skill 也不會預先建立任何內容。

<a id="two-artifacts-two-bars"></a>

## 兩種產物，兩種標準

詞彙表和 ADR 有不同的標準。這個 skill 的大多數問題都來自於混淆這兩者。

| | `GLOSSARY.md` | `docs/adr/NNNN-slug.md` |
| --- | --- | --- |
| 包含內容 | 術語。某事物**是**什麼，用一兩句話說明，被拒絕的同義詞列在 `_Avoid_` 之下 | 一個決策，用一到三句話說明：背景、選擇、原因 |
| 撰寫標準 | 模糊術語變成了標準術語 | **三者皆需滿足**：難以逆轉、缺乏背景時令人驚訝、是真實權衡的結果 |
| 撰寫時機 | 內嵌，在術語確定的那一刻 | 提供建議，而非假設 |
| 絕不包含 | 實作細節、[spec](https://www.aihero.dev/ai-coding-dictionary/spec)、草稿、一般程式設計概念 | 記錄本次 session 所做的每個決策的日記 |

如果 ADR 的三項測試中有任何一項未透過，就不會有 ADR。容易逆轉的決策最終會被逆轉；不令人驚訝的決策沒有人會問；沒有真實替代方案的決策只是記錄了您做了顯而易見的事。

`GLOSSARY.md` 規則最為重要，因為它在實務中最容易出錯。**它是一個詞彙表，且僅此而已。** 如果不加控制，models 會將「寫入 `GLOSSARY.md`」視為許可權，將您給出的每個答案都持久化，導致檔案變成一份持續更新的 spec。這是跨多個 models 報告最多的問題。

<a id="cross-referencing-and-where-it-stops"></a>

## 交叉引用，及其界限

當您說明某事物如何運作時，skill 會檢查程式碼並指出任何矛盾。*「您的程式碼取消整個 Orders，但您剛說部分取消是可能的，哪一個是對的？」* 在變更任何一方之前，skill 會讓語言和程式碼大聲地保持一致。

它交叉引用**程式碼**以及已提交的 `GLOSSARY.md` 和 ADRs，不包含其他內容。它不會搜尋您的 issue tracker。如果您的團隊幾個月前在一個已關閉的 issue 中討論並解決了命名衝突，skill 會再次提出它，彷彿它是新的。有一個 [an open request](https://github.com/mattpocock/skills/issues/717) 旨在修復此問題。在那之前，變通方法是將指示放入您自己的 `docs/agents/domain.md`，skills 已經會讀取它。

<a id="common-questions"></a>

## 常見問題

**我的 `GLOSSARY.md` 有 500 行。1,000 行。3,000 行。我該怎麼辦？**
大小隻是症狀。原因是檔案吸收了實作細節和從未屬於詞彙表的決策。解決方案是一條直接指示：`/grill-with-docs make my GLOSSARY.md more concise and remove any implementation details from it`。對膨脹的檔案執行它，大部分內容會消失。只有當檔案精簡且仍涵蓋兩個讀者不希望同時掌握的領域時，才使用 `GLOSSARY-MAP.md` 進行拆分。拆分膨脹的檔案只會得到幾個膨脹的檔案。skill 在此處的指引還不夠強，無法在最初防止這種增長，相關 issue 追蹤仍處於開放狀態。

**為什麼是 `GLOSSARY.md` 而不是 `GLOSSARY.md`？**
這是整個 skill 集合中爭議最大的命名問題，且沒有定論。反對目前名稱的理由很充分：如果它是「一個詞彙表，且僅此而已」，`GLOSSARY.md` 就說明瞭這一點，正如一位讀者所言，「有了 ai agents，一切都是 [context](https://www.aihero.dev/ai-coding-dictionary/context)」。支援目前名稱的理由是地圖：`GLOSSARY-MAP.md` 指向多個 `GLOSSARY.md` 檔案的讀法很自然，而 `GLOSSARY-MAP.md` 則不然，且 `context` 是 DDD 中用於模型有界區域的標準詞彙。至少有一個人維護一個本地 fork，純粹為了重新命名該檔案。您也可以這樣做，但集合中的每個其他 skill 都在尋找 `GLOSSARY.md`，因此重新命名意味著需要修補所有這些 skill。

**`/ubiquitous-language` 去哪了？**
它被移除了，且未被棄用。其職責移入了 `domain-modeling`，該 skill 持續維護整個模型，而不是從一次對話中傾倒出一份詞彙表。詞彙執行現在變得更加重要，而非較少。它在 grilling、triage 和 mapping 底層執行，而不是作為一個您必須記得的獨立步驟。

**如何為沒有詞彙表的程式碼庫建立詞彙表？**
明確要求它，而不是等待它累積。`/grill-with-docs help me scaffold my existing repo with a GLOSSARY.md` 是檔案記載的路徑。預期會有一長串審問。一位使用者報告說，在檔案成形之前，提出了超過 50 個問題。在 brownfield 儲存庫上，順帶使用建立詞彙表的速度太慢。

**我可以保留領域模型並使用我自己的 ADR 格式嗎？**
今天沒有順利完成。詞彙表部分與 ADR 部分包含在同一個 skill 中，因此如果團隊已有既定的 ADR 慣例（不同的範本、不同的位置、不同的命名方式），就會收到與內部風格相衝突的指示。目前的選項是將該 skill 複製到本地並進行編輯，或在儲存庫自己的 agent 檔案中覆寫 ADR 慣例。將這兩者分開是 [an open request](https://github.com/mattpocock/skills/issues/557)。

**詞彙表真的值得保留嗎？它多了一個需要審查的產出物，而且可能會過時。**
有時並非如此。DDD 越接近實作，其效用就越低。其價值在於上游的命名與概念對齊，而非聚合與分層儀式。同義詞控制對命名邊界很重要：模組名稱、資料表名稱、狀態列舉、問題標題、CLI 命令。在一般文字中，其重要性則低得多。還有一種反對意見認為，領域術語壓縮了已經共享這些術語的*人類之間*的溝通，而 agent 對純英文描述的反應也是一樣的。從這個角度看，詞彙表的價值在於讓你和審查者與 agent 的行為保持一致，而不是讓 agent 變得更好。在一天內完成的建置中，可以跳過它。而且，未經審查、由 agent 撰寫的詞彙表比沒有詞彙表更糟：它會充滿自信的陳述，後續的 session 會將其視為真實。

**它能把我的模糊提示轉化為領域語言嗎？**
不能，也沒有計畫開發這樣做的 skill。你自己都不理解的領域語言，寫下來後也毫無意義。這個 skill 在你已經具備理解後，強制執行精確性；它不會發明你不具備的詞彙。相關的陷阱是，在沒有進行建模的情況下使用領域詞彙。在錯誤的概念之上使用正確的名詞，會產生讀起來正確但實際上不正確的輸出。

<a id="its-working-if"></a>

## 如果出現以下情況，表示它運作正常

- 它會在句子中途停下，詢問你指的是兩件事中的哪一件，而不是隨意選一個繼續進行。
- `GLOSSARY.md` 在對話**期間**發生變化，而不是在最後一次性完成。
- 它拒絕為明天就能撤銷的事情撰寫 ADR，並指出三項測試中哪一項未透過。
- 新條目用一到兩句話定義某事物*是什麼*，並列出你在 `_Avoid_` 下放棄使用的詞彙。
- 當你的程式碼與你的句子不一致時，它會引用你的程式碼反駁你。
- `GLOSSARY.md` 變短的情況與變長的情況一樣常見。

<a id="where-it-fits"></a>

## 適用情境

`domain-modeling` 是一個**由模型呼叫的參考**，它比其他 skill 更常在*底層*執行，而非獨立執行。[grill-with-docs](https://aihero.dev/skills-grill-with-docs) 透過審問 session 驅動它，[wayfinder](https://aihero.dev/skills-wayfinder) 在繪製地圖時載入它，[triage](https://aihero.dev/skills-triage) 使用它來讓 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 使用專案自己的詞彙，而 [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 在決策確定時呼叫它。它最接近的兄弟是 [codebase-design](https://aihero.dev/skills-codebase-design)。它們共同構成了其他一切之下的詞彙層，這個負責*領域*，那個負責模組的*形狀*。你也可以直接存取它，當你希望獲得紀律，但不想承諾通常會載入它的 skill 的步驟時。當你不確定哪個 skill 適合時，[ask-matt](https://aihero.dev/skills-ask-matt) 會為你導航。
