<a id="what-it-does"></a>

## 功能說明

`implement-spec` 會接收 [spec](https://www.aihero.dev/ai-coding-dictionary/spec) 及其 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)，並在單次執行中完成所有工作。負責協調的 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 會將每個 ticket 交給在各自 git worktree 中工作的 implementer [subagent](https://www.aihero.dev/ai-coding-dictionary/subagent)，將每個完成的分支合併到單一 **integration branch**，對結果執行 [code-review](https://aihero.dev/skills-code-review)，並解決這些 tickets。

它將 tickets 視為 **task graph**，而非清單。阻擋邊（blocking edges）決定哪些工作可以開始，因此在任何時刻都會有一個 **frontier**，其中包含所有阻擋條件都已滿足的 tickets，而 frontier 上的每個 ticket 都會同時執行。這與逐一處理 tickets 的方式不同。圖的形狀決定了節奏，而非 tracker 上 tickets 的順序。

<a id="when-to-reach-for-it"></a>

## 適用時機

您透過輸入 `/implement-spec` 來啟動此功能，agent 不會自行主動使用它。

| 您的情況 | 建議使用 |
| --- | --- |
| 一個已拆分為帶有阻擋邊的 tickets 的 spec，您希望在一輪執行中完成 | `/implement-spec` |
| 一次處理一個 ticket，在您自己的 [context window](https://www.aihero.dev/ai-coding-dictionary/context-window) 中，並在 ticket 之間 [clearing](https://www.aihero.dev/ai-coding-dictionary/clearing) | [implement](https://aihero.dev/skills-implement) |
| 尚未拆分為 tickets 的 spec | 先使用 [to-tickets](https://aihero.dev/skills-to-tickets) |
| 沒有實際圖結構的小型工作 | 直接使用 [implement](https://aihero.dev/skills-implement) |

<a id="prerequisites"></a>

## 前置條件

- **Issue tracker。** 此 skill 會從 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 設定的 tracker 讀取 tickets，並在其上解決這些 tickets。如果尚未設定，它會停止並提示您先執行該設定，而不是自行猜測。
- **帶有阻擋邊的 tickets**，格式如同 [to-tickets](https://aihero.dev/skills-to-tickets) 所寫入的。如果沒有邊，圖結構將是扁平的，所有 ticket 都會同時開始。
- **一個能在背景執行 subagents 並為每個 subagent 提供 git worktree 的 [harness](https://www.aihero.dev/ai-coding-dictionary/harness)。** 此 skill 的存在目的是同時執行 tickets，因此在一次只執行一個 subagent 的 harness 上，它只會變成較慢的 `implement`。

<a id="the-integration-branch"></a>

## Integration branch

所有工作都會落在同一個分支上。每個 implementer 會：

1. 在開始前確認其 worktree 是基於 integration branch，
2. 使用 [tdd](https://aihero.dev/skills-tdd) 建構其 ticket，每次進行一個切片的紅綠迴圈，
3. 在回報完成前，將 integration branch 的最新提交合併到其自己的分支，以便後續合併為 fast-forward。

Tracker 決定是否要建立 pull request。如果您的 tracker 透過 PR 關閉工作，或者您要求建立 PR，此 skill 會在第一次合併後開啟一個 draft PR，並在最後將其標記為 ready。否則，執行會停在 integration branch 上，所有 tickets 都會以您的 tracker 關閉工作的方式解決，這在針對本地 markdown tracker 時可完全離線運作。

Implementers 透過 [context pointers](https://www.aihero.dev/ai-coding-dictionary/context-pointer)（spec、ticket、共享探索筆記、先前 commits）與 orchestrator 溝通，而非貼上摘要。這能保持每個 subagent 的 prompt 精簡，並為 orchestrator 的視窗保留空間以處理圖結構。

<a id="common-questions"></a>

## 常見問題

**這與我自己對每個 ticket 執行 `/implement` 有何不同？**

這正是此 skill 存在要回答的問題。在發布之前，人們一直自行建立類似版本，有一位使用者描述了這種需求：他們希望「subagents 來實作 tickets」，而不是「必須個別建立新 session 並告訴它們逐一實作 ticket，而一個 spec 可能包含超過 5 個 tickets」。使用 `implement` 時，您是排程者：每個 ticket 一個 [session](https://www.aihero.dev/ai-coding-dictionary/session)，中間進行清理，並自行追蹤哪些 tickets 已解除阻擋。`implement-spec` 將此工作交給一個協調 session。代價是您不再能即時閱讀每個 ticket 的工作內容；您會在最後審查 integration branch。要開始執行，請清理 context 並輸入 `/implement-spec`，附上指向 spec 的指標（issue 編號或檔案路徑）。對於沒有實際圖結構的小型變更，請跳過此功能並直接使用 `implement`。

**需要 GitHub 嗎？我希望它停在分支上。**

不需要了。一位喜歡開發中版本的使用者曾有完全相同的抱怨：「它最後會建立一個 PR，這需要像 GitHub 這樣的線上儲存庫。我希望它能離線執行相同的工作，並停在所有工作已合併的分支上。」現在執行會結束於 integration branch。只有當設定的 tracker 透過 PR 關閉工作，或您要求建立 PR 時，才會開啟 PR。因此，在本地 markdown tracker 上，執行會在所有 tickets 解決且工作已合併到分支上時結束。

**它的審查與修復迴圈執行了數小時，或不斷「修復」尚未建構的 tickets。**

當 `code-review` 在 skill 指定的唯一時點以外的地方執行時，這兩種情況都會發生。它將程式碼與整個 spec 進行比較，因此只有在所有 tickets 都完成後才有意義。如果在執行中途執行，每個未建構的 ticket 都會被視為失敗。Agent 隨後會建構該 ticket，這又會觸發另一次審查。最後，skill 會執行一次 `code-review` 並將所有發現交給一個修復 subagent，但它尚未說明在該次修復後何時停止。一位使用者報告了一個包含五個 ticket 的功能，「審查與修復迴圈大約花了四個小時」。如果您看到第二次廣泛審查開始，請告訴它針對已修復的發現執行聚焦檢查並停止。請預期第一次審查會發現實際問題。執行的輸出是一個 draft，需要審查來完成，而非可獨立發布的內容。

**它會像 implement 那樣驅動 tdd 嗎？**

現在會了，雖然最初不會。執行開發中版本的使用者注意到「implementer subagents 不會繼承 /tdd 指令」，因此當規模從一個 ticket 擴大到整個 spec 時，紅綠迴圈就停止了。現在每個 implementer 都會使用 `tdd` 建構其 ticket。目前仍沒有像 `implement` session 中那樣互動式同意 seams 的步驟，因此如果您希望固定 seams，請在 spec 或 tickets 中命名它們。

**兩個並行執行的 implementers 在同一個檔案上發生衝突，或為同一事物選擇了不同的名稱。**

Worktrees 不會消除衝突，而是將衝突延後到合併時才發生。從工單文字中寫入的封鎖邊，只是對每個工單將觸及哪些檔案的猜測，而兩個位於「程式碼庫不同部分」的工單，仍可能共用同一個訊息目錄、設定登錄檔或型別。每位實作者只能看到自己的工單與共用筆記，永遠看不到其他實作者的進行中工作，因此某位使用者的 Web 與行動端工單分別新增了相同的字串 `blockedSince` 與 `blockedOn`。當兩個前沿工單觸及同一個共用檔案時，應在它們之間新增封鎖邊，讓它們依序執行；或者讓探索筆記明確指定每個工單將新增的確切名稱。

**被封鎖的工單永遠不會啟動，即使其封鎖者已合併。**

這是 GitHub 上的一個已知問題。追蹤器的 blocked-by 計數只有在封鎖者「關閉」時才會減少，而工單通常在 PR 合併時關閉，這正是執行結束的時刻。追蹤器是啟動圖的正確來源，但在執行中途時資訊已過時。請指示協調器自行追蹤哪些工單已合併至整合分支，並據此計算前沿。

**這是否取代 Sandcastle 或 AFK 指令碼？**

不會。人們會這樣問，是因為這些 skill 現在已深入實作層面：「Sandcastle 還有意義嗎？你的 skill 現在似乎也能處理實作了。」`implement-spec` 在單一 harness 會話中指派一個 agent 負責協調，這不需要任何基礎設施，且讓你能夠觀察並引導過程。對於真正 [AFK](https://www.aihero.dev/ai-coding-dictionary/afk) 的工作，確定性的迴圈（[Sandcastle](https://github.com/mattpocock/sandcastle)、shell 指令碼、CI 工作）更快、更便宜且更可靠，因為沒有 agent 來做協調決策。

**某工單的關鍵測試在其 worktree 中被跳過，但報告為綠色（透過）。**

Worktree 只包含 git 追蹤的內容。讀取 gitignored 測試資料、本地資料庫或憑證的測試，可能會在該處靜默跳過。對於驗證依賴未追蹤內容的工單，請指示協調器改在主 checkout 中執行。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示運作正常

- 只要圖允許，多個實作者會同時執行，而非依序執行。
- 當工單的最後一個封鎖者進入整合分支時，該工單立即啟動，而非等到整個執行結束。
- 每個工單的追蹤記錄都顯示 `tdd` 正在執行，且在程式碼之前有一個失敗的測試。
- 合併至整合分支的操作是快進（fast-forward），而非衝突解決。
- 執行結束時，所有工單都在同一個分支上解決，且僅在你的追蹤器要求時才建立 PR。

<a id="where-it-fits"></a>

## 適用情境

`implement-spec` 是主鏈中的建置步驟，作為對每個工單執行一次 [implement](https://aihero.dev/skills-implement) 的並行替代方案：

```txt
grill-with-docs → to-spec → to-tickets → implement-spec → retro
```

其相鄰步驟為 [to-tickets](https://aihero.dev/skills-to-tickets)，它將封鎖邊宣告為任務圖；以及 [code-review](https://aihero.dev/skills-code-review)，它在收尾前對整合分支執行審查。[ask-matt](https://aihero.dev/skills-ask-matt) 則是當你不確定自己處於哪個流程時，對整套流程的路由器。
