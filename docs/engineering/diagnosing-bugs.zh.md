<a id="what-it-does"></a>

## 功能說明

`diagnosing-bugs` 針對難以解決的 bug 或效能退化執行六階段診斷流程：建立重現步驟、最小化問題、對假設進行排序、加入偵測程式碼、搭配回歸測試進行修復、清理。

在存在**緊密**的回饋迴路之前，它不會讓 agent 形成理論。這個回饋迴路必須包含一個已命名且已執行過一次的指令，該指令在此 bug 上會顯示為紅色，在修復後會顯示為綠色。收到 bug 報告後，coding agent 預設會閱讀程式碼並進行猜測。這個 skill 會阻擋這種行為。如果不存在能顯示紅色的指令，就不會進入第二階段。這個單一閘門正是此 skill 的目的。一旦迴路建立完成，後續的所有步驟（二分法、假設測試、偵測）都是機械化的。

<a id="when-to-reach-for-it"></a>

## 何時使用

輸入 `/diagnosing-bugs`，或者當任務符合條件時，agent 會自行呼叫。它是由模型呼叫的，會在出現「診斷」或「debug this」等詞語時觸發，或在報告指出某事物損壞、丟擲錯誤、失敗或緩慢時觸發。

在困難的情況下使用它：第一眼看不出的 bug、間歇性的 flake、在兩個已知良好狀態之間引入的退化。它故意設計得緩慢且徹底，因此不適合用於希望在一則訊息中獲得答案的問題。

