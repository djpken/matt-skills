<a id="what-it-does"></a>

## 功能說明

`tdd` 採用測試優先的方式來建置功能或修復錯誤：先寫一個失敗的測試，然後撰寫剛好足夠讓它透過的程式碼，接著處理下一個行為。它承載了讓這個迴圈能產出值得保留的測試所需的標準：什麼樣的測試才算好、測試應該放在哪裡、mock 的用途為何，以及讓測試套件變得毫無價值的三種反模式。

它不會在你尚未事先同意的接縫處撰寫任何測試。在任何測試存在之前，它會列出打算進行測試的公開邊界，並暫停等待你的確認。測試的精力是有限的，這個步驟會將精力花在關鍵路徑上，而不是每個邊緣案例。`tdd` 也是一份**參考**，而非驅動器。它包含迴圈的規則，而由其他事物（你，或 [implement](https://aihero.dev/skills-implement)) 來執行應用這些規則的 [session](https://www.aihero.dev/ai-coding-dictionary/session)。

<a id="when-to-reach-for-it"></a>

## 何時使用

輸入 `/tdd`，或者當任務符合條件時，[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 會自動使用它：以測試優先的方式建置功能或修復錯誤，或者當你說出「紅綠重構」時。

當有具體的行為需要建置，具有輸入和可觀察的輸出，且你希望測試能在重構後依然有效時，請使用它。

| 你的情況 | 該去哪裡 |
| --- | --- |
| 具有定義明確輸入和輸出的行為（商業邏輯、請求/回應合約、轉換、驗證） | `tdd` |
| 行為尚未確定 | [to-spec](https://aihero.dev/skills-to-spec)，它也會在撰寫任何程式碼之前同意測試接縫 |
| 問題其實在於介面的形狀，而非測試 | [codebase-design](https://aihero.dev/skills-codebase-design) |
| 你有一份 [spec](https://www.aihero.dev/ai-coding-dictionary/spec) 或 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)，希望整個建置流程由它代為執行 | [implement](https://aihero.dev/skills-implement)，它會針對每個 ticket 驅動 `tdd` |
| 設定、接線、膠合程式碼、型別註解、直接的 CRUD 委派 | 這裡沒有適合的選項；請參閱下方的未解決缺口 |

最後一行是一個真實的缺口。這個 skill 決定接縫的*位置*，但其中沒有任何內容決定一個變更是否值得進行這個迴圈。如果你對一個沒有獨立真相來源可供斷言的變更執行它，你會得到一個重述實作的測試。這就是該 skill 所警告的同義反覆反模式，只是從另一個方向達成的。這是 [issue #746](https://github.com/mattpocock/skills/issues/746)，目前仍未解決。在該問題關閉之前，你需要自行做出判斷，或者將規則寫入你的 `CLAUDE.md`。

<a id="prerequisites"></a>

## 先決條件

需要安裝 [codebase-design](https://aihero.dev/skills-codebase-design)。`tdd` 過去包含自己的深層模組和介面設計筆記；v1.0 刪除了這些內容，改採共用 skill，現在 `tdd` 使用其介面設計詞彙。沒有其他要求；該 skill 是 [stateless](https://www.aihero.dev/ai-coding-dictionary/stateless)，且不會自行撰寫任何檔案。

<a id="the-loop-and-the-seam-it-runs-at"></a>

## 迴圈，以及它運作的接縫

該 skill 建立在三個術語之上。

**紅綠。** 撰寫失敗的測試，然後只寫足夠讓它透過的程式碼。不要為下一個測試撰寫程式碼。沒有重構階段。該 skill 在 2026 年 6 月移除了它，因為 agent 幾乎從不執行此步驟，且審查與實作作為獨立的 session 運作得更好。重構屬於 [code-review](https://aihero.dev/skills-code-review)。

**垂直切片。** 在一個接縫處撰寫一個測試，然後是最小實作，接著重複。第一個迴圈是一個**曳光彈**，證明單一路徑從頭到尾可行。相反的是水平切片：先寫所有測試，再寫所有程式碼。批次撰寫的測試驗證的是*想像中*的行為。它們檢查事物的形狀，而非使用者實際執行的操作，並且在你理解實作之前，就讓你承諾了一套測試結構。

**事先同意的接縫。** 接縫是你觀察行為的公開邊界，無需深入內部。這條規則沒有例外。不會在未經確認的接縫處進行測試。在完整鏈路中，接縫會更早地在 [to-spec](https://aihero.dev/skills-to-spec) 期間達成協議：「`/tdd` 被指示僅在事先同意的測試接縫處工作，`/code-review` 會檢查是否僅使用了已同意的測試接縫。」單獨呼叫時，`tdd` 會直接詢問你。

它旨在防止的三種反模式：

| 反模式 | 特徵 |
| --- | --- |
| 與實作耦合 | 當你重新命名內部函式時測試就會失敗，儘管行為並未改變。Mock 了內部協作者、斷言呼叫次數、使用資料庫查詢來驗證而非介面。 |
| 同義反覆 | 預期值是透過程式碼計算的方式計算出來的，因此測試天生就會透過。預期值必須來自其他地方：已知的正確字面量、演算範例、規格說明。 |
| 水平切片 | 在有任何實作之前，一批測試就已經落地。 |

Mocks 僅用於系統邊界：外部 API、時間、隨機性，有時是檔案系統或資料庫。永遠不要 mock 你自己的模組。

<a id="common-questions"></a>

## 常見問題

**為什麼它不重構？描述中寫著「紅綠重構」。**

因為重構步驟被移除了，但描述沒有。移除是有意為之的。Agent 幾乎從不執行該步驟，且將實作與審查保持在獨立的 session 中運作得更好。結果是否仍算符合教科書的 TDD，不如這個迴圈是否產出更好的程式碼重要。觸發短語與主體之間的差異已記錄為 [issue #589](https://github.com/mattpocock/skills/issues/589)，目前仍未解決，因此「紅綠重構」繼續作為觸發該 skill 的短語運作。你得到的是紅綠，重構則在 [code-review](https://aihero.dev/skills-code-review) 中進行。

**它要求我選擇一個測試接縫，但我完全不知道該選哪個。**

這是該 skill 最常被報告的摩擦點（[issue #607](https://github.com/mattpocock/skills/issues/607)）。提示僅列出候選接縫的名稱，沒有說明每個接縫能捕捉或遺漏什麼，因此你是在標籤之間做選擇。目前還沒有發布修復方案。實際的變通方法是，在回答之前要求 agent 說明權衡：元件層級的接縫會遺漏什麼而整合層級的接縫能捕捉到，以及它會慢多少。這也是為什麼鏈路會在 `to-spec` 中事先達成接縫協議，因為在那裡你可以看到整個功能，而不只是一個提示。

**它先寫了實作，再寫測試，儘管 skill 說要先紅。**

這種情況確實會發生。有一位使用者將 [model](https://www.aihero.dev/ai-coding-dictionary/model) 套用在上面，得到了一個異常誠實的回答：「我知道該 skill 說『一次只寫一個測試，觀察它因正確的原因而失敗』。我讀過了。但我只是預設地沿用了我的正常習慣。」該 skill 接受這種情況。沒有任何指示能讓 agent 百分之百地遵守，而更嚴格的措辭只會限制 agent 的創造力，卻收效甚微。即使 agent 沒有嚴格遵循，這個迴圈仍然值得執行，因為整體結果仍然更好。如果對某個特定部分而言，嚴格遵循至關重要，請觀察執行過程，而不是依賴 skill 來強制執行。

**應該先撰寫瀏覽器測試或端到端測試嗎？**

通常不需要，而且該 skill 也不會阻止它。一位使用者回報 agent 先撰寫了一個 Playwright 測試，然後花了一段很長的迴圈重新執行它，並結論認為 *測試* 壞掉了，而該功能根本還不存在。瀏覽器測試足夠慢，以至於紅綠回饋迴路無法收回成本。請在您的儲存庫的 `CLAUDE.md` 中說明，瀏覽器測試應在行為運作正常之後再進行。

**`/tdd` 是否取代了 `/implement`，或該課程的 `/do-work`？**

沒有。`/tdd` 記錄了方法論；`/implement` 是一個簡單的工作→回饋→commit 迴路，是 `/do-work` 的直接替代方案。該課程的單一 `/do-work` 步驟現在已拆分到 `/implement`、`/tdd` 和 `/code-review` 中。如果您在問應該對某個 ticket 執行哪一個，答案幾乎總是 `/implement`。

**deep-modules 和 interface-design 的指引去哪了？**

在 v1.0 中移入了 [codebase-design](https://aihero.dev/skills-codebase-design)，並加以泛化，讓多個 skill 共用同一套詞彙。`refactoring.md` 也同時被移除；重構現在是 [code-review](https://aihero.dev/skills-code-review) 的工作，而該 skill 承載了 Fowler 的 code smell 基準。

**它知道我的其他 ticket 嗎？**

不知道。針對單一 ticket 執行時，它可能會提出屬於相鄰 ticket 的工作，因為它無法看到 issue 圖譜的其餘部分（[issue #129](https://github.com/mattpocock/skills/issues/129)）。這不是 `tdd` 的工作。在提供 ticket 的同時附上規格說明會有幫助；但一開始就將 ticket 調整到適當大小會更有幫助。

<a id="its-working-if"></a>

## 如果出現以下情況，表示它運作正常

- 在任何測試檔案存在之前，它會停止並指出它打算測試的接縫（seams），然後等待。
- 出現一個測試，變紅，加入剛好足夠讓它透過的程式碼，然後才出現下一個測試，而不是一批測試後接一批程式碼。
- 測試名稱讀起來像是功能（「使用者可以使用有效購物車結帳」），而不是內部實作（「checkout 呼叫 paymentService.process」）。
- 斷言中的預期值是您可以追溯到規格說明的常數，而不是以程式碼計算方式重新計算的值。
- 重新命名內部函式不會導致測試套件中的任何專案失敗。
- Mocks 只出現在外部邊界（例如付款 API、時鐘），絕不出現在您自己的模組周圍。

<a id="where-it-fits"></a>

## 適用範圍

`tdd` 在主鏈的建置步驟中執行；它本身不是一個獨立的步驟：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

[to-spec](https://aihero.dev/skills-to-spec) 事先同意測試接縫，[implement](https://aihero.dev/skills-implement) 針對每個 ticket 驅動 `tdd`，而 [code-review](https://aihero.dev/skills-code-review) 事後檢查是否只使用了商定的接縫，並負責 `tdd` 不再處理的重構工作。它的另一個鄰居是 [codebase-design](https://aihero.dev/skills-codebase-design)，這是 `tdd` 所使用的接縫和 deep-module 詞彙的共用來源。您也可以單獨使用它，只要有一個具體的行為需要建置，且沒有完整的規格說明即可。當您不確定哪個 skill 適合您的情況時，[ask-matt](https://aihero.dev/skills-ask-matt) 會為您導向。
