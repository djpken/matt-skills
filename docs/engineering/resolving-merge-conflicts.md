> **已封存。** 此 skill 已在 v1.3.0 版本中從 plugin 移除，不再維護。沒有任何替代品：agent 會透過合併或 rebase 衝突來運作，而不需要專用的 skill。此頁面保留以供參考。

<a id="what-it-does"></a>

## 功能說明

`resolving-merge-conflicts` 會逐步處理進行中的 git merge 或 rebase，逐個處理 hunk，然後執行專案自有的檢查，並以 commit 完成該操作。

它拒絕將衝突視為純文字問題。在處理任何 hunk 之前，它會追溯每一側的 **[primary source](https://www.aihero.dev/ai-coding-dictionary/primary-source)**（commit 訊息、PR、原始 issue），因此它是在兩種意圖之間做選擇，而非在兩段文字之間做選擇，並在相容之處保留兩者。若不相容，它會選擇符合合併宣告目標的一側，並指出取捨。它不會發明新的行為來掩蓋衝突，也絕不使用 `--abort`。它總是將合併推進到完成的 commit。

<a id="when-to-reach-for-it"></a>

## 何時使用

輸入 `/resolving-merge-conflicts`，或當任務符合條件時，[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 會自動使用它。

當 git 已因無法自行解決的衝突而停止時，請使用此功能。它的範圍限於眼前的衝突，而非衝突兩側的任何其他內容：

| 您的情況 | Skill |
| --- | --- |
| 合併或 rebase 進行中，樹狀結構中有衝突標記 | 此 skill |
| 合併已完成，但某些功能因您看不見的原因而出錯 | [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs) |
| 規劃如何切分工作以減少分支衝突 | 兩者皆非：請參閱下方的並行工作問題 |

<a id="primary-sources-over-ours-and-theirs"></a>

## 優先使用 primary sources 而非 `ours` 和 `theirs`

此 skill 的存在是為了防止透過標記來解決問題：`--ours`、`--theirs`，或手動刪除看似較不重要的區塊，以便移除標記並讓建置成功編譯。這種解決方式可能在語法上完美無瑕，但仍可能悄悄遺漏某人有意進行的變更。

您無法保留未曾閱讀過的意圖。因此，工作從歷史記錄開始（commits、PRs、[tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)），然後才移至 diff。檢查步驟的存在也是基於相同原因。此 skill 會找到儲存庫自有的 [automated checks](https://www.aihero.dev/ai-coding-dictionary/automated-check) 並在 commit 前執行它們，因為合併是 git 中最容易產生同時滿足兩個分支卻無法透過任一分支測試的程式碼的地方。

<a id="common-questions"></a>

## 常見問題

**Claude Code 本身已經能很好地解決衝突。為什麼還需要這個 skill？**

增加的價值在於「找到 primary sources」和「執行回饋迴路」這兩個步驟，否則您每次都得手動提示。未受提示的 agent 通常會僅憑 diff 產生一個合理的解決方案並就此停止。此 skill 的價值在於它不讓 agent 跳過的兩個步驟：閱讀每一側存在的原因，以及事後執行檢查。與良好的 [model](https://www.aihero.dev/ai-coding-dictionary/model) 相比，這是一個微小的提升，且這是有意為之。至少有一位讀者預測，隨著模型改進，這整個 skill 將變成無效操作。

**我是否應該讓並行 agent 避免操作相同檔案，以從根本上避免衝突？**

大部分情況下不需要。在並行任務之間劃分檔案的代價高於其效益，因為 agent 處理合併衝突的能力已足夠好，使得取捨不如表面看來那麼嚴峻。唯一值得保持的紀律是先進行大型重構。當大型重新命名在十個分支分叉後才落地時，那才是仍然昂貴的情況。

來自並行 worktrees 使用者報告的一個注意事項：當兄弟 [sessions](https://www.aihero.dev/ai-coding-dictionary/session) 各自在自己的樹中建立 ticket 時，合併回去最好由撰寫該變更的 session 執行，因為它是唯一已經知道意圖的一方。如果一個 agent 在最後解決所有人的衝突，它會失去 [context](https://www.aihero.dev/ai-coding-dictionary/context)，而這是此 skill 第 2 步必須重新建立的內容。

**為什麼絕不使用 `--abort`？**

中止會丟棄解決工作，並讓您在下次嘗試時回到相同的、未變化的衝突。此 skill 是為合併將要發生的情況而撰寫的。如果您決定它不應該發生，那是在呼叫前做出的決定，而非迴圈內的一個分支。

<a id="its-working-if"></a>

## 運作正常的指標

- Agent 在解決問題時會引用 commit 訊息、PR 或 issues，而不只是 diff hunks。
- 每個 hunk 最終都包含兩側的行為，或包含明確註記，說明遺棄了什麼以及原因。
- 結果中不會出現任何兩個分支中都不存在的內容。
- Agent 找到了 typecheck、tests 和 format，並在 commit 前執行它們並透過，而不是在您注意到某些東西壞掉之後才執行。
- 您以乾淨的樹狀結構結束，操作已完成，包括多 commit rebase 中所有剩餘的 commit。

<a id="where-it-fits"></a>

## 適用位置

這是一個隨時可用的獨立功能，不依賴任何其他 skill：它在 git 停滯時開始，在樹狀結構乾淨且已 commit 時結束。它唯一的真正鄰居是 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs)，該功能在合併乾淨解決但合併後的程式碼行為異常時接手：這是一個診斷問題，而非衝突問題。它位於主要的從構想到發布流程之外，因此 [ask-matt](https://aihero.dev/skills-ask-matt) 是關於其前後執行內容的地圖。
