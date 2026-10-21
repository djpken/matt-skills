<a id="what-it-does"></a>

## 功能說明

`retro` 會回顧一次程式編寫 [session](https://www.aihero.dev/ai-coding-dictionary/session)，並針對 agent 的 **[environment](https://www.aihero.dev/ai-coding-dictionary/environment)** 提出改進建議，讓下一次執行更加順利。它會讀取該 session 自身的記錄（預設為目前 session，或您在 session 紀錄中指定的 session）。它會找出 agent 遇到困難的時刻，並提供一份候選修正清單，依嚴重程度由高到低排列。

它改變的是環境，而非程式碼。舉例來說，agent 提交的 bug、它花了二十次 [tool calls](https://www.aihero.dev/ai-coding-dictionary/tool-call) 才找到的檔案，或是審查者遺漏的規則。`retro` 不會直接修正這些問題。它會詢問儲存庫中有哪些因素導致這些問題發生，並提出能防止下次再犯的檢查、指標或標準。它也僅提出建議。在您選擇某個候選方案之前，不會有任何變更。

<a id="when-to-reach-for-it"></a>

## 何時使用

您透過輸入 `/retro` 來呼叫此功能，agent 不會自行使用它。

在一個比預期更困難的 session 結束時使用它。例如，agent 花了太長時間尋找某樣東西、犯了工具本可捕捉的錯誤，或需要它無法取得的資訊。順利的 session 沒什麼可教的，發現的問題來自困難的 session。如果您想對該 session 產生的程式碼進行評定，請改用 [code-review](https://aihero.dev/skills-code-review)。

<a id="where-the-findings-land"></a>

## 發現的問題落在哪裡

每個候選方案屬於一個類別，類別決定修正方案的位置：

| Session 中出了什麼問題 | 如何修正 |
| --- | --- |
| Agent 花了很長時間才找到檔案或事實 | 從它已經讀取的檔案中提供 **navigation pointer** |
| 它犯了工具本可捕捉的錯誤 | 一個 **[automated check](https://www.aihero.dev/ai-coding-dictionary/automated-check)**：lint 規則、型別、測試、pre-commit hook、CI job |
| 審查者遺漏了需要判斷的錯誤 | 在 `CODING_STANDARDS.md` 中為審查 agent 新增規則 |
| `AGENTS.md` 或 `CLAUDE.md` 體積過大 | 將其引導內容移出，放入標準或檢查中 |
| 某次 tool call 的代價高於其回傳內容 | 簡化該工具，或替換它 |
| 引導檔案中充滿了不產生任何影響的行 | 刪除這些 **no-ops** |
| Agent 需要它無法取得的資訊 | 擴大其存取許可權：將 dev server 紀錄 tee 到檔案，或提供對某個服務的唯讀存取權 |

核心概念是標準屬於 **reviewer**，而非 implementer。實作 agent 面臨最大的 context 壓力，因為它需要探索、撰寫程式碼並除錯失敗。審查 agent 只收到 diff 和沒有其他東西。因此，新規則應放在有空間應用它的地方，也就是審查階段。它絕不會放入 [AGENTS.md](https://www.aihero.dev/ai-coding-dictionary/agents-md)，因為該檔案會載入每個 session 的 [context window](https://www.aihero.dev/ai-coding-dictionary/context-window)，無論其是否相關。

在 `retro` 撰寫任何規則之前，它會對違規行為進行分類。**mechanical** 違規（例如禁止使用的 API、import 結構、檔案位置規則）會獲得確定性的檢查，因為檢查可以失敗，而標準檔案中的一句話不能。只有真正的判斷事項，即 linter 無法強制執行的部分，才會變成文字。如果儲存庫完全沒有護欄（沒有 pre-commit hook，也沒有執行 lint、typecheck 和測試的 CI job），`retro` 會將此報告為其自身的發現。

<a id="common-questions"></a>

## 常見問題

**它會自己撰寫 lint 規則，還是等待確認？我可以設定它在每個 session 後自動執行嗎？**

它會等待。`retro` 僅提出建議。在您選擇某個候選方案之前，不會有任何變更，因此它不會手動編輯任何內容，也不會自動安裝 hook。這是刻意設計的。一位使用者在「被自動 hook 阻擋了良好變更」後，要求了完全相同的功能。決定什麼值得永久檢查需要判斷力，因此該 skill 保持 [human-in-the-loop](https://www.aihero.dev/ai-coding-dictionary/human-in-the-loop) 且由使用者呼叫。有些使用者會在每次實作執行後執行它。但順利的 session 沒什麼可教的，因此每個 session 後執行主要會產生沒人需要的規則。沒有 dry-run 模式。建議的檢查與其他程式碼一樣，因此在讓它阻擋合併之前，請先對儲存庫進行測試。

**這會不會讓 lint 規則永遠累積？它會建議移除規則嗎？**

部分會，這是它最弱的地方。它可以移除文字：引導檔案中的 no-ops，以及 `AGENTS.md` 或 `CLAUDE.md` 中屬於標準或檢查的引導內容。當檔案過大時，它會標記這些專案以供刪除。它根據所讀取的單一 session 進行判斷，因此請將每一項視為刪除測試的候選，而非最終裁決。它不會審計上個月建議的 lint 規則、hooks 或 CI jobs。它只看到一個 session，因此無法告訴您某規則現在觸發過於頻繁，或導致該規則的 bug 已消失。您仍必須自行修剪檢查。在良好程式碼上不斷觸發的規則是移除它的訊號。

**它會不會只是發明通用建議來填充其類別？**

這是對它最強的批評。一位使用者發現「一旦工作完成，AI 傾向於忘記 session 中段的困難，並發明通用建議來滿足 retro 類別」。每個候選方案必須來自 session 自身的記錄，因此建議是針對該 session 的具體內容。這既有代價也有好處。它很少發明不相關的東西，但可能會對這個 session 的內容給予過重的權重。丟棄任何您無法追溯到特定時刻的候選方案。也請將嚴重程度順序視為初稿，因為安靜且昂貴的錯誤可能排在吵雜且便宜的錯誤之後。

**我的 session 很長。現在執行，還是重新開始？**

預設情況下，它會回顧目前的 session。這是最佳情況，因為困難仍留在 context window 中。如果 session 已經移出 [smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone)，請 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) 它，並讓新的 `/retro` 指向 session 紀錄中的前一個 session。

**Agent 不斷犯同樣的錯誤。我應該在 `CLAUDE.md` 中新增一行嗎？**

通常不應該，而 `retro` 對這一點的反對比其他任何事都更強烈。`CLAUDE.md` 中的一行會載入每個 session，降低檔案中其他內容的權重，並隨著程式碼變更而過時。如果錯誤是 mechanical 的，修正是會失敗的檢查。如果是判斷事項，則放入審查者讀取的編碼標準中。`AGENTS.md` 和 `CLAUDE.md` 僅用於 navigation pointers 和極少數其他用途。出於同樣原因，`retro` 不是 [memory system](https://www.aihero.dev/ai-coding-dictionary/memory-system)。它不儲存發生了什麼事。它改變環境，使錯誤無法再次發生。

**我的設定提到了 `CODING_STANDARDS.md`，但我沒有它。它從哪裡來？**

沒有任何 skill 會隨附此檔案。當 session 首次為 reviewer 找到需要判斷的規則時，`retro` 會建議建立該檔案。在您接受後，[code-review](https://aihero.dev/skills-code-review) 會讀取它。您已經維護的其他標準檔案，例如 `CONTRIBUTING.md`，運作方式相同。

**它與 `improve-codebase-architecture` 有何不同？**

輸入內容不同。[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 只需要程式碼，並尋找其結構上的改進。`retro` 需要 session 歷史記錄，並改進 agent 工作的環境，而非程式碼。您會同時使用兩者，兩者互不取代。

<a id="its-working-if"></a>

## 如果符合以下情況，即表示運作正常

- 每個候選專案都指向 session 中的特定時刻，而非通用的最佳實務。
- 重複犯的錯誤會轉化為失敗的檢查，而您的 `AGENTS.md` 會隨時間變短，而非變長。
- 如果檢查已經存在但未連線，發現的問題是將其連線起來，而非建議建立新的檢查。
- 在相同型別任務上的下一個 session 能更快找到所需內容。

<a id="where-it-fits"></a>

## 適用位置

`retro` 是主鏈的最後一步，在此您檢視鏈的執行狀況：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

在值得學習的 build 之後執行它，在同一個 session 中，或指向該 session 的記錄。如果 build 順利完成，您可以跳過此步驟。

- [code-review](https://aihero.dev/skills-code-review) 是 `retro` 最常調整的 reviewer agent。新的編碼標準會放在其 Standards 軸線讀取的位置。
- [writing-for-agents](https://aihero.dev/skills-writing-for-agents) 為 `retro` 建議的每個 steering file 和 skill 設定寫作風格，且 `retro` 會在開始前載入它。

[ask-matt](https://aihero.dev/skills-ask-matt) 在您不確定情境需要哪個 skill 時，會跨整個集合進行路由。
