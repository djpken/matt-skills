<a id="what-it-does"></a>

## 功能說明

`codebase-design` 修正了用於設計模組的詞彙：**module**、**interface**、**depth**、**seam**、**adapter**、**leverage**、**locality**。它精確地定義了每一個詞彙，禁止使用模糊的替代詞（如 "component"、"service"、"API"、"boundary"），並闡述了由此衍生出的少數幾項原則。

這是一份參考資料，而非流程。它不執行任何迴圈，不產生任何產出物，也從不會停下來向你提問。其他所有涉及設計的 skill 都會使用它的詞彙。單獨使用時，它只提供詞彙便停止。在呼叫它之前請瞭解這一點。如果你將 [session](https://www.aihero.dev/ai-coding-dictionary/session) 指向一個沒有流程且沒有停止規則的 skill 並說 "go"，agent 就會自行發明一個流程。以下問題展示了這種情況的樣貌。

<a id="when-to-reach-for-it"></a>

## 何時使用

輸入 `/codebase-design`，或者當設計任務符合條件時，agent 會自動使用它。

當你已經知道要重新設計哪些程式碼，且需要思考其結構時，請使用它：seam 放在哪裡、interface 可以有多小、抽取是否移除了呼叫端的複雜度。也使用它來解決關於設計詞彙含義的爭論。

有幾個 skill 與它相近。請根據你面臨的問題來選擇：

| 問題 | Skill |
|---|---|
| 單一模組的結構：其 interface、seam、depth | `codebase-design` |
| *領域的詞彙*："account" 有三種含義，兩個人對 "cancellation" 的理解不同 | [domain-modeling](https://aihero.dev/skills-domain-modeling) |
| 你還不知道*要重新設計哪個*模組 | [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture)（用於尋找候選項的調查） |
| 你希望設計經過辯論，而不僅僅是命名 | [grilling](https://aihero.dev/skills-grilling) |
| 有具體的行為需要建置，且你希望測試在重構後仍能存活 | [tdd](https://aihero.dev/skills-tdd) |

<a id="the-vocabulary"></a>

## 詞彙表

詞彙表即是該 skill。該 skill 將每個術語與其他術語相對照進行定義，並為每個術語提供它所取代的詞彙。

| 術語 | 含義 | 不要說 |
|---|---|---|
| **Module** | 任何具有 interface 和 implementation 的事物。刻意不區分規模：可以是函式、類別、套件，或是跨層次的切片。 | unit, component, service |
| **Interface** | 呼叫端必須知道才能正確使用的一切：型別簽名，加上不變數、順序約束、錯誤模式、必要設定、效能特性。 | API, signature |
| **Depth** | Interface 上的 leverage：呼叫端或測試每學習一個單位 interface 所能執行的行為量。**Deep**：小 interface 背後有大量行為。**Shallow**：interface 的複雜度幾乎與 implementation 相同。 | none |
| **Seam** | Michael Feathers 的術語：一個可以在不編輯該處的情況下改變行為的位置。它是 interface 的*位置*，而將其放在哪裡是一個獨立的決策，與背後放置什麼內容分開。 | boundary |
| **Adapter** | 在 seam 處滿足 interface 的具體事物。它命名的是角色，而非事物種類：記憶體 fake 和 Postgres repo 都是 adapters。 | none |
| **Leverage** | 呼叫端從 depth 獲得的東西：每學習一個單位 interface 獲得更多能力。 | none |
| **Locality** | 維護者從 depth 獲得的東西：變更、bug 和驗證都保持在同一個地方，因此一次修復即可涵蓋所有呼叫端。 | none |

該 skill *不*將 depth 定義為 implementation 行數與 interface 行數的比率，這是 Ousterhout 自己的定義。該指標獎勵填充 implementation，因此該 skill 改用 depth-as-leverage。

<a id="the-four-principles"></a>

## 四項原則

- **Depth 是 interface 的特性，而非 implementation 的特性。** 一個 deep module 內部可以由小的可替換部分建置而成。呼叫端只是看不到它們。一個 module 可以擁有供其自身測試使用的內部 seams，以及在其 interface 上的一個外部 seam。
- **刪除測試。** 想像刪除該 module。如果複雜度消失了，該 module 就是一個透傳（pass-through）。如果複雜度在 N 個呼叫端中重新出現，該 module 就隱藏了真實的複雜度。
- **Interface 是測試表面。** 呼叫端和測試跨越同一個 seam。如果你想測試*超過* interface 的部分，該 module 的形狀就是錯誤的。
- **一個 adapter 意味著假設性的 seam。兩個 adapters 意味著真實的 seam。** 在沒有東西在其上變化之前，不要切開 seam。單一 adapter 的 seam 只是間接性（indirection）。

兩個支援檔案走得更遠，該 skill 會按需讀取它們，而非預先讀取。[DEEPENING.md](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/DEEPENING.md) 將候選項的依賴關係分為四類（in-process、local-substitutable、remote-but-owned、true-external），因為類別決定了你如何跨 seam 測試深化後的 module。[DESIGN-IT-TWICE.md](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/DESIGN-IT-TWICE.md) 啟動並行的 [sub-agents](https://www.aihero.dev/ai-coding-dictionary/subagent) 為同一個 module 產生三個或更多截然不同的 interface，然後根據 depth、locality 和 seam 放置位置進行比較。

<a id="common-questions"></a>

## 常見問題

**我實際上如何在 TypeScript 中建置一個 deep module？**

這是關於該 skill 最常被問到的問題，而該 skill 並不回答它。它定義了 deep module *是什麼*；它沒有說明如何阻止雜散的 import 到達 interface 之外。[Issue #458](https://github.com/mattpocock/skills/issues/458) 直言不諱地說："假設我們對 interface 感到滿意，它隱藏了細節等等。但我們如何強制執行它？我認為沒有 linting 或明確的護欄，人類和 LLM 都會隨著時間推移開始讓它變得混亂。" 該討論串中的答案提供了三個選項：將其包裝在類別或 IIFE 中，並接受類別變得巨大；將其作為 monorepo 中的套件，並接受 monorepo 工具鏈；或使用像 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 這樣的 linter 來禁止繞過 interface 的 imports。在這些機制中，Effect 是最好的，dependency-cruiser 是第二好的。儲存庫的 `in-progress/` 桶中有一個 `setup-ts-deep-modules` skill，它制定了 `src/packages/<name>/index.ts` 慣例，但它是一個 beta 頻道的 skill，沒有檔案頁面，且不提供 lint 規則。

**我將一個 session 指向它，它燒掉了 100k [tokens](https://www.aihero.dev/ai-coding-dictionary/token) 重新設計我從未要求過的事情。**

這是一個已知問題，已提交為 [issue #449](https://github.com/mattpocock/skills/issues/449)。該 skill 由模型呼叫，並自我描述為詞彙，但其中沒有任何內容能硬性阻止 agent 將其視為可執行的流程。當被指示「在 /codebase-design 中恢復並推動未決定的事項」時，agent 選擇了該 skill 中最接近流程的部分：`DESIGN-IT-TWICE.md` 中的平行 sub-agents。它重新探索了先前 session 已經對映的程式碼，並在詢問任何問題之前工作了很長時間。這個 skill 沒有 driver skill 所具備的任何護欄（檢查點、一次只問一個問題、不自動推進），因為它是一個參考。變通方法是呼叫一個 driver skill（`/grill-with-docs`、`/improve-codebase-architecture` 或 `/tdd`），並使用 `codebase-design` 作為其詞彙。該 issue 目前仍為開啟狀態。

**`design-an-interface` 去哪了？是否有 `/interface-design` skill？**

這個 skill 取代了 `design-an-interface` 並接管了其內容。沒有遺失任何東西。它的「設計兩次」技術（平行 sub-agents 產生截然不同的設計，源自 Ousterhout）在此以 `DESIGN-IT-TWICE.md` 的形式提供。另外，許多人要求一個專門針對深模組/薄介面哲學的 `/interface-design` skill；這個 skill 已經涵蓋了該哲學，且沒有計劃建立獨立的 skill。如果你是在尋找這兩個名稱中的任何一個，這就是該頁面。

**這不是一種檔案結構慣例，例如資料夾、barrel files、feature slices 嗎？**

不是。人們多次對此提出異議，但該 skill 並未改變。[Issue #95](https://github.com/mattpocock/skills/issues/95) 提出將正式的 fractal-tree 檔案結構作為深模組的具體實現；回覆指出兩者相互獨立：「深模組關於介面的設計以及透過嚴格介面進行存取，無論檔案系統看起來如何。採用這種方法擁有淺模組是完全可能的。」在 #458 中也出現了相同的觀點：「我認為你可能將模組的概念與檔案系統繫結得太緊了。檔案系統當然可以作為模組形狀的有用提示，但在建構深模組時，沒有必要使用檔案系統。」詞彙表刻意將 **module** 定義為與規模無關。

**`tdd` 實際上有使用這個詞彙嗎？**

現在有，但有很長一段時間沒有。v1.0 從 `tdd` 中移除了內嵌的深模組註解，改以這個共用 skill 取代，但沒有人新增指標來取代它們，因此 `tdd` 自行定義了「seam」且未參考任何內容。指標現在位於 `tdd` 中，當未決定的問題是介面的形狀而非測試時，agent 會遵循它。`tdd` 仍然擁有「seam」作為你*測試*的邊界；這個 skill 擁有其背後的模組形狀。

**design-it-twice 模式在 Claude Code 之外有效嗎？**

不完全是。`DESIGN-IT-TWICE.md` 說「使用 Agent 工具平行生成 3 個以上的 sub-agents」，而「Agent」是 Claude Code [tool](https://www.aihero.dev/ai-coding-dictionary/tool) 的名稱。該儲存庫為其他 [harnesses](https://www.aihero.dev/ai-coding-dictionary/harness) 提供了元資料，包括 Codex，而這些可能沒有該名稱的工具。因此，平行設計階段的可攜性不如該 skill 的元資料所暗示的那樣。[Issue #564](https://github.com/mattpocock/skills/issues/564) 追蹤此問題，且目前仍為開啟狀態。

**我可以將自己的概念新增到詞彙表中，例如 connascence、module secrets、[progressive disclosure](https://www.aihero.dev/ai-coding-dictionary/progressive-disclosure) 嗎？**

人們曾提出這些建議。[Issue #180](https://github.com/mattpocock/skills/issues/180) 新增了 Parnas 的 module secrets 和 Page-Jones 的 connascence，作為*什麼*內容洩漏過 seam 的命名層，並附帶可用的 diff；[issue #303](https://github.com/mattpocock/skills/issues/303) 提出在實現內部進行 progressive disclosure，使得在公開介面上是深模組的模組，其內部也具有結構。兩者均為開啟狀態且未合併。發布的詞彙表刻意保持簡短，且該 skill 本身給出了原因：一致的語言是重點，而一個沒有人一致使用的術語比沒有術語更糟。

<a id="its-working-if"></a>

## 如果出現以下情況，則表示它正在運作

- 設計對話不再產生「component」、「service」和「boundary」這些詞，而是開始產生「module」、「interface」和「seam」。
- 有人可以指向一個建議的提取，並說明它是否透過刪除測試，而不需要含糊其辭。
- 建議的 seam 會附上第二個介面卡的名稱，而不僅僅是第一個。
- 介面的討論涵蓋不變數、順序和錯誤模式，而不僅僅是型別簽名。
- 呼叫它不會啟動 session。如果 agent 僅從 `/codebase-design` 開始讀取檔案並提出重構，它已經將參考誤認為 driver。

<a id="where-it-fits"></a>

## 它適用的位置

`codebase-design` 是一個**隨時可取的獨立專案**，是工程 skills 下方的詞彙層，而非任何鏈條中的一個步驟。它最接近的鄰居是 [domain-modeling](https://aihero.dev/skills-domain-modeling)，這是*問題領域*詞彙的平行參考，而非模組的形狀。兩者通常一起使用，因為良好地命名深模組需要兩者。[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 是另一個：它調查程式碼庫以尋找深化候選者，並用這個詞彙表撰寫每一個，因此它找到模組，而你用這個 skill 的詞彙來設計它。當你不確定哪個 skill 或流程適合時，[ask-matt](https://aihero.dev/skills-ask-matt) 會為你導航。
