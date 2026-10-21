<a id="what-it-does"></a>

## 功能說明

`teach` 會將您執行它的目錄轉換為一個常駐的教學工作區，並透過多個 [sessions](https://www.aihero.dev/ai-coding-dictionary/session)，以簡短且自包含的 HTML 課程來教導您一個主題。

它不會根據 [model](https://www.aihero.dev/ai-coding-dictionary/model) 已知的內容進行教學。它將 [parametric knowledge](https://www.aihero.dev/ai-coding-dictionary/parametric-knowledge) 視為不可信賴的來源。在開始教學之前，它會尋找高可信度的資源，將其記錄在 `RESOURCES.md` 中，並在每個課程中引用這些資源。它也是 [stateful](https://www.aihero.dev/ai-coding-dictionary/stateful) 的。任務、資源、課程以及您已學習內容的記錄，全都作為檔案存在於該目錄中，因此下一個 session 會從這些檔案繼續，而不是從上次對話的殘留內容繼續。

<a id="when-to-reach-for-it"></a>

## 何時使用

您透過輸入 `/teach` 來呼叫此功能；[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不會自行主動使用它。

當學習本身就是專案時，請使用它：例如一種語言、一個框架、您剛加入的程式碼庫、瑜伽、著色器（shaders）或認證考試。它不適用於順帶解釋一個概念的情況。

| 您的需求 | 建議使用的工具 |
| --- | --- |
| 花數週時間學習一個主題，並累積多個 session | `teach` |
| 在目前的 session 中解釋一個概念 | 直接在該 session 中詢問即可 |
| Agent 重新解釋上一則訊息，因為之前沒有講清楚 | [wait-what](https://aihero.dev/skills-wait-what) |
| 為了磨練你已有的思考，而非獲取新素材 | [grill-me](https://aihero.dev/skills-grill-me) |
| 一個背景 agent，用來閱讀 [primary sources](https://www.aihero.dev/ai-coding-dictionary/primary-source) 並為您留下一份附有引用的檔案 | [research](https://aihero.dev/skills-research) |
| 為了學習在審問過程中出現的某件事，而不偏離 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) | 將 [handoff](https://aihero.dev/skills-handoff) 移交至教學工作區，然後 `teach` 在那裡 |

<a id="prerequisites"></a>

## 先決條件

`teach` 會建立一個目錄，而不是產生單一檔案，且該 skill 假設每個工作區只對應一個任務，因此請在一個您願意完全交給單一主題的地方執行它。請不要將它放在您正在開發的專案中。獨立的儲存庫是最佳選擇，優於全域 `~/.learnings/` 資料夾或目前的工作專案。獨立的儲存庫也讓您能夠 commit 這些課程，這正是團隊分享課程的方式。

該目錄中會累積以下內容：

| 路徑 | 內容說明 |
| --- | --- |
| `MISSION.md` | 您學習此主題的原因。其他所有檔案都依賴它。如果此檔案缺失，`teach` 做的第一件事就是向您提問，直到補全為止 |
| `RESOURCES.md` | 經過審核的教學來源，分為知識（Knowledge）與智慧（Wisdom，社群）兩類 |
| `lessons/*.html` | 編號課程：教學的基本單位 |
| `reference/*.html` | 精簡的速查表、演演算法、詞彙表：供您隨時回查的檔案 |
| `learning-records/*.md` | 採用 ADR 風格的筆記，記錄您已明確學會的內容，用於決定接下來要教什麼 |
| `assets/*` | 可重用的元件，從共用的樣式表開始，讓課程看起來像同一門課程 |
| `NOTES.md` | 您指定的教學偏好 |

關於上述清單有兩點說明。詞彙表適合大多數主題，但該 skill 附帶的 `GLOSSARY-FORMAT.md` 已不再被 `SKILL.md` 連結，因此除非您主動要求，否則不會生成（[issue #559](https://github.com/mattpocock/skills/issues/559)）。所有內容都會存放在您執行 `/teach` 的目錄中。

<a id="storage-strength-not-fluency"></a>

## 重視儲存強度，而非流暢度

應該思考的關鍵詞是 **storage strength**（儲存強度）：即長期記憶保留能力，與 **fluency**（流暢度）相對。流暢度是指閱讀當下那種感覺像精通、但一週後就遺忘的即時回憶能力。`teach` 透過「可取的困難」（desirable difficulty）來建立前者：包括提取練習、間隔重複與交錯學習。知識必須先行。在該階段，困難會對您不利，因為它會消耗您理解內容所需的短期記憶。接著，`teach` 透過緊密的回饋迴圈來訓練該技能，在該階段，困難就是工具。

有兩件事決定您會學到什麼。**任務**（您想要學習此主題的具體現實原因）是每個課程的基礎。沒有它，課程就會變得抽象，且無法決定接下來該教什麼。根據任務與學習記錄，`teach` 會在您的 **最近發展區**（zone of proximal development）內選擇下一個課程：難度足以需要付出努力，但不至於超前太多而無法學習。

儲存強度也是該 skill 選擇反駁而非附和的原因。需要 **智慧**（現實世界判斷力）的問題，會先給出一個嘗試性的答案，然後指向一個社群，讓您在其中驗證該答案。該 skill 不允許您跳過測驗。有一位使用者回報說，他回答「多謝」後，被告知測驗尚未完成。

<a id="lessons-references-and-components"></a>

## 課程、參考檔案與元件

一個 **課程** 是一個自包含的 HTML 檔案，短到可以一次完成，與任務相關，並提供一個具體的勝利成果。它會引用來源，推薦一個主要來源供您自行閱讀，並連結到同級課程與參考檔案。

您很少會回頭看課程，但您會回頭看參考檔案。因此，課程的精簡形式（語法表、演演算法、姿勢序列、詞彙表）應放在 `reference/` 中，而不是埋在介紹它的課程裡。

課程是由 `assets/` 中的 **元件** 構成的：樣式表、測驗小工具、模擬器、圖表輔助工具。重用是預設行為。Agent 在撰寫課程前會先閱讀 `assets/`，並基於現有內容進行建構。如果第二個課程可能用到新的內容，它會將其寫成元件，而不是內嵌在課程中。共用樣式表是每個工作區的第一個元件，它讓課程看起來像同一門課程，而非許多不相關的頁面。

<a id="common-questions"></a>

## 常見問題

**檔案放在哪裡？我的檔案出現在 `~/.claude/skills` 中。**
在您執行 `/teach` 的目錄中（[#377](https://github.com/mattpocock/skills/issues/377)）。

**我應該留在同一個 session，還是每個課程都開新的 session？**
三種方式都可行：留在同一個 session、在新的 session 中重新呼叫 `/teach`，或在同一個資料夾中開啟新的 session。每個課程都是一次獨立的呼叫。課程狀態存在於資料夾中，而非對話中。常見做法是在工作區中開啟一個新的 session，並輸入 `/teach next lesson for <topic>`。

**我怎麼知道它不是在教我它編造的東西？**
你不能僅憑 skill 的說法就相信它。你必須閱讀原始資料。`teach` 不夠可靠，無法在未經核實的情況下信任，任何建立在 LLM 之上的 skill 也是如此。基礎設定（`RESOURCES.md`、每課都有引用、每課推薦一個原始資料來源）讓核實課程變得容易。但這並不代表不需要核實。這種失敗確實發生過：一位學習 2x2 魔方的使用者收到了無法解開魔方的虛構步驟序列。在這種情況下，請檢查模型、harness、effort 設定以及資料來源。在具有精確記號的程式性領域中，風險最高；而在輸出可立即驗證的領域（例如可以執行的程式碼）中，風險最低。

**正確的測驗答案永遠是第一個選項。**
多人在 Sonnet、Opus 和 GLM 上確認了這一點，且目前仍未修復。`SKILL.md` 現在要求每個答案的字數相同。這消除了另一種線索（正確答案過去是唯一完全推理過的選項），但對位置沒有說明。一位貢獻者測試了針對位置的指令層級修復，但在九個課程中，33 次測試裡正確答案仍有 33 次落在 A 槽（[#335](https://github.com/mattpocock/skills/issues/335)）。因此，真正的修復是在 `assets/` 中建立一個會洗牌答案的測驗元件，而不是改善措辭。在該功能發布之前，請忽略答案位置。你的 `assets/` 資料夾由你自行修改，因此你可以要求一個在渲染時洗牌的元件作為本地修復。

**它假設我已經知道某些事情，並使用了從未定義的術語。**
這是最常見的實際抱怨。沒有評估步驟。`teach` 會根據任務和學習記錄推斷你的程度，而在第一次會話中沒有學習記錄。一位在 wayfinder 管線中執行它的使用者直言不諱地說：「它從未進行嚴格的盤問來確定我的起點，因此對我已知的內容做了許多假設。」另一位報告說，課程使用了未定義的術語，而關於其硬體的一課涵蓋了硬體能做的事，卻從未說明它不能做的事。兩件事有助於改善：在第一則訊息中說明你的既有知識和缺口，並在課程未達標時大聲糾正程度，因為糾正會成為學習記錄並引導下一課。明確的知識評估步驟是一個持續的功能請求（[#725](https://github.com/mattpocock/skills/issues/725)），而非已發布的行為。

**它是否執行間隔重複，以及它是否知道何時停止教學？**
第一個問題的答案是否，第二個問題的答案也不是可靠的。該 skill 圍繞間隔和交替設計課程，但沒有任何東西安排複習，也沒有 Anki 或日曆整合。使用者經常要求這兩者。一個相關的缺口是退出標準。正如一位使用者所說，`teach`「很擅長製作下一課，但不太擅長知道何時停止並切換到複習或實際練習」。如果你想要複習或演練而不是新內容，請提出要求；該 skill 不會自行建議切換。

**它只對程式碼有用嗎？**
不。大多數報告的用途都在編碼之外：韓語、日語正式語域、鋼琴、吉他、桌遊設計、OpenSCAD、電影情節、Azure 和 CCNA 認證、大學考試，以及八歲和十歲的孩子獲得關於密室逃脫和火蠑螈的可列印書籍。該 skill 中沒有任何特定於程式設計的部分。任務、資源、最近發展區和演練在任何領域都以相同方式運作。在程式碼領域內，使用者報告說，在陌生的程式碼庫或新團隊的技術棧中獲得方向感，比從零開始學習語言更有價值。

**我應該用哪個模型來執行它？**
沒有標準答案，且報告的差異很大。使用者報告說，較高的 [reasoning effort](https://www.aihero.dev/ai-coding-dictionary/effort) 產生的課程比中等設定更好。一位使用者透過 Copilot CLI 使用 Codex 執行相同的 skill，得到了一張 30 行的 HTML 卡片，而 Claude Code 產出了一整課。如果你的組織允許你在該處新增 skill，它可以在 Claude Cowork 中未經修改地執行。如果課程內容稀薄，請在重寫你的提示之前，先更改模型、[harness](https://www.aihero.dev/ai-coding-dictionary/harness) 或 effort。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示它運作正常

- 它在空資料夾中做的第一件事是詢問你為什麼需要這個，而不是直接產出課程。
- `RESOURCES.md` 會在課程完成前填滿，且每課都指定了一個值得你自己閱讀的原始資料來源。
- 課程中的宣告帶有外部連結。沒有引用的課程代表該 skill 是憑記憶教學。
- 一課只需一次就座，並讓你能夠做一件之前不會做的事。
- 在該資料夾中開啟新的會話並說「下一課」，會繼續課程而不是重新開始。
- `learning-records/` 會增長，且課程會停止重新教授你已經展示過的內容。
- 這些課程看起來像一個課程：它們連結 `assets/` 中的樣式表，而不是各自攜帶自己的樣式表。
- 需要判斷的問題會引導你前往論壇、subreddit 或課堂，而不僅僅是提供答案。

<a id="where-it-fits"></a>

## 適用位置

`teach` 是一個**隨時可用的獨立工具**。它不是建置鏈中的一個步驟，也不與工程流程共享任何產物；它在你學習該主題的期間，在自己的資料夾中運作。

它唯一的真正鄰居是 [handoff](https://aihero.dev/skills-handoff)。它們一起回答「如果我正在被盤問我不理解的事情，我該怎麼辦？」不要停止盤問去學習。`/handoff` 到教學工作區，在那裡使用 `/teach` 學習該主題，然後回去繼續你中斷的地方。附近的替代方案是 [research](https://aihero.dev/skills-research)，適用於你想要的是有引用的檔案而不是課程和保留的情況。當你不確定哪個 skill 或流程適合時，[ask-matt](https://aihero.dev/skills-ask-matt) 會引導你遍歷整個集合。
