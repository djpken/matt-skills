<a id="what-it-does"></a>

## 功能說明

`ask-matt` 是本儲存庫中各項 skill 的路由器。您描述目前的情況（一個無法著手的構想、一堆傳入的 bug 報告、或一個 [session](https://www.aihero.dev/ai-coding-dictionary/session) 已進行過久），它會指出適合的 skill 或 skill 序列，並說明該序列中哪些環節需要人類決策。

它只負責建議，然後停止。它不會盤問、撰寫 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)、開啟檔案或啟動它剛命名的 skill；您得到的回饋是下一步該輸入的內容，由您來輸入。它也是本儲存庫中 skill 的手動地圖，而非掃描您已安裝的內容，因此它不會將您路由至您自己的 skill 或其他作者的 skill。

<a id="when-to-reach-for-it"></a>

## 何時使用

您透過輸入 `/ask-matt` 來呼叫此功能；agent 不會自行呼叫它。

| 您的情況 | 路由器回饋的內容 |
| --- | --- |
| 有一個構想，但不知道從何開始 | 主流程的起點，以及建置規模是否小到可以跳過 spec |
| 來自他人的 bug 和請求不斷湧入 | [triage](https://aihero.dev/skills-triage) 的入口，以及為什麼您自己產生的 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 不屬於此處 |
| 兩個看似可互換的 skill | 兩者之間的界線，通常是一個具體的測試，而非品味問題。[grill-me](https://aihero.dev/skills-grill-me) 或 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 取決於您是否在工作目錄中；[grill-with-docs](https://aihero.dev/skills-grill-with-docs) 或 [wayfinder](https://aihero.dev/skills-wayfinder) 取決於工作量是否適合在一個 session 內完成 |
| 一個長 session 以及關於 [context](https://www.aihero.dev/ai-coding-dictionary/context) 的決策 | 在階段邊界處，針對五個選項的有序樹狀結構 |
| 一個您已選定的 skill | 沒有有用的資訊。直接呼叫該 skill。 |

<a id="prerequisites"></a>

## 先決條件

路由器只命名 skill，不負責安裝。它指向的所有內容都必須已安裝，建議才可執行，且它只知道本儲存庫中已推廣的 skill。

依賴追蹤器的路由（triage、`to-spec`、`to-tickets`、`implement`）假設 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 已在儲存庫中設定好 issue 追蹤器。即使尚未完成設定，路由器仍會建議這些路由。

<a id="flows-not-skills"></a>

## 流程，而非 skill

此 skill 的核心詞是 **flow**，即穿過各項 skill 的路徑，而非單一 skill。當您描述情況時，路由器會將您定位在流程的某個步驟上。這與「這裡是符合您關鍵字的 skill」是不同的答案。共有五種路由型別，skill 本身會完整描述它們：

- **主流程**，從構想到發布。Grill、spec、tickets、implement（一次處理一張 ticket，或使用 [implement-spec](https://aihero.dev/skills-implement-spec) 並行處理整個任務圖）、review，然後是 [retro](https://aihero.dev/skills-retro)，將建置過程的教訓回饋到 agent 的環境中。它有兩個分支。一個是原型繞道，用於需要可執行程式碼來解決的問題。另一個是 spec 與 tickets 的分拆，只有當建置跨越多個 session 時，其成本才值得。
- **入口**，用於產生工作然後併入主流程的情況：傳入的 bug 報告、某處損壞、或工作量過於模糊且龐大以至於無法在一個 session 中處理。
- **程式碼庫健康**，維護而非功能開發。[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 掃描程式碼以尋找深化機會，它找到的每一個機會都會作為一個構想重新進入主流程。
- **獨立專案**，位於所有流程之外，您單獨使用它們：原型、問卷、研究執行。
- **底層詞彙層**，其他 skill 在問題出在詞彙而非流程時會引用的兩個參考。

<a id="the-phase-boundary"></a>

## 階段邊界

此 skill 的另一個關鍵概念是 **階段邊界**。階段是 session 內的一塊工作（[grilling](https://www.aihero.dev/ai-coding-dictionary/grilling)、實作、QA），而兩個階段之間的邊界是「我該怎麼處理這個 context？」這個問題唯一屬於的地方。在階段中間沒有需要決策的事：繼續，或將剩餘部分拆分給 [subagents](https://www.aihero.dev/ai-coding-dictionary/subagent)。

| 選項 | 何時採用 |
| --- | --- |
| **Continue** | 下一個階段需要原封不動地保留此階段，或您還有 [smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone) 剩餘。這是唯一能將 session 保持為 [primary source](https://www.aihero.dev/ai-coding-dictionary/primary-source) 的動作，因此請先排除它 |
| **`/clear`** | 您身後的一切皆可丟棄。這是成本最低的選項，且如果您判斷錯誤，無法復原 |
| **[handoff](https://aihero.dev/skills-handoff)** | 有東西需要傳輸：新的 [harness](https://www.aihero.dev/ai-coding-dictionary/harness)、新的目錄、同事、或在階段中途分叉的側邊任務 |
| **Subagent** | 任務範圍足夠緊湊，可以讓您 [away from the keyboard](https://www.aihero.dev/ai-coding-dictionary/afk) |
| **`/compact`** | 以上皆非。這是預設選項，樹狀結構通常在此結束 |

人們常搞錯其中兩個，這就是為什麼路由器提供順序而不只是清單。`/handoff` 看起來像是 context 視窗之間移動的通用方式，但它不是。它只給您可攜性。`/compact` 是樹狀結構中的最後一個選項，而非第一個，因為它上方的四個選項中，每一個都更便宜或更精確。

<a id="common-questions"></a>

## 常見問題

**不是有一張按正確順序排列的 skill 清單嗎？**

人們不斷在 README 中要求這樣一張清單。這個 skill 就是那張清單。靜態表格會說 `wayfinder → to-spec → to-tickets → implement → code-review → retro`，但在大多數情況下是錯誤的。重要的是分支：是否有程式碼庫、建置是否跨越 session、談話是否能解決這個問題。代價是路由器是手動維護的，因此會落後於儲存庫。`/grilling` 在路由器命名它之前很久就已發布。

**它告訴我有一半的 skill 未安裝。**

這是一個已知的 bug，尚未修復。路由器路由您經過的大多數 skill 都設定為 `disable-model-invocation: true`，這意味著 harness 會將它們排除在注入 agent context 的 skill 清單之外。Agent 將該清單視為完整，並報告它們缺失。在一個報告的 session 中，它聲稱整個 spec-and-tickets 流程不存在，並重新路由到裸的 `/grilling` 和 `/tdd`。該 plugin 的二十七個 skill 中有十六個帶有此旗標，因此這是常見情況而非邊緣案例。它們已安裝。請照常輸入斜線命令，或檢查 `.claude-plugin/plugin.json`，它是判斷何者存在的權威來源。

**它描述了某個 skill 的行為，但該 skill 並不那樣做。**

這也是一個真實的 bug，而且尚未修復。路由器是根據每個 skill 的一行摘要來回答，而不是根據 skill 本身。一份詳細報告追蹤了單一 session 中的三個例項，其中包括僅根據摘要「將 thread 轉為 spec」就建議跳過 [to-spec](https://aihero.dev/skills-to-spec) 的情況。路由器從未開啟 `to-spec/SKILL.md`。在每個案例中，它都是在使用者提出異議後才進行驗證，從未主動執行。在那裡跳過 `to-spec` 導致遺漏了真實的 seam check，而產生的 tickets 也低估了工作量。當路由器對另一個 skill 做出您將據以行動的陳述時，請要求它先開啟該 `SKILL.md`。同樣適用於地圖完全未涵蓋的問題，例如是否使用 [plan mode](https://www.aihero.dev/ai-coding-dictionary/agent-mode)：該答案來自 [model](https://www.aihero.dev/ai-coding-dictionary/model) 的推論，而非在此處書寫的內容。

**為什麼是散文而不是編號清單？**

這是一個合理的抱怨。一個 open issue 認為大部分路由是確定性的，而敘述形式使得掃描變得困難。沒有限制您要求壓縮形式：「只給我順序」就能得到順序。散文承載了條件部分：分支、預期需要人類決策的地方，以及在步驟之間清除或壓縮的地方。扁平的清單恰恰遺漏了這些內容。

**它能否路由到我自己的 skills，或其他作者的 skills？**

不能。有三個獨立的提案要求建立一個能讀取您本地 `skills/` 目錄並根據已安裝內容進行推薦的路由器。`ask-matt` 不是那樣。它是一組特定內容的手動維護地圖，對於您撰寫或從其他地方安裝的 skills 一無所知。

**它告訴我編輯一個 SKILL.md。**

該建議通常是正確的，但編輯很少能持久。有人問它如何讓 [implement](https://aihero.dev/skills-implement) 關閉 tickets，被告知要在 skill 中新增一行，並立即發現了問題。`npx skills update` 會覆蓋檔案，且 plugin 安裝是唯讀的。將常駐行為放在您自己的 `CLAUDE.md` 或 `AGENTS.md` 中，或在呼叫時說明。您在 prompt 中進行的變更會在更新後保留。人們以此方式將流程指向 Linear 而非 GitHub，或詢問哪些 open tickets 可以並行執行。

**它命名了一個我沒有的 skill，或漏掉了一個我有的。**

在假設它已消失之前，請檢查 changelog 是否有重新命名。`writing-great-skills` 變成了 [writing-for-agents](https://aihero.dev/skills-writing-for-agents) 且沒有別名，`to-prd` 變成了 [to-spec](https://aihero.dev/skills-to-spec)，而 `pathfinder` 變成了 [wayfinder](https://aihero.dev/skills-wayfinder)。四個 skills 被直接退役並合併到吸收它們的 skills 中：`ubiquitous-language`、`design-an-interface`、`qa` 和 `request-refactor-plan`。如果它漏掉了您擁有的 skill，那是路由器自身的滯後，如上所述。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示它運作正常

- 它以命名要輸入的內容結束並停在那裡，而不是自己開始工作。
- 它給出的路由提到了在哪裡清除或壓縮 context，以及預期您在哪裡進行審查，而不僅僅是 skill 名稱的列表。
- 當兩個 skills 很接近時，它會說明哪一個是正確的，以及為什麼另一個不適合您。
- 它對另一個 skill 行為的任何陳述，都會在 trace 中顯示為它正在讀取該 skill 的 `SKILL.md`。
- 您在它返回的內容中認出了自己的情況，而不是最接近的通用場景。

<a id="where-it-fits"></a>

## 適用範圍

`ask-matt` 是一個**獨立路由器**，位於整個集合之上。它從不是鏈中的一個步驟。它指向每個鏈，而其他 docs 頁面連結回它，因此它們都不必重新繪製圖表。從這裡您最常到達 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，即主流程的開頭，或 [triage](https://aihero.dev/skills-triage)，即針對到達的工作而非您啟動的工作的入口。

它是關於其所描述 skills 的 [secondary source](https://www.aihero.dev/ai-coding-dictionary/secondary-source)。當路由器與 `SKILL.md` 不一致時，`SKILL.md` 是正確的。
