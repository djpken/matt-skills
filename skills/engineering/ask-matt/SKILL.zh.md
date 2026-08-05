---
name: ask-matt
description: 詢問哪個 skill 或流程適合你的情況。本 repo 中所有使用者呼叫 skill 的路由器。
disable-model-invocation: true
---

# Ask Matt

你不會記住所有 skill，所以就問吧。

**流程（flow）** 是穿越各 skill 的路徑。大多數路徑沿著一條**主流程**前進，兩條**入口道**匯入其中。其他的都是獨立 skill，或是在底下運作的詞彙層。

## 主流程：想法 → 出貨

大多數工作走的路線。你有一個想法，想把它做出來。

1. **`/grill-with-docs`** — 透過訪談磨練想法。只要你**在一個工作目錄裡**，就從這裡開始：它有狀態，會把學到的東西保留在 `CONTEXT.md` 和 ADR 中。（沒有工作目錄？用 `/grill-me`——見獨立項目。兩者跑的是同一個 `/grilling` 原語；`grill-with-docs` 會留下書面紀錄，所以只要有 repo 可以留，它就是比較好的那一個。）
2. **分支——你能在對話中解決所有問題嗎？** 如果某個問題需要可執行的答案（狀態、商業邏輯、必須親眼看到的 UI），透過原型繞道，用 **`/handoff`** 作為雙向橋樑（原型活在自己的目錄裡，這正是 `/handoff` 的用途——見 Phase boundaries）：
   - **`/handoff`** 出去，再針對那個檔案開一個新 session，
   - **`/prototype`** 用可拋棄的程式碼回答問題，
   - **`/handoff`** 帶著你學到的東西回來，並從原始想法 thread 中引用它。
3. **分支——這是多 session 的建置嗎？**
   - **是** → **`/to-spec`**（把這段對話轉成 spec），再用 **`/to-tickets`** 拆成 tracer-bullet tickets，每張都宣告自己的 **blocking edges**。本地 tracker 上是 `.scratch/<feature>/issues/` 底下每張 ticket 一個檔案，依 blockers 優先順序手動處理；真正的 tracker 上這些 edges 會變成 native blocking links，任何 blockers 都完成的 ticket 就能被領走——每張 ticket 都啟動 **`/implement`**，**每張之間清除 context**。每張 ticket 都是自成一體的，所以上一張的 context 是可拋棄的。
   - **否** → 直接在這裡執行 **`/implement`**，在同一個 context 視窗中。

   無論哪種，**`/implement`** 都在內部驅動 **`/tdd`** 建置每張 issue——一次一個 red-green slice——再執行 **`/code-review`**（Standards + Spec 兩軸審查這份 diff）才 commit。想在沒有完整 spec 的情況下單獨 test-first 建置具體行為，用 **`/tdd`**；想針對固定基準點單獨 review branch 或 PR，用 **`/code-review`**。

### Context 衛生

把步驟 1–3 保持在**一個不中斷的 context 視窗**中——在 `/to-tickets` 完成之前不要 compact 或 clear——讓 grilling、spec 和 tickets 都建立在同樣的思考基礎上。每個 `/implement` 再從頭開始，從 ticket 出發。

這個限制是 **[smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone)**：視窗（頂尖模型約 150k tokens）內模型仍能清晰推理的範圍。如果 session 在 `/to-tickets` 之前接近上限，不要在退化狀態下硬撐——在最近的 phase boundary `/compact`，然後繼續（見 Phase boundaries）。

## 入口道

產生工作後匯入主流程的起始情況。

- **Bugs 和 requests 堆積** → **`/triage`**。它讓 issues 通過 triage roles 並產出 agent-ready issues，後續再由 **`/implement`** 接手。

  Triage 只用於**你沒有建立的** issues：bug reports、incoming feature requests，任何 raw arrival。`/to-tickets` 產出的 tickets 已是 agent-ready，**不要對它們做 triage**。

