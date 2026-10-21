<a id="what-it-does"></a>

## 功能說明

`code-review` 會沿著兩個軸線，檢視 `HEAD` 與您指定的固定點（commit、branch、tag、`main`、`HEAD~5`）之間的 diff。**Standards** 軸線詢問程式碼是否遵循此儲存庫的撰寫方式。**Spec** 軸線詢問程式碼是否執行了原始 issue 或 [spec](https://www.aihero.dev/ai-coding-dictionary/spec) 所要求的功能。每個軸線都在各自的 [sub-agent](https://www.aihero.dev/ai-coding-dictionary/subagent) 中執行，因此兩者互不看見對方的推理過程。

此 skill 絕不合併或重新排序這兩個軸線。報告會以每個軸線最嚴重的問題結尾，並拒絕在兩者之間指定單一勝者。變更可能透過一個軸線但失敗於另一個。遵循所有慣例但實作了錯誤功能的程式碼，會透過 Standards 但失敗於 Spec。完全按照 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket) 要求執行但破壞儲存庫慣例的程式碼，則情況相反。混合的判定會讓透過的軸線掩蓋失敗的軸線。

<a id="when-to-reach-for-it"></a>

## 何時使用

輸入 `/code-review`，或者當您要求檢視 branch、PR、進行中的工作，或任何「自 X 以來」的內容時，agent 會自動使用它。

