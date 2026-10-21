<a id="what-it-does"></a>

## 功能說明

`improve-codebase-architecture` 會掃描程式碼庫，尋找**深化機會**。這些是淺層模組（介面複雜度幾乎與其所隱藏的實作相當）可以轉為深層模組的地方。它會將這些機會整理成一份自包含的 HTML 報告，然後針對你選定的專案 [grills](https://www.aihero.dev/ai-coding-dictionary/grilling) 你。

它絕不修改程式碼。整個執行過程只會產生一段對話，以及一個位於你的作業系統暫存目錄中的 HTML 檔案。你稍後會在獨立的 [session](https://www.aihero.dev/ai-coding-dictionary/session) 中，透過正常的建置流程進行重構。這使它成為一種調查工具，而非重構工具，因此你可以對尚未準備好進行修改的程式碼庫執行它。

兩項過濾機制可防止報告變成通用的清理建議。首先，每個候選專案必須透過**刪除測試**：如果你移除此模組，其複雜度是會移入更小的介面背後，還是會分散到呼叫端？只有前一種情況才會獲得卡片。其次，除非你指定特定區域，否則它會先讀取近期的 commit 歷史記錄，並將掃描重點放在經常變動的檔案路徑上。在無人觸碰的程式碼中進行深化，是一項永遠無法收回成本的重構。

<a id="when-to-reach-for-it"></a>

## 何時使用

你透過輸入 `/improve-codebase-architecture` 來啟動此功能；[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不會自行使用它。

它不是主要建置迴圈中的一個步驟。你定期執行它，以排入更多能改善程式碼庫的工作。人們在四種情境下使用它：

| 情境 | 使用方式 |
| --- | --- |
| 例行維護 | 每隔幾天執行一次，或在有空閒時間時執行，以確保結構不會在功能開發之間退化。 |
| 大型建置前 | 指向 [spec](https://www.aihero.dev/ai-coding-dictionary/spec) 並詢問「我們如何能讓這個變更變得容易？」這是對它最有效的提示。 |
| 既有專案稽核 | 對大型、結構鬆散或 [vibe-coded](https://www.aihero.dev/ai-coding-dictionary/vibe-coding) 的儲存庫執行，以瞭解其目前的狀態。 |
| 舊有測試工作 | 在針對無法測試的程式碼撰寫測試之前，使用它來尋找缺失的接縫。 |

與同類工具容易混淆之處：

- 若要設計你已選定的單一模組，請使用 [codebase-design](https://aihero.dev/skills-codebase-design)。此 skill 負責找到要處理的模組，而 `codebase-design` 是你進行設計的地方。
- 對於規模過大、無法在單一 session 中處理的整體工作，請使用 [wayfinder](https://aihero.dev/skills-wayfinder)。
- 對於「這個特定專案壞掉了」的情況，請使用 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs)。當真正的發現是沒有良好的接縫來鎖定該 bug 時，它會引導你回到這裡。

<a id="prerequisites"></a>

## 先決條件

執行時無任何先決條件。如果 `GLOSSARY.md` 或 `docs/adr/` 中的 ADR 存在，它會讀取這些內容並使用你領域中的專有名詞。此時候選專案會顯示為「深化 Order intake 模組」，而非「重構 FooBarHandler」。

它在兩個位置進行寫入。報告會寫入 `<tmpdir>/architecture-review-<timestamp>.html`，位於儲存庫之外。在 grilling 迴圈期間，它會在 `GLOSSARY.md` 中新增或精煉術語，若該檔案不存在則會建立它。它也會建議將被拒絕的候選專案記錄為 ADR，以便未來的執行不會再次建議它。

<a id="depth-and-the-report-that-hunts-for-it"></a>

## 深度，以及尋找深度的報告

此 skill 基於一個概念：**深度**。深層模組將大量行為隱藏在小型且穩定的介面背後。淺層模組則透過幾乎與下方程式碼一樣寬的介面，暴露其實作。報告會尋找三種淺層形式：

- 僅為可測試性而提取的純函式，但真正的 bug 在於呼叫端如何使用它們（缺乏**區域性性**）。
- 跨越其**接縫**洩漏的模組。
- 一個必須開啟五個檔案才能理解的 концепт。

針對每一種情況，它會提出能解決問題的深化方案。

每個候選專案是一張卡片，包含涉及的檔案、摩擦點、以白話文描述的解決方案、以**區域性性**和**槓桿**為單位的效益、前後對比圖，以及強度徽章。

| 徽章 | 對你的意義 |
| --- | --- |
| `Strong` | 明確透過刪除測試，且摩擦點真實存在。請認真對待這些專案。 |
| `Worth exploring` | 合理的深化方案，但效益取決於程式碼接下來的發展方向。 |
| `Speculative` | 為求完整而納入。你可以忽略其中大多數專案。 |

報告以**首要建議**結尾，即它會優先執行的候選專案。接著 skill 停止並詢問你想探索哪個候選專案。此時你尚未做出任何決定，也沒有任何程式碼被修改。

<a id="what-happens-after-you-pick-one"></a>

## 選定一項後會發生什麼

當你選定一個候選專案時，會針對它啟動 [grilling](https://aihero.dev/skills-grilling) session。它涵蓋約束條件、接縫背後應放置的內容、哪些測試能保留，以及深化後的介面應為何種樣貌。該 session 的輸出是決策，而非 diff。從那裡開始，適用正常流程：將決策帶入 [to-spec](https://aihero.dev/skills-to-spec)，然後 [to-tickets](https://aihero.dev/skills-to-tickets)，最後 [implement](https://aihero.dev/skills-implement)。

<a id="common-questions"></a>

## 常見問題

**它花了一小時就一個想法對我進行 grilling，而不是展示選項。我可以關閉這個功能嗎？**

可以。在啟動時說明（「不要 grilling 我，只要展示報告」）。這是關於此 skill 最常見的抱怨。一位使用者喜歡它，認為它是「獲得徹底改進分析的一種便捷方式」，但在加入 grilling 迴圈後，發現它「幾乎無法使用」。在他們的 session 中，它提出單一解決方案，然後詢問「十個或一百個問題」。設計意圖是報告優先，grilling 僅在你選擇的候選專案上開始。但較弱的 [models](https://www.aihero.dev/ai-coding-dictionary/model) 會直接跳過，針對它們的第一個想法進行訪談。該討論串中的結果因模型而異很大。這是一個未解決的問題，且此 skill 目前尚無檔案記錄的 no-grill 模式。

**報告開啟時是無樣式的原始 HTML，沒有圖表。發生了什麼事？**

報告從 CDN 載入 Tailwind 和 Mermaid，因此開啟時需要網路存取。如果某些東西阻擋了這些指令碼，頁面會中斷且沒有錯誤訊息。在報告的案例中，一個安全 hook 要求 SRI 雜湊值。Agent 新增了它們，但 CDN 向瀏覽器提供的位元組與向計算雜湊值的 `curl` 提供的不同，因此瀏覽器阻擋了該指令碼。離線和鎖定環境有相同的問題。Agent 無法看到這個問題，因為它從未渲染該頁面。作為變通方法，請要求使用內嵌 CSS 和手動建立的 SVG 圖表，而非 CDN 設定。這是一個未解決的問題。

**它給了我十二個候選專案。我應該在同一個 session 中處理它們，還是開始新的 session？**

每個 session 只使用一個 candidate。如果你在一個 conversation 中處理了多個，[context window](https://www.aihero.dev/ai-coding-dictionary/context-window) 會同時被報告、grilling、domain-model 編輯和程式碼變更填滿。報告只是一個 temp file，所以請將 candidate 帶入下一步，而不是檔案。選定一個，進行 grilling，並將決策帶入 `/to-spec`。將其餘的轉為 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)，以便稍後單獨處理。將選定的改進放入 spec，而不是直接進入 implementation。人們經常問這個問題，而該 skill 本身並未記錄相關 workflow。

**我應該如何 prompt 它？**

用你接下來要建構的內容來 prompt 它。如果即將進行大型建構，請指向 spec 並詢問「我們如何讓這個變更變得容易？」沒有 prompt 的執行會自行尋找 hot spots。這對於例行維護沒問題，但提供方向會讓報告更具可執行性。

**它在大型 legacy codebase 上有效嗎？**

部分有效。它在缺乏一致結構的大型現有 codebase 上運作良好，並且是任何一次性結構設定後推薦的維護工具。但擁有失控專案的使用者回報說它「有點幫助，但似乎還不夠」。一位擁有八年歷史 legacy codebase 的開發者回報模型在原地打轉，但同一個 skill 在整潔的 repo 上會產生乾淨的圖。目前還沒有專門針對該情況的 `/refactor` skill。如果 codebase 沒有共享詞彙，請先執行 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 來建立一個。這通常會讓此 skill 的輸出好很多。

**這與 `/codebase-design` 有何不同？**

`/codebase-design` 是一個 reference，而不是 session driver。它提供詞彙（module、interface、depth、seam、adapter、leverage、locality），而此 skill 使用它。如果你給一個新的 agent `/codebase-design` 作為任務，它會以已知的方式失敗。它沒有自己的流程可遵循，所以 agent 會發明一個，再次探索程式碼，並在詢問你任何問題之前執行非常長的時間。執行此 skill，並讓它使用那一個。

**它會告訴我 codebase 沒問題嗎？**

很少，所以在開始前請知道這一點。該 skill 存在於輸出 findings，因此它傾向於產生 candidates，而不是得出沒有問題的結論。使用 strength badges 來修正這一點。如果報告中的每個 candidate 都是 `Speculative`，該 skill 就沒找到任何東西。

**它在 Codex 或其他 harness 中有效嗎？**

部分有效。探索步驟直接以 `subagent_type=Explore` 命名 Claude Code 的 `Agent` tool。沒有該 tool 的 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 可能會跳過平行探索，而不是使用其自身的等效功能。該 skill 仍然會執行，但掃描較不徹底。有人提出了 harness-neutral 的重寫，但尚未合併。

**我實際上如何在 TypeScript 中 implementation deep modules？**

該 skill 沒有提供好的答案。人們經常要求一個 `TYPESCRIPT.md`，其中包含這些原則的具體檔案和 module 佈局，但它並不存在。該 skill 告訴你 deepening 屬於哪裡，以及 seam 後面放什麼。你自己必須將其轉為 package 或目錄結構。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示它正在運作

- Candidates 命名的是你 domain 的概念，而不是發明的 class 名稱：「Order intake module」，而不是「FooBarHandler」。
- Candidates 在你最近編輯過的檔案中，而不是在 repo 中沒人碰觸的部分。
- 執行期間沒有程式碼變更。唯一的 new file 是你 temp directory 中的 HTML 報告。
- 它在報告後停止，並詢問你想要哪個 candidate。它不會自行繼續。
- 每張卡片以 locality 或 leverage 解釋收益，並說明哪些 tests 會變得更簡單，而不只是「這更乾淨」。
- 當你因持久原因拒絕一個 candidate 時，它會提供記錄 ADR，以便下次執行不再建議它。

<a id="where-it-fits"></a>

## 它的位置

`improve-codebase-architecture` 是**定期維護**。你每隔幾天在 chain 之外執行它，以排隊工作，而不是執行工作。它的鄰居：

- [codebase-design](https://aihero.dev/skills-codebase-design) 擁有每個 candidate 使用的 depth-and-seam 詞彙。
- [grilling](https://aihero.dev/skills-grilling) 在你選擇 candidate 後走決策樹。
- [domain-modeling](https://aihero.dev/skills-domain-modeling) 在你做出決策時保持 `GLOSSARY.md` 和 ADRs 最新。

它的輸出是一個 idea，回到 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 或 [to-spec](https://aihero.dev/skills-to-spec) 的主建構流程中。它在主流程末尾的對應者是 [retro](https://aihero.dev/skills-retro)。此 skill 改進 agent 工作的程式碼，而 `retro` 在 build 後改進其周圍的環境（checks、standards、steering files）。關於哪個 skill 適合某種情況，[ask-matt](https://aihero.dev/skills-ask-matt) 是整個集合的路由器。