- **有東西壞了** → **`/diagnosing-bugs`**。給難搞的那種用：第一眼看不出所以然的 bug、間歇性的 flake、在兩個已知良好狀態之間悄悄爬進來的 regression。它拒絕在有一個**緊密回饋迴圈**——一個已經對*這個* bug 亮紅燈的指令——之前先理論化，然後用一個 regression test 修復。它的事後檢討會在真正的發現是「沒有好的接縫可以鎖住這個 bug」時，交接給 **`/improve-codebase-architecture`**。

- **一個龐大、模糊的工作——greenfield 專案或巨大 feature build，大到一個 session 裝不下** → **`/wayfinder`**，這裡認知負荷最重的流程。當這裡到目的地的路還看不見時，它會在 issue tracker 上繪製一張**共享地圖（shared map）**，由**決策 tickets（decision tickets）**組成，逐一解決——產出**決策，而非交付物（decisions, not deliverables）**——直到迷霧散去、路徑清楚。**`/grill-with-docs`** 磨練的是一個 session 就能裝下的想法；wayfinder 則是給裝不下的那種——而且更慢、更密集，只留給真正需要它的情況，絕不用在範圍明確的 feature 上。

  地圖清楚之後，**它是交接，不是建置**：匯回主流程進入 **`/to-spec`**，把地圖上互相關聯的決策收斂成可建置的計畫，接著照常跑 `/to-tickets` 和 `/implement`。如果直接把地圖丟進 `/implement` 迴圈，會跳過那次收斂、丟掉關聯細節——只有在這件事其實真的不大時，才直接進 `/implement`。

## Codebase health

不是 feature work，而是 upkeep。

- **`/improve-codebase-architecture`** — 有空時隨時執行，讓 codebase 保持適合 agents 操作。它會浮出 **deepening opportunities**；選一個就會*產生一個 idea*，可帶進 `/grill-with-docs` 進入主流程。它是找出 candidates 的 survey；**`/codebase-design`**（見下方）則是設計所選項目的 bench。

## Vocabulary underneath

兩個 model-invoked references 會在其他 skills **底下**運作，各自是自身 vocabulary 的 single source of truth。當問題是**詞語**而不是流程時，可以直接取用它們；也可以讓上面的 skills 自行拉進來。

- **`/domain-modeling`** — 磨利專案的 *domain* language：挑戰模糊 term、解決 overloaded word（例如 "account" 同時做三件事）、把難以逆轉的決策記錄成 ADR。這是 `/grill-with-docs` 用來保持 `CONTEXT.md` 乾淨 glossary 的 active discipline。
- **`/codebase-design`** — 設計 module *shape* 的 deep-module vocabulary（module、interface、depth、seam、adapter、leverage、locality）：用小 interface 承載大量 behaviour，放在乾淨 seam 上。`/tdd` 和 `/improve-codebase-architecture` 都使用這套語言。

## Phase boundaries

**階段（phase）**是一個 session 裡的一塊工作——grilling、implementation、QA。在兩個階段的**邊界**上你有五個選項，而在它們之間做選擇是這整張地圖裡最模糊的決策：

- **Continue** — 留在原地。不花代價，也不損失任何東西。
- **`/clear`** — 清空視窗，當這裡的一切都與接下來無關時用。
- **`/handoff`** — 寫一個可攜的 Markdown 檔案。範圍很窄：只用於**換一個 harness**、**換一個目錄**、**交給同事**，或在**階段中途**分支出一個附帶任務。它買到的是可攜性。
- **Subagent** — 把一個範圍明確的任務送進它自己的視窗，拿回一份報告。
- **`/compact`** — 壓縮這段 context，用它播種一個新 session。**預設選項**，排在樹的最底層，而不是第一個該伸手拿的。

讀 [PHASE-BOUNDARIES.md](PHASE-BOUNDARIES.md) 了解完整的決策樹——五個問題、每個分支背後的理由，以及為什麼 primary-source 的代價讓 **Continue** 成為第一個該排除的選項。在**邊界**上做這個決定；階段中途，就繼續，或把剩下的工作拆給 subagents。

