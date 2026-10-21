<a id="what-it-does"></a>

## 功能說明

`implement` 負責執行已經決定的工作。你將它指向 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket)、[spec](https://www.aihero.dev/ai-coding-dictionary/spec)，或對話中剛達成共識的計畫，它就會撰寫程式碼、在接縫處驅動 [tdd](https://aihero.dev/skills-tdd)、邊做邊進行型別檢查、最後執行 [code-review](https://aihero.dev/skills-code-review)，並提交到目前的分支。

它絕不會重新開啟計畫。沒有訪談、沒有澄清環節、也不會提出不同的方法。上游已確定的內容就是輸入，而這個 skill 的全部職責就是將其轉化為 commit。這正是它與對全新的 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 輸入「建立這個」的區別，後者往往會在建立過程中重新設計工作。

<a id="when-to-reach-for-it"></a>

## 何時使用

你需要自己輸入 `/implement` 來呼叫它，agent 不會自行使用。它隨 `disable-model-invocation: true` 一起發布，因此其他 skill 也無法呼叫它。當 [ask-matt](https://aihero.dev/skills-ask-matt) 或 [to-tickets](https://aihero.dev/skills-to-tickets) 提到「然後對每個 ticket 執行 `/implement`」時，那是給你的指示，而不是 agent 會主動執行的動作。

工作目前所在的位置決定了這是否是正確的 skill：

| 工作是… | 使用 |
| --- | --- |
| 追蹤器上的一個 ticket | `/implement #42`，每個 [session](https://www.aihero.dev/ai-coding-dictionary/session) 處理一個 ticket，[clearing](https://www.aihero.dev/ai-coding-dictionary/clearing) 不同 ticket 之間的 context |
| 一個 spec，尚未拆分，且建立工作跨越多個 session | 先使用 [to-tickets](https://aihero.dev/skills-to-tickets)，然後對每個 ticket 使用 `/implement` |
| 一個 spec，且建立工作規模較小 | 直接對 spec 使用 `/implement` |
| 僅存在於剛進行的對話中，且規模仍小 | 直接在該視窗中使用 `/implement` |
| 尚未記錄在任何地方 | 使用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，若沒有程式碼庫則使用 [grill-me](https://aihero.dev/skills-grill-me) |
| 一個具體的行為，希望先寫測試，且沒有 spec | 直接使用 [tdd](https://aihero.dev/skills-tdd) |
| 已經建立完成，希望進行檢查 | 直接使用 [code-review](https://aihero.dev/skills-code-review) |

同一 session 的情況值得特別說明，因為該 skill 本身的第一行並未涵蓋此情況。`SKILL.md` 提到「spec 或 tickets」，這會促使 [model](https://www.aihero.dev/ai-coding-dictionary/model) 尋找一個不存在的檔案。如果計畫僅存在於對話串中，請在呼叫時說明這一點。

<a id="prerequisites"></a>

## 前置條件

`implement` 會提交到你目前所在的分支。它不會建立分支，也不會詢問。開始前請確認你處於希望進行工作的分支上。

如果 tickets 來自 [to-tickets](https://aihero.dev/skills-to-tickets)，[setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 已設定它們所在的追蹤器。`code-review` 在結案時會讀取相同的設定以找到來源 spec。

<a id="what-one-run-does"></a>

## 單次執行的內容

一次執行包含五個步驟，依序進行：

1. 讀取 ticket 或 spec，並找出接縫。
2. 在預先商定的接縫處驅動 [tdd](https://aihero.dev/skills-tdd)，每次處理一個紅綠切片。
3. 頻繁進行型別檢查，邊做邊執行單一測試檔案。
4. 最後執行一次完整的測試套件。
5. 執行 [code-review](https://aihero.dev/skills-code-review)，然後提交到目前的分支。

一次執行涵蓋一個 ticket。[to-tickets](https://aihero.dev/skills-to-tickets) 產生的 tickets 是追蹤彈道垂直切片，大小適配單個全新的 [context window](https://www.aihero.dev/ai-coding-dictionary/context-window)，因此預期的節奏是：清除 context、實作一個 ticket、提交、再次清除。每個 ticket 都是自包含的，因此你可以丟棄前一個 ticket 的 context。

<a id="pre-agreed-seams"></a>

## 預先商定的接縫

該 skill 的核心概念是 **seam**（接縫），即在不深入內部的情況下觀察行為的公開邊界。測試位於接縫處。當接縫在任何程式碼存在之前就已商定，測試就能持續有效，你可以重寫底層實作而不必更改測試。

「預先商定」這一點很重要，也是該 skill 最薄弱的環節。`implement` 內部沒有任何機制來商定接縫。`tdd` 是負責詢問的 skill，它拒絕在未確認的接縫處撰寫測試。因此實際上，商定發生在上游的 spec 中，或執行的首次互動中。如果任何地方都沒有商定，執行就會變成「只是寫程式碼」，且沒有任何警告。在 spec 中命名接縫可以防止這種情況。

<a id="common-questions"></a>

## 常見問題

**它完成了，但我的 ticket 仍然開啟，且驗收標準仍未勾選。**

這是正確的，也是預期的。`implement` 沒有完成步驟。它在 commit 時結束，絕不觸碰工作專案。這在 GitHub Issues 和本地 markdown 追蹤器上都是一樣的，因此這不是追蹤器整合問題。它也不會處理 `code-review` 產生的發現，也不會勾選來源 issue 上的 `- [ ]` 方塊。請自行關閉 ticket 並核對標準。這在依賴鏈上最為重要，因為 `to-tickets` 將前沿定義為所有阻擋者都已關閉的 tickets。如果沒有東西被關閉，就永遠不會有東西在視覺上解除阻擋。

**我可以一次指向所有 tickets，或並行執行多個嗎？**

使用 `/implement` 不行：一次呼叫，一個 ticket。若要一次執行整個 spec，請使用 [implement-spec](https://aihero.dev/skills-implement-spec)，它會將就緒前沿上的每個 ticket 交給 [subagent](https://www.aihero.dev/ai-coding-dictionary/subagent) 在各自的 worktree 中處理，然後將結果合併到一個整合分支。在單一 checkout 中並排執行多個 `/implement` session 比不受支援的情況更糟。一份現場報告描述了一個 `git commit --amend` 在一個 session 中落在另一個 session 的 commit 上，一個 stash 從 `refs/stash` 中消失，以及 commits 落在錯誤的分支上，這都在一個下午的三個 issues 中發生。這些 session 共用一個工作目錄、一個 index 和一個 HEAD。使用者透過 git worktrees 來規避此問題，但 `refs/stash` 在 worktrees 之間也是共用的，因此僅靠 worktrees 無法解決 stash 的情況。

**它可以開啟 pull request 而不是提交嗎？**

未內建此功能。它直接提交到目前的分支。許多人認為這過於急切，因為程式碼在他們驗證其運作之前就已落地。沒有設定旗標，也沒有 PR 模式。人們會在呼叫時覆蓋此行為（「提交到分支並開啟 PR」），或透過編輯該 skill 的本地副本來實現。當 agent 確實撰寫 PR 時，[pr](https://aihero.dev/skills-pr) 會塑造其內容。

**`code-review` 說它看不到我的變更。**

`code-review` 檢視 `git diff <fixed-point>...HEAD`，這排除了已暫存和工作樹中的變更。`implement` 在提交前執行它，因此除非已存在中間 commit，否則該 diff 中沒有可檢視的內容。多人已報告此問題，且雙方均未修復。請先提交，然後針對你分支的起點進行檢視。

另外，有些人完全不希望執行期間包含審查，因為代理審查自己剛寫好的程式碼時，會偏向自己的解決方案。在一個全新的 session 中，針對固定的點執行 [code-review](https://aihero.dev/skills-code-review) 是一個有效的替代方案。相同的偏差也是該 skill 將兩個軸線放在獨立的 sub-agent 中執行的原因。

**一張工單耗用了 150k tokens。我是不是用錯了？**

可能沒有。工單很可能太大了。一次執行會進行程式碼庫探索、每個接縫的紅綠迴圈、完整測試套件以及審查，因此一張非平凡的工單超過 100k [tokens](https://www.aihero.dev/ai-coding-dictionary/token) 是正常的，而不是出了問題的跡象。修正方法在上游。在 [to-tickets](https://aihero.dev/skills-to-tickets) 中調整工單的大小，讓每張工單都能放入一個全新的 window。如果單張工單持續超出，請拆分它，而不是提高 [effort](https://www.aihero.dev/ai-coding-dictionary/effort) 等級。

**`/implement #2` 在全新的 session 中處理了完全無關的東西。**

代理將 `#2` 解析為 context 中的另一個編號清單，例如 todo 檔案或檢查清單，而不是設定的 tracker。`implement` 現在會從 issue tracker 取得傳入的參考，並在開始前陳述其標題，當參考不明確時會詢問。請檢查該標題是否符合您意圖的工單；傳入 issue URL 或 `owner/repo#2` 可完全消除歧義。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示運作正常

- Session 開啟時會讀取工單或規格，並重述它將要建置的內容，而不是詢問您要建置什麼。
- 您可以在 trace 中看到實際的 `tdd` Skill 工具呼叫，而不只是 diff 中出現的測試。
- Typechecks 和單一測試檔案在執行期間反覆執行，而完整測試套件在接近結尾時執行一次。
- 執行在您目前的 branch 上達到 commit，而無需您提示它繼續。
- Diff 是一張工單份量的變更：貫穿每一層的垂直切片，而不是將多張工單合併在一起。

<a id="where-it-fits"></a>

## 適用位置

`implement` 是主鏈中的建置步驟：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

其相鄰技能包括 [to-tickets](https://aihero.dev/skills-to-tickets)，該技能會產生其消耗的工單，並宣告決定工單順序的封鎖邊；[tdd](https://aihero.dev/skills-tdd)，該技能會在每個接縫處內部驅動；以及 [code-review](https://aihero.dev/skills-code-review)，該技能會在提交前執行。它位於規劃技能的下游，並信任這些技能。它不會重新驗證所接收內容的結構，因此結構不良的圖表或水平分層的工單會按照書寫內容進行建置。

這種信任是 [wayfinder](https://aihero.dev/skills-wayfinder) 在 [to-spec](https://aihero.dev/skills-to-spec) 處合併到鏈上，而不是將其 map 直接迴圈進入 `implement` 的原因。只有當 effort 結果很小時，才從 map 直接前往 `implement`。

[ask-matt](https://aihero.dev/skills-ask-matt) 是當您不確定自己處於哪個流程時，對整套系統的 router。
