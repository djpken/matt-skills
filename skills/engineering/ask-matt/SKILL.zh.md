---
name: ask-matt
description: 詢問哪個 skill 或流程適合你的情況。本 repo 中所有使用者呼叫 skill 的路由器。
disable-model-invocation: true
---

# Ask Matt

你不會記住所有 skill，所以就問吧。

**流程（flow）** 是穿越各 skill 的路徑。大多數路徑沿著一條**主流程**前進，兩條**入口道**匯入其中。其他的都是獨立 skill。

## 主流程：想法 → 出貨

大多數工作走的路線。你有一個想法，想把它做出來。

1. **`/grill-with-docs`** — 透過訪談磨練想法。**有程式碼庫**時從這裡開始：它有狀態，會將學到的東西保留在 `CONTEXT.md` 和 ADR 中。（沒有程式碼庫？用 `/grill-me`——見獨立項目。）
2. **分支——你能在對話中解決所有問題嗎？** 如果某個問題需要可執行的答案（狀態、商業邏輯、必須親眼看到的 UI），透過原型繞道，用 **`/handoff`** 作為雙向橋樑（見跨 session）：
   - **`/handoff`** 出去，再針對那個檔案開一個新 session，
   - **`/prototype`** 用可拋棄的程式碼回答問題，
   - **`/handoff`** 帶著你學到的東西回來，並從原始想法 thread 中引用它。
3. **分支——這是多 session 的建置嗎？**
   - **是** → **`/to-spec`**（把這段對話轉成 spec），再用 **`/to-tickets`** 拆成 tracer-bullet tickets，每張都宣告自己的 **blocking edges**。本地 tracker 上是照順序手動處理的 `tickets.md`；真正的 tracker 上這些 edges 會變成 native blocking links，任何 blockers 都完成的 ticket 就能被領走——每張 ticket 都啟動 **`/implement`**，**每張之間清除 context**。
   - **否** → 直接在這裡執行 **`/implement`**，在同一個 context 視窗中。

   無論哪種，**`/implement`** 都在內部驅動 **`/tdd`** 建置每張 ticket——一次一個 red-green slice——再執行 **`/code-review`**（Standards + Spec 兩軸審查這份 diff）才 commit。想在沒有完整 spec 的情況下單獨 test-first 建置具體行為，用 **`/tdd`**；想針對固定基準點單獨 review branch 或 PR，用 **`/code-review`**。

### Context 衛生

把步驟 1–3 保持在**一個不中斷的 context 視窗**中——在 `/to-tickets` 完成之前不要 compact 或 clear——讓 grilling、spec 和 tickets 都建立在同樣的思考基礎上。每個 `/implement` 再從新 session 開始，從 ticket 出發。

這個限制是 **[smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone)**：視窗（頂尖模型約 120k tokens）內模型仍能清晰推理的範圍。如果 session 在 `/to-tickets` 之前接近上限，不要在退化狀態下硬撐；用 `/handoff` 後在新 thread 繼續。

## 入口道

產生工作後匯入主流程的起始情況。

- **Bugs 和 requests 堆積** → **`/triage`**。它讓 issues 通過 triage roles 並產出 agent-ready issues，後續再由 **`/implement`** 接手。

  Triage 只用於**你沒有建立的** issues：bug reports、incoming feature requests，任何 raw arrival。`/to-tickets` 產出的 tickets 已是 agent-ready，**不要對它們做 triage**。

- **一個龐大、模糊的工作——greenfield 專案或巨大 feature build，大到一個 session 裝不下** → **`/wayfinder`**。當這裡到目的地的路還看不見時，它會在 issue tracker 上繪製一張**共享地圖（shared map）**，由調查 tickets 組成，逐一解決——產出**決策，而非交付物（decisions, not deliverables）**——直到迷霧散去、路徑清楚。之後匯回主流程進入 **`/to-spec`**（或者如果這件事其實不大，直接進 **`/implement`**）。**`/grill-with-docs`** 磨練的是一個 session 就能裝下的想法；wayfinder 則是給裝不下的那種。

## Codebase health

不是 feature work，而是 upkeep。

- **`/improve-codebase-architecture`** — 有空時隨時執行，讓 codebase 保持適合 agents 操作。它會浮出 **deepening opportunities**；選一個就會*產生一個 idea*，可帶進 `/grill-with-docs` 進入主流程。它是找出 candidates 的 survey；**`/codebase-design`**（見下方）則是設計所選項目的 bench。

## Vocabulary underneath

兩個 model-invoked references 會在其他 skills **底下**運作，各自是自身 vocabulary 的 single source of truth。當問題是**詞語**而不是流程時，可以直接取用它們；也可以讓上面的 skills 自行拉進來。

- **`/domain-modeling`** — 磨利專案的 *domain* language：挑戰模糊 term、解決 overloaded word（例如 "account" 同時做三件事）、把難以逆轉的決策記錄成 ADR。這是 `/grill-with-docs` 用來保持 `CONTEXT.md` 乾淨 glossary 的 active discipline。
- **`/codebase-design`** — 設計 module *shape* 的 deep-module vocabulary（module、interface、depth、seam、adapter、leverage、locality）：用小 interface 承載大量 behaviour，放在乾淨 seam 上。`/tdd` 和 `/improve-codebase-architecture` 都使用這套語言。

## 跨 session

- **`/handoff`** — 當 thread 已滿或你需要分支（例如進入 `/prototype` session），這會把對話壓縮成一個 Markdown 檔案。你不是在原地繼續，而是**開啟新 session 並引用那個檔案**來跨越 context 視窗。它是 context 視窗之間的橋樑，雙向皆可。當你想要**新 session**但需要**保留目前對話**時使用。
- **`/compact`**（內建）— 停留在**同一個對話**中，讓較早 turns 被摘要。在**階段之間的刻意休息點**使用，當你不介意失去逐字歷史時。不要在階段中途 compact，agent 可能會迷失方向。`/handoff` 是 fork；`/compact` 是 continue。

## 獨立項目

完全脫離主流程。

- **`/grill-me`** — 與 `/grill-with-docs` 相同的無情訪談，但用於**沒有 codebase**時。無狀態：不在本地保存任何東西，不建立 `CONTEXT.md`。用它來磨練任何不在 repo 中的計畫或設計。
- **`/prototype`** — 一個小型、可拋棄的程式，用來回答一個設計問題：這個 state model 感覺對嗎，或這個 UI 應該長什麼樣子。從第一天起就是 throwaway；保留答案，刪掉程式碼。它是主流程步驟 2 的 detour，但任何難以在紙上決定的設計問題都可以用。
- **`/research`** — 將閱讀 legwork 委派給**背景 agent**：它用 **primary sources** 調查問題，然後在 repo 留下一份有引用的 Markdown 檔。它閱讀時你可以繼續工作。它產生的檔案可帶進 `/grill-with-docs` 的主流程；research 餵養思考，但不取代思考。
- **`/teach`** — 在多個 sessions 中學習一個概念，以目前目錄作為有狀態的工作區。
- **`/writing-great-skills`** — 寫好與編輯 skill 的參考。

## 前提條件

**`/setup-matt-pocock-skills`** — 在第一次使用 engineering flow 前執行，設定其他 skills 所需的 issue tracker、triage labels 和 doc layout。Custom issue trackers 也可使用。