| 您的情況 | 該去哪裡 |
| --- | --- |
| 您可以描述為症狀的特定缺陷 | 此 skill |
| 具有已知前後狀態的緩慢端點或計時退化 | 此 skill。它有一個效能分支（測量基準，然後進行二分法） |
| 「這個程式碼庫中的瓶頸在哪裡？」，沒有特定症狀 | 不是此 skill。它診斷一個已知的失敗，而不是進行稽核 |
| 來自他人的原始 bug 報告，尚未確認或撰寫 | [先進行 triage](https://aihero.dev/skills-triage) |
| 用於回答設計問題的拋棄式程式碼，而非追蹤缺陷 | [prototype](https://aihero.dev/skills-prototype) |
| 以測試優先方式建置計畫中的行為 | [tdd](https://aihero.dev/skills-tdd) |
| 在 bug 修復後，詢問什麼能防止該 bug | [retro](https://aihero.dev/skills-retro)，在同一個 session 中執行 |
| 不存在良好的縫隙來鎖定 bug | [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture)，由您自行啟動 |

<a id="the-tight-loop-is-the-skill"></a>

## 緊密迴路即是此 skill

第一階段投入最多的精力，因為它是唯一困難的階段。此 skill 列出了建立迴路的方法，大致按優先順序排列：

1. 在能觸及 bug 的任何縫隙處建立一個失敗的測試。
2. 針對執行中的 dev server 的 curl 或 HTTP 指令碼。
3. 使用 fixture 輸入的 CLI 呼叫，並與已知良好的快照進行 diff。
4. 針對 DOM、console 或網路進行斷言的 headless browser 指令碼。
5. 重放的 capture：儲存的請求、payload 或事件紀錄，在隔離環境中透過程式碼路徑執行。
6. 拋棄式 harness：系統的最小子集，一個函式呼叫。
7. 針對「有時輸出錯誤」的 property 或 fuzz 迴路。
8. 您可以交給 `git bisect run` 的二分法 harness。
9. 差異迴路：相同輸入，舊版本對新版本。
10. [human-in-the-loop](https://www.aihero.dev/ai-coding-dictionary/human-in-the-loop) bash 指令碼，最後手段。此 skill 為此提供了 `scripts/hitl-loop.template.sh`：agent 執行指令碼，您在終端機中遵循提示，您的答案會以可解析的輸出形式返回。

*一個* 迴路不是目標。**緊密**才是：快速（數秒）、確定性（每次執行結果相同）、精準（斷言您的確切症狀，而非「沒有崩潰」），且可由 agent 在沒有您的情況下執行。一個 30 秒且不穩定的迴路幾乎與沒有迴路一樣差。對於偶爾出現的 bug，目標不是乾淨的重現，而是**更高的重現率**：迴圈觸發條件、並行化、增加壓力、注入 sleep，直到 flake 率高到足以進行 debug。

當 agent 無法建立迴路時，此 skill 會告訴它停止並說明情況，列出它嘗試過的方法，並向您請求 [environment](https://www.aihero.dev/ai-coding-dictionary/environment) 存取許可權、已 capture 的產物，或允許加入暫時性偵測程式碼。它不得繼續形成假設。

<a id="the-gates-between-phases"></a>

## 階段之間的閘門

這些階段是閘門，而不是檢查清單。在特定條件為真之前，agent 無法進入該階段。

| 閘門 | 必須為真的條件 |
| --- | --- |
| 進入第二階段 | 一個已命名、已執行並貼上輸出的指令，該指令在此 bug 上能顯示為紅色 |
| 進入第三階段 | 重現步驟已重現*且*已最小化：每個剩餘元素都是重現 bug 所必需的 |
| 進入第四階段 | 存在 3 到 5 個已排序且可證偽的假設，每個假設都陳述其預測，並在測試任何假設之前展示給您 |
| 進入第五階段 | 偵測程式碼對應到特定預測，一次一個變數，每個 debug log 都有像 `[DEBUG-a4f2]` 這樣的標籤，以便透過一次 grep 找到它們全部進行清理 |
| 完成 | 原始重現步驟不再重現，偵測程式碼已移除，且 commit message 指出最終被證明正確的假設 |

第五階段有一個例外。agent 在修復之前撰寫回歸測試，但僅當存在**正確的縫隙**時：即測試能模擬在呼叫點發生的真實 bug 模式的縫隙。如果唯一可用的縫隙太淺，此 skill 會告訴 agent 說明情況，而不是撰寫一個會帶來錯誤信心的測試。缺失的縫隙本身是一個發現，agent 會記錄它而不是隱藏它。

<a id="common-questions"></a>

## 常見問題

**它在我只想要直接答案的快速問題上觸發。**
這是此 skill 最常被報告的問題。特別是在 GPT-5.6-Sol 上，使用者報告它在對問題的普通描述上觸發：「模型觸發了相當正式的 diagnosing-bugs skill。然後它繼續建構重現場景（通常建立價值有限的 mock 場景），然後才給我回應或建議。這導致了相當大的回應延遲。」四個人分別在 [issue #578](https://github.com/mattpocock/skills/issues/578) 上報告了相同問題。被接受的修復方法是從較輕量的方法開始，僅在問題需要時才轉為完整診斷，但該變更尚未發布。此 skill 是針對 Claude Code 的呼叫行為進行調整的，而具有較低啟用閾值的 [model](https://www.aihero.dev/ai-coding-dictionary/model) 會過於頻繁地觸發它。在修復發布之前，變通方法是說明您的要求（「只回答這個問題，不要診斷」）或在您的 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 中禁用其模型呼叫。

**我可以指向一個程式碼庫並詢問效能問題在哪裡嗎？**
不可以。它診斷一個您已經能命名的失敗。其效能分支是針對具有症狀的退化（建立基準測量，然後進行二分法，先測量後修復），而不是針對主動掃描。針對主動版本的 skill 曾被 [提出並關閉](https://github.com/mattpocock/skills/issues/431)；目前沒有 skill 執行此功能。

**它在撰寫修復之前會停止並詢問我嗎？**
不。只有第三階段設有人工檢查點：在測試任何假設之前，會先向您顯示排序後的假設清單；若您不在場，agent 會依其自身的排序繼續執行。在插樁與修復之間沒有閘門，因此 agent 可能在您尚未同意其根本原因之前就開始撰寫程式碼。[Issue #124](https://github.com/mattpocock/skills/issues/124) 要求設定該閘門，目前仍處於開啟狀態。若您希望有此功能，請在呼叫該 skill 時說明。

**我已經針對此 bug 報告執行過 `/triage`。這是否又是重複的工作？**
部分是，且兩個 skill 均未說明此點。正如一位讀者所言：「Triage 的步驟 3 本質上是 diagnosing-bugs 第一至第二階段的淺層、有限例項，但兩個檔案均未提及彼此。」Triage 執行有限的「這是否真的是 bug，以及表面現象為何」檢查；此 skill 則執行徹底版本。先執行 triage 並非浪費，因為其驗證通常能提供第一階段所需的大部分原始資料。但請預期此 skill 會完整重做該工作，且不會有交叉參考告知您這一點。

**它貼上的重現輸出是否會洩露機密？**
有可能。該 skill 要求 agent 貼上呼叫指令及其輸出，並請求 HAR 檔案、紀錄傾印和核心傾印等產物。沒有任何指示要求 agent 對這些內容進行清理。[Issue #674](https://github.com/mattpocock/skills/issues/674) 正是提出此問題（憑證、令牌、cookie 和個人資料被複製到聊天、issue 或 PR 中），並建議實施遮蔽防護措施。該 issue 目前開啟且尚未實現。目前請將遮蔽視為您的責任，特別是在輸出內容發布到任何公開場所之前。

**我的安全掃描器將此 skill 標記為高風險。**
Snyk 將其標記為高風險，且該標記為誤報。這是該集合中唯一隨附可執行 shell 指令碼（`hitl-loop.template.sh`）並包含執行該指令碼及 curl 開發伺服器指示的 skill。隨附的 `.sh` 檔案、執行指示以及外發 HTTP 請求，足以觸發靜態掃描器。該指令碼本身約為 30 行 `read -r -p` 提示，會暫停以等待人工輸入。掃描器評估的是該 skill 可能執行的動作，而非已證實的漏洞利用。

**`/diagnose` 發生了什麼事？**
v1.0.0 將其重新命名為 `/diagnosing-bugs`。舊名稱已不存在。您任何串接 `/diagnose` 的內容（例如包裝 skill 或已儲存的提示）都需要更新。

<a id="its-working-if"></a>

## 若符合以下條件，則表示運作正常

- 在提出任何單一理論之前，它會先向您顯示指令及其紅色輸出。若理論先於輸出出現，則表示該 skill 未正確執行。
- 它重現的失敗是您報告的那個，而非途中發現的相近失敗。
- 它在開始猜測之前會縮小重現範圍，並能告訴您為何需要每個剩餘部分。
- 在測試任何假設之前，它會向您顯示 3 至 5 個排序後的假設清單，每個假設都附有您可以證偽的預測。
- 它新增的每個除錯紀錄都帶有類似 `[DEBUG-a4f2]` 的標籤，當它宣告完成時，針對該標籤的 grep 搜尋結果為空。
- commit 或 PR 訊息會指明哪個假設是正確的。
- 當它無法透過測試鎖定 bug 時，會如此說明，而非撰寫淺層的測試。

<a id="where-it-fits"></a>

## 適用情境

`diagnosing-bugs` 是隨時可用的獨立工具。當某項功能損壞時，您啟動它；當修復及其回歸測試完成時，它結束。它不保留任何狀態，也不需要預先設定。[ask-matt](https://aihero.dev/skills-ask-matt) 將「某項功能損壞」路由至此。

有兩個相鄰工具很重要。[retro](https://aihero.dev/skills-retro) 在其之後執行：一旦修復完成，在同一 session 中執行它，以詢問什麼措施本可防止該 bug，因為此時 session 擁有的資訊比開始時更多。`diagnosing-bugs` 本身從不呼叫 `retro`，因為 `retro` 是由使用者呼叫的。[triage](https://aihero.dev/skills-triage) 在其之前執行，適用於來自他人的原始報告 bug，並執行相同前兩個階段的較淺層版本。