| 您的情況 | 使用 |
| --- | --- |
| 存在 diff，且您想知道它是否建構正確 *且* 是否為正確的事物 | `code-review` |
| 您想在 diff 中尋找 bug：null 路徑、競態條件、差一錯誤 | Claude Code 內建的檢視功能，而非此功能（見下方的名稱衝突） |
| 尚未撰寫任何內容，且您希望以測試優先的方式撰寫 | [tdd](https://aihero.dev/skills-tdd) |
| 需要建構整個 spec，包含檢視 | [implement](https://aihero.dev/skills-implement)，它會自行呼叫此 skill |
| 整個程式庫已偏離，而非單一 diff | [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) |
| 某些東西壞了，且您不知道原因 | [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs) |

您必須提供固定點。如果您未提供，skill 會要求您提供，而不是猜測。在啟動任何 sub-agent 之前，它會檢查 ref 是否可解析，以及 diff 是否為空，因此拼錯的 branch 名稱會在您面前失敗，而不是在兩個 sub-agent 內部失敗。

<a id="prerequisites"></a>

## 先決條件

Standards 軸線不需要任何額外條件。它會讀取儲存庫所記錄的內容（`CODING_STANDARDS.md`、`CONTRIBUTING.md` 等），並在儲存庫未記錄任何內容時，回退到內建基準。

Spec 軸線需要 spec 存在且可找到。它依此順序查詢：

1. commit 訊息中的 issue 參考（`#123`、`Closes #45`、GitLab `!67`），透過 tracker 檔案擷取。
2. 您作為引數傳入的路徑。
3. 位於 `docs/`、`specs/` 或 `.scratch/` 下，與 branch 或功能名稱相符的 spec 檔案。
4. 詢問您。

步驟 1 依賴 tracker 檔案，該檔案由 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 撰寫。若沒有該檔案，只要您提供路徑，該軸線仍可運作。若完全沒有 spec，skill 會跳過 Spec sub-agent，報告會顯示 "no spec available"，而不是捏造需求。

<a id="the-two-axes"></a>

## 兩個軸線

| | Standards | Spec |
| --- | --- | --- |
| 問題 | 是否建構正確？ | 是否為正確的事物？ |
| 讀取 | 儲存庫記錄的標準，加上 smell 基準 | 原始 issue 或 spec |
| 報告 | 記錄的違規（可以是硬性規定），以及 smells（總是判斷取決於情況） | 缺失或部分需求、範圍蔓延、錯誤實作的需求 |
| 每個發現引用 | 標準檔案與規則，或命名的 smell 加上 hunk | spec 的特定行 |

此設計旨在避免通用的檢視 skill，該 skill 不知道您的標準。這樣的 skill 會標記您程式庫中刻意為之的內容，並遺漏您的程式庫所依賴的不變數。因此，儲存庫自己的檔案是 Standards 軸線上的 [主要來源](https://www.aihero.dev/ai-coding-dictionary/primary-source)，且**儲存庫永遠優先**。

**Smell 基準**位於儲存庫標準之下。它包含 Fowler 的 _Refactoring_ 第 3 章中的十二種程式碼 smell：Mysterious Name、Duplicated Code、Feature Envy、Data Clumps、Primitive Obsession、Repeated Switches、Shotgun Surgery、Divergent Change、Speculative Generality、Message Chains、Middle Man、Refused Bequest。每種都是帶有標籤的啟發式方法（「可能的 Feature Envy」），絕非硬性違規。每種都說明該 smell 是什麼以及如何修復，因此發現會附上修復方法，而不只是抱怨。兩個軸線都會跳過您的 linter 已經強制執行的任何內容。

<a id="common-questions"></a>

## 常見問題

**它與 Claude Code 自己的 `/code-review` 衝突。我該怎麼辦？**

這是此 skill 最常被報告的問題，且尚未修復。Claude Code 隨附自己的 `/code-review`，它執行不同的功能：它在 diff 中尋找 bug，而此功能檢查 spec 相容性與儲存庫標準。當您安裝此程式庫時，其中一個會勝出，而哪個勝出取決於您的安裝方式：

- **Plugin marketplace。** 每個 skill 都會獲得 `mattpocock-skills:` 字首，且內建功能在無限定名稱下難以觸及。
- **一般 skill 安裝。** 本地檔案勝出，且此 skill 會遮蔽內建功能。

一個解決方案是完全移除 Claude Code 的內建 skills。這可以節省大量 [context](https://www.aihero.dev/ai-coding-dictionary/context)，且衝突不再重要。遮蔽本身可說是 Claude Code [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 的 bug（skill 作者應可自由命名 skill），因此另一個解決方案是重新命名本地副本。`npx skills update` 會撤銷對 frontmatter 的編輯或重新命名的目錄。使用者報告的永續性變通方法是將 skill fork 到新名稱，並從受管理的集合中移除 `code-review`。保留您 fork 來源的 commit 記錄，以便手動重新同步。

**它的 sub-agents 不斷再次呼叫 `/code-review` 並生成更多 agents。**

這是一個已知的未修復 bug。多人在多個 harness 中重現了此問題。Standards 和 Spec 提示未禁止委派，因此 sub-agent 可能再次找到該 skill 並再次扇出。一份報告顯示超過 50 個 agents。人們在 forks 上應用的修復是在兩個 sub-agent 簡報中附加一行：「不要呼叫 `/code-review` 或生成額外 agents：直接執行此檢視。」有些人偏好從 harness 層級處理，以便每個 skill 都繼承此保護。兩者都尚未包含在發行的 skill 中。如果您無人監督地執行此功能，請監控 agent 數量。

**我應該在撰寫程式碼的同一個 [session](https://www.aihero.dev/ai-coding-dictionary/session) 中執行它嗎？**

建議使用全新的執行環境。正如一位讀者所言：「在同一個上下文中審查自己，那不是審查，而是帶有斜線指令的確認偏誤。」在撰寫階段進行審查的 agent，其上下文中包含了塑造該程式碼的所有假設。獨立的審查者則不會擁有這些上下文。這也正是為什麼人們要求 [implement](https://aihero.dev/skills-implement) 移除其內建的審查步驟，因為該步驟會在剛寫完 diff 的同一個 session 中執行審查。獨立版本的做法是，您從一個乾淨的 session 中自行呼叫 `/code-review`。

**是在每個 ticket 之後執行，還是在最後統一執行？**

兩種方式都可行，且該 skill 不會替您做決定。逐個 ticket 審查可確保每個 diff 夠小，讓 Spec 軸線有一個明確的 spec 可供比對，這正是 `implement` 使用的模式。而在分支末尾統一審查，則能捕捉到逐個 ticket 審查時各自遺漏的 ticket 間互動。如果您不確定，建議逐個 ticket 審查，並以分支建立點為基準，再做一次最終審查。

**我可以信任這些發現嗎？**

未經核實前不可信任。Sub-agent 的輸出是假設，而非證據。有一個團隊報告了十幾項破壞性變更，這些問題在基於文字描述的審查中都被遺漏了。該 skill 會將兩份報告原樣或輕微清理後合併。它不會針對檔案重新驗證每一項聲稱，因此發現可能引用錯誤的位置，或誇大影響範圍。在採取行動前，請先閱讀每項發現的引用。該 skill 要求每項發現都必須附有引用（標準規則、smell 及其對應的 hunk，或 spec 行），這正是讓這些發現可被核實的原因。

**為什麼我每次執行它，它都會發現新的問題？**

每次修復都會新增需要審查的程式碼，且 Standards 軸線中涉及判斷的部分，每次執行都會產生不同的結果。一位讀者描述了這個迴圈：「/code-review 和 /improve-code-architecture 每次都會發現新東西。我實施修復，重新執行這些 skills，然後一次又一次。」沒有收斂保證。請將一次執行視為一份線索清單。處理那些背後有引用規則支撐的專案，然後停止。不要迴圈執行直到它返回乾淨結果，因為它永遠不會。

**它會審查我未提交的變更嗎？**

不會。它對 `<fixed-point>...HEAD` 進行 diff。三點形式從 merge-base 開始測量，並排除已暫存（staged）和工作樹（working-tree）中的變更。如果 `implement` 尚未進行中間 commit，審查將無法看到即將進入下一個 commit 的工作。請先 commit，然後審查，再進行 amend 或新增 fixup。

<a id="its-working-if"></a>

## 如果出現以下情況，表示它運作正常

- 在啟動任何 sub-agent 之前，若遇到無效的 ref 或空的 diff，它會拒絕啟動。
- 報告以兩個獨立的區塊形式出現，分別位於 `## Standards` 和 `## Spec` 之下，而非合併為單一清單。
- 每項 Standards 發現都會指名您儲存庫檔案中的一條規則，或十二種 smell 之一，並引用對應的 hunk；每項 Spec 發現都會引用 spec 中的一行。
- 結尾摘要會給出每個軸線的最嚴重問題，並拒絕選出整體的勝者。
- 在沒有可用 spec 的情況下，Spec 區塊會說明此情況，而不是列出從程式碼中推斷出的需求。

<a id="where-it-fits"></a>

## 適用場景

`code-review` 是建置鏈結尾附近的審查步驟：`grill-with-docs → to-spec → to-tickets → implement → code-review → retro`。它也可以獨立用於您指定的任何分支或 PR。

- [implement](https://aihero.dev/skills-implement) 是最接近的鄰居。它驅動建置，並在 commit 前將此 skill 作為其自身的結尾審查來呼叫。[implement-spec](https://aihero.dev/skills-implement-spec) 會對整個整合分支執行一次相同的操作。
- [retro](https://aihero.dev/skills-retro) 在其之後執行並進行調整。當某個 session 顯示審查遺漏了某一類錯誤時，`retro` 會提出檢查專案或 `CODING_STANDARDS.md` 規則，供 Standards 軸線隨後讀取。
- [pr](https://aihero.dev/skills-pr) 在通過審查的變更推送後，撰寫 pull request 的內容。
- [to-spec](https://aihero.dev/skills-to-spec) 和 [to-tickets](https://aihero.dev/skills-to-tickets) 產生 Spec 軸線比對的檔案，因此模糊的 spec 會使該軸線變得模糊。
- [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 是整個 codebase 的對應版本，因為此 skill 只檢視單一 diff。

[ask-matt](https://aihero.dev/skills-ask-matt) 在您不確定情境需要哪個 skill 時，會在整個集合中進行路由。
