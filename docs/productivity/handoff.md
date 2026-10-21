<a id="what-it-does"></a>

## 功能說明

`handoff` 會將您目前的對話濃縮為一份**交接檔案**：這是一份 markdown 檔案，寫入作業系統的暫存目錄，而非工作區，讓新的 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 可以讀取並接手工作。

它提供的是**可攜性**，而非壓縮，因此這個 skill 的範圍比聽起來更窄。只有當工作必須*移動*時，您才需要這個檔案：移到新的 [harness](https://www.aihero.dev/ai-coding-dictionary/harness)、新的目錄、同事那裡，或是您想要分叉出去的旁支任務。如果沒有東西需要移動，您就不需要交接。留在 [session](https://www.aihero.dev/ai-coding-dictionary/session) 中、`/clear`、[subagent](https://www.aihero.dev/ai-coding-dictionary/subagent) 以及 `/compact` 涵蓋了普通的階段結束情況，而 `/compact` 比這個 skill 更常處理這種情況。

<a id="when-to-reach-for-it"></a>

## 何時使用

您透過輸入 `/handoff` 來呼叫此功能；agent 不會自行使用它。請附上一則關於下一個 session 用途的備註，skill 就會為該目的撰寫檔案。

只有四個觸發條件，且僅限這四個：

| 情境 | 為什麼需要檔案 |
| --- | --- |
| 更換 harness (Claude → Codex) | 新的 harness 無法看到舊的 [context](https://www.aihero.dev/ai-coding-dictionary/context) |
| 移動到不同的目錄或儲存庫 | 原型目錄是常見情況 |
| 將工作傳送給同事 | 他們需要能讀取的東西 |
| 分叉階段中發現的旁支任務 | 您繼續工作；第二個 agent 接手分叉 |

對於其他情況（相同的 harness、相同的目錄、您已完成 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 並準備進入實作階段），請使用 `/compact`。[ask-matt](https://aihero.dev/skills-ask-matt) 擁有在階段邊界處涵蓋所有五個選項的有序樹狀結構。

<a id="branching-is-the-use-people-skip"></a>

## 分叉是人們常忽略的用途

這個 skill 的描述讀起來像是 session 恢復：寫下摘要，在此結束，在那裡恢復。這樣讀起來，它看起來像是較差的 `/compact`，所以人們會跳過它。分叉情況是值得了解的。您**留在目前的 session 中**，並將累積的 context 副本交給第二個並行工作的 agent。

透過 [prototype](https://aihero.dev/skills-prototype) 的繞道就是這樣運作的。您深入設計對話中，遇到一個只有執行程式碼才能解決的問題，而您不希望在已建立的執行緒上花費時間去找出答案。交接給原型 session，取得答案，將答案交回，並從原始執行緒中引用它。工作交叉了兩次，您的原始對話保持活躍，且您無需重新解釋任何內容。

階段邊界處的五個選項中有三個保留了不同的東西：`/compact` 保留了您的意圖，`/clear` 什麼都沒保留，`/handoff` 保留了工作移動的能力。

<a id="what-travels-and-what-doesnt"></a>

## 什麼會移動，什麼不會

檔案承載了活躍的執行緒（正在進行什麼、為什麼、接下來是什麼），並附有一個**建議 skills** 章節，指明下一個 agent 應該使用什麼。skill 在寫入檔案前會移除機密資訊。

它刻意不承載任何已寫下的內容。檔案透過路徑或 URL 引用規格、計畫、ADR、issues、commits 和 diffs，絕不複製它們。這讓檔案保持精簡，並讓已確定的細節保持在單一位置，而不是兩份會逐漸產生差異的副本。

<a id="common-questions"></a>

## 常見問題

**是交接還是濃縮？**
除非有東西需要移動，否則使用 `/compact`。留在同一個任務上是濃縮，不是交接。當 harness 和目錄保持不變，且您需要保持在迴圈中時，階段邊界樹狀結構大多數時候會結束於 `/compact`。`/handoff` 不會更好地進行摘要。它的優勢在於結果是一個檔案，您可以將其帶到 `/compact` 無法到達的地方。

**那麼 compact、clear 和 handoff 之間的實際差異是什麼？**
每一種都保留了不同的東西。`/compact` 壓縮此 context 並在新視窗中繼續，因此您的意圖得以保留。`/clear` 清空視窗並從零開始。當身後的一切都可丟棄時，這是正確的，但如果不是，您無法撤銷。`/handoff` 寫入一個可攜檔案，因此工作能在移動到其他地方時存活。這三者都將**[原始資料](https://www.aihero.dev/ai-coding-dictionary/primary-source)**（對話發生的樣子）轉化為**[二手資料](https://www.aihero.dev/ai-coding-dictionary/secondary-source)**（其摘要）。繼續是唯一不這樣做的選項，這就是為什麼您首先排除它。

**我的交接檔案去哪了？**
它進入暫存目錄，這是該 skill 最常被報告的問題。skill 將其解析為 `$TMPDIR`，否則為 `/tmp`（在 Windows 上為 `%TEMP%`），但路徑很長。請要求返回路徑，並在繼續之前保留它。使用暫存目錄是刻意的，因為交接是一個傳輸中的檔案，而非您維護的產物。暫存目錄也不持久，如下個問題所述。

**我的交接在 session 之間消失了。**
某些環境會在 session 之間清除暫存目錄（Codex 是報告的案例），而重新啟動會清空 `/private/tmp`。如果下一個 session 不在一小時內開始，或在不同的 harness 下開始，請在檔案寫入後立即自行將其複製到持久位置。這也適用於檔案*指向*的任何內容：如果排程引用了暫存目錄中的其他檔案，下一個 agent 無法跟隨。

**我實際上是怎麼把它交給下一個 agent 的？**
開啟新的 session 並指向該路徑：讀取這個檔案，然後繼續。指向檔案，而不是將摘要貼到 shell 命令中。當您將包含反引號或 `$(...)` 的摘要插入到 `claude "<summary>"` 中時，shell 會破壞它。通常的結果是靜默截斷而非錯誤，因此新的 agent 帶著不完整的簡報開始，而沒有任何提示告訴您。

**這與 `/branch`、`--fork-session` 或內建的 `/handoff` 相同嗎？**
它們相似，但不相同。`/branch` 在這裡不是已發布的 skill；`/handoff` 是正式名稱。分叉繼承 context 的精確副本；這個 skill 產生針對特定下一個任務的*目標*壓縮，並存於檔案中。當分叉可行時（相同機器、相同 harness、相同目錄），分叉的工作量較少。當目的地是分叉無法到達的地方時，檔案是更好的選擇。

**什麼時候應該放在 `CLAUDE.md` 中？**
問問自己下個月是否還成立。`CLAUDE.md` 是關於專案的常駐 context，無論相關與否都會載入每個 session。交接是關於一個正在進行的工作，一旦該工作完成就無用了。如果您不斷重新解釋一個事實，它應該放在 `CLAUDE.md` 中。一個未完成的任務屬於交接。

**它捕捉了是什麼，而不是為什麼。**
這是一個合理的批評，人們經常提出這一點。有兩點可以幫助改善。傳遞引數（告訴它下一個 session 的用途），讓 skill 保留與*該用途*相關的推理，而不是將其扁平化。另外，留意 session 從未驗證的自信陳述，例如「X 尚未建置」或「Y 已完成」。下一個 agent 會信任該檔案，而不會重新檢查，因此以事實形式寫下的信念，會成為後續所有內容的錯誤前提。在移交檔案之前先閱讀它，並將任何僅憑假設的內容降級處理。

**為什麼它是 skill 而不是 slash command？**
兩者都可行，適合不同的情境。作為 skill，它透過與此處其他所有專案相同的安裝路徑進行發布和更新，這使其具有可共享性。阻止 agent 自行觸發它的，是它的 frontmatter，而非機制本身。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示它運作正常

- 該檔案僅佔對話的一小部分，且規格、問題和 diff 在其中以路徑和 URL 的形式出現，而非複製的文字。
- 你可以直接閱讀它，無需開啟原始 session，就能知道下一步該做什麼。
- 新的 agent 會直接開始工作，而不是要求你重新解釋設定。
- 在 fork 的情況下，當你回到原始 session 時，它保持不變。
- suggested-skills 部分會列出你原本會自行選擇的 skill。
- 其中沒有任何內容是金鑰、token 或密碼。

<a id="where-it-fits"></a>

## 適用位置

`handoff` 是一個**隨時可用的獨立工具**。它在 session 之間運作，而非在 build chain 內部。它的範圍較窄，在階段邊界處，你使用它的頻率會低於其他四個選項。它最接近的鄰居是 [prototype](https://aihero.dev/skills-prototype)，因為 prototype 存在於自己的目錄中，而進出的往返正是這個 skill 所針對的操作。當你在邊界處不確定該繼續、清除、移交、委派還是壓縮時，[ask-matt](https://aihero.dev/skills-ask-matt) 擁有對這五種操作進行排序的樹狀結構，並引導你使用該集合中的其他部分。