## 獨立項目

完全脫離主流程。

- **`/grill-me`** — 與 `/grill-with-docs` 相同的無情訪談，但是**無狀態**的：不在本地保存任何東西，不建立 `CONTEXT.md`。當你**不在工作目錄裡**時用它——磨練一個計畫、一個設計、一段文字，任何底下沒有 repo 的東西。如果你在工作目錄裡，改用 `/grill-with-docs`：它跑同一場訪談還會留下書面紀錄，所以絕對是比較好的選擇。
- **`/grilling`** — 訪談本身的原語：rounds、frontier，事實是 agent 的工作，決策是你的。`/grill-me` 和 `/grill-with-docs` 是進入它的兩個命名入口，`/triage`、`/wayfinder` 和 `/improve-codebase-architecture` 內部都在跑它。只有在你想要不帶任何包裝的訪談時，才直接用它。
- **`/resolving-merge-conflicts`** — 逐個 hunk 處理進行中的 merge 或 rebase 衝突，依**意圖**（追溯到雙方的 primary source）而非挑行來解決，然後完成這次操作。它絕不執行 `--abort`。完全獨立，不在任何流程上：當你已經卡在衝突中時才用它。
- **`/prototype`** — 一個小型、可拋棄的程式，用來回答一個設計問題：這個 state model 感覺對嗎，或這個 UI 應該長什麼樣子。Throwaway 限制的是程式碼怎麼寫，不是承諾要銷毀它：答案會收進正式程式碼，原型本身則作為 **primary source** 保留在 main 分出的 `prototype/<name>` 分支上，並從 implementation issue 指向它。它是主流程步驟 2 的 detour，但任何難以在紙上決定的設計問題都可以用。
- **`/research`** — 將閱讀 legwork 委派給**背景 agent**：它用 **primary sources** 調查問題，然後在 repo 留下一份有引用的 Markdown 檔。它閱讀時你可以繼續工作。它產生的檔案是要帶**進**主流程 `/grill-with-docs` 的材料——research 餵養思考，不取代思考。
- **`/to-questionnaire`** — 當卡住你的東西不在你腦中或 codebase 裡，而是在**別人腦中**時，這會寫一份問卷讓對方填。它是 `/grill-me` 的反面：不是就主題訪談你，而是就**發送**這件事訪談你——要寄給誰、你需要拿回什麼——並把問題對準那個缺口。拿回來的東西是 `/grill-with-docs` 或 `/to-spec` 的材料。
- **`/wizard`** — 給只有**人類**才能做的步驟：佈建基礎設施、設定憑證或 CI secrets、點過一個陌生的第三方 dashboard、跑一次性的 migration 或 cutover。它會產生一支互動式 bash script，開啟每個 URL、擷取每個值，並寫進 `.env` 和 GitHub secrets——讓這個流程不再是每次都要向 agent 重新解釋一遍的東西。Model-invoked，所以 agent 一撞到只有人類能跨過的牆就會伸手拿它。如果 agent 自己就能做，它就該自己做；這是給人類真的在迴路中的情況用的。
- **`/wait-what`** — 給沒說到位的訊息用的修正。在對話中途、在任何其他 skill 裡面用它，agent 會用你缺少的 context 重新表達剛才說的話，講白話、用 `CONTEXT.md` 的詞彙。它是事後補救；`/grill-with-docs` 是事前的解方，因為早早談定的共同語言，才是讓術語一開始就不會冒出來的原因。
- **`/teach`** — 在多個 sessions 中學習一個概念，以目前目錄作為有狀態的工作區。
- **`/writing-for-agents`** — 寫給 agent 消費的文件的參考：skill、AGENTS.md、被指向的文件。

## 前提條件

**`/setup-matt-pocock-skills`** — 在第一次使用 engineering flow 前執行，設定其他 skills 所需的 issue tracker、triage labels 和 doc layout。Custom issue trackers 也可使用。
