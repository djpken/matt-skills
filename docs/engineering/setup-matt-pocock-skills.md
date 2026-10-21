<a id="what-it-does"></a>

## 功能說明

`setup-matt-pocock-skills` 會針對單一儲存庫回答三個問題：問題（issues）存放於何處、分診（triage）標籤的名稱為何，以及領域檔案（domain docs）的位置。它會將這些答案記錄為 `docs/agents/` 下的 markdown 檔案。

這些檔案是不同儲存庫之間唯一會有所差異的部分。skill 本身在所有環境中皆完全相同。它們會在執行時讀取 `docs/agents/issue-tracker.md`，並依照其指示執行。這就是為何這套系統不繫結於 GitHub，且您無需編輯任何 skill 檔案來將其指向其他追蹤器。使用「將 skills 連結至自訂問題追蹤器」來呼叫它，可適用於任何能透過程式介面連線的系統，且無需對 skills 進行任何修改。

這是一個由提示詞（prompt）驅動的 skill，而非確定性指令碼。它會讀取您的 `git remote`、`CLAUDE.md` 和 `GLOSSARY.md`，提出其發現的內容，並在寫入任何內容前等待您確認。

<a id="when-to-reach-for-it"></a>

## 何時使用

您透過輸入 `/setup-matt-pocock-skills` 來呼叫此功能；[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不會自行主動使用它。其元資料（metadata）刻意將其標記為不可自動呼叫，因此其他 skill 無法為您觸發它。

請在每個儲存庫中僅使用一次，且須在任何其他工程 skill 首次使用之前執行。如果 [triage](https://aihero.dev/skills-triage)、[to-spec](https://aihero.dev/skills-to-spec)、[to-tickets](https://aihero.dev/skills-to-tickets) 或 [wayfinder](https://aihero.dev/skills-wayfinder) 開始猜測您的問題去向，或套用您的追蹤器中不存在的標籤，這表示該儲存庫尚未完成設定。您可以在專案進行到一半時於儲存庫中執行此功能。該 skill 會讀取已存在的內容，因此不會遺失先前的工作成果。

<a id="prerequisites"></a>

## 先決條件

它會寫入您執行它的儲存庫中：

| 寫入內容 | 位置 |
| --- | --- |
| `issue-tracker.md` | `docs/agents/` |
| `domain.md` | `docs/agents/` |
| `triage-labels.md` | `docs/agents/`，僅在安裝了 `triage` skill 時 |
| 一個 `## Agent skills` 區塊 | `CLAUDE.md` 或 `AGENTS.md` 中已存在的那一個 |

您會將所有內容以 markdown 格式提交。沒有使用者層級或全域模式。設定檔位於儲存庫中，因此每個儲存庫都會擁有自己的副本。

<a id="the-three-decisions"></a>

## 三個決策

它會以建議答案開始每個章節，並跳過其探索過程已回答的問題。大多數執行僅需兩次確認。

| 決策 | 建議內容 | 詢問時機 |
| --- | --- | --- |
| **問題追蹤器** | 與您的 `git remote` 相符的那一個 | 總是詢問，因為這是唯一的真實選擇 |
| **分診標籤** | 保留五個標準名稱（`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`） | 僅在安裝了 `triage` skill 時 |
| **領域檔案** | 單一上下文：根目錄下的一個 `GLOSSARY.md` 加上 `docs/adr/` | 僅在偵測到 monorepo 訊號時，屆時會提供多上下文 `GLOSSARY-MAP.md` |

追蹤器選項：

| 選項 | 問題存放位置 | 需求 |
| --- | --- | --- |
| **GitHub** | 該儲存庫的 GitHub Issues | `gh` CLI |
| **GitLab** | 該儲存庫的 GitLab Issues | `glab` CLI |
| **本地 markdown** | 本儲存庫中 `.scratch/<feature>/` 下的檔案 | 無，甚至不需要遠端 |
| **其他** | 您指定的任何位置 | 由您提供一段描述工作流程的文字 |

前三項作為模板隨 skill 一併發布，可即開即用。本地 markdown 是一個完整選項，而非備援方案。該 skill 支援沒有遠端的個人專案。一個注意事項：如果您使用 GitHub，請勿使用本地 markdown。它們是互斥的替代方案，請擇一使用。

「其他」也是一個完整選項。Jira、Linear、Azure DevOps 和 Beads 皆以此方式運作。您描述工作流程，skill 會將您的文字記錄在 `docs/agents/issue-tracker.md` 中，下游 skills 則會遵循該文字。使用者已建立過此類設定：一個基於 [MCP](https://www.aihero.dev/ai-coding-dictionary/mcp) 的 Jira 變體、一個形狀類似 `gh` 的 Gitea CLI，以及一個手工建立的本地儀錶板。

<a id="common-questions"></a>

## 常見問題

**我必須使用 GitHub 嗎？**

不需要。GitHub、GitLab 以及 `.scratch/` 下的本地 markdown 皆以現成模板形式發布，其他任何系統皆可透過「其他」路徑運作。這是被問得最多的問題，大致如下：*「硬性鎖定在 github」*、*「我可以使用 GitLab / Jira 嗎」*、*「Azure DevOps 呢」*。答案始終相同：是設定選擇追蹤器，而非 skill。

**更新 skills 後，我需要重新執行嗎？**

在 v1.1 之後的直接答案是肯定的。該 skill 自身的結尾訊息則較為寬鬆。它告訴您僅在切換追蹤器或重新開始時才需重新執行。兩者皆有道理。種子模板（seed templates）會隨版本變更，因此來自較舊版本的 `docs/agents/issue-tracker.md` 可能相對於現在讀取它的 skills 而過時。如果下游 skill 的行為與檔案描述不同，請重新執行設定。這很輕量。

**它寫入了 `CLAUDE.md`，但我使用的是 Codex。**

這是一個已知的缺口，且仍未解決。檔案選擇規則為「如果 `CLAUDE.md` 存在則編輯它，否則使用 `AGENTS.md`」。它檢查的是哪個檔案存在，而非哪個 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 正在執行。在一個留有 Claude Code 殘留 `CLAUDE.md` 的儲存庫中，該 skill 會將其 `## Agent skills` 區塊寫入一個 Codex 永遠不會讀取的檔案。使用者有兩種變通方法：手動將區塊移至 `AGENTS.md`，或保持 `AGENTS.md` 為標準並讓 `CLAUDE.md` 成為指向它的一行指標。如果兩個檔案都不存在，該 skill 會詢問您要建立哪一個，而不是自行選擇。這讓預期它會自行決定的人感到困惑。

**它沒有建立我的分診標籤。**

它不會建立。`docs/agents/triage-labels.md` 是一個*對映*：它告訴 `/triage` 您的追蹤器中哪些字串對應於五個標準角色。它不會執行 `gh label create`。在新的 GitHub 儲存庫中，標籤尚不存在，使用者已多次將此視為錯誤提出。有兩個後果：

- 如果您的追蹤器已使用標準名稱，則對映為同一性表格（identity table），無需任何設定。這是預期的常見情況，而非遺漏的步驟。
- 此 skill 也不會建立 [wayfinder](https://aihero.dev/skills-wayfinder) 的 `wayfinder:map` 和 `wayfinder:<type>` 標籤，且 `gh issue create --label <missing>` 會失敗而非建立標籤。請在 GitHub 儲存庫上首次執行 wayfinder 之前手動建立它們。

**我能在此處設定其他 skills 的行為嗎（[grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 頻率、問題格式、語氣）？**

不能。它只設定三項內容：追蹤器、標籤、檔案佈局。使用者曾要求將其作為個人偏好設定的地方。答案是 skills 保持其觀點（opinionated）且不接受個人層級的設定。偏好應作為純文字指令放在您的 `CLAUDE.md` 中，所有 skills 已會讀取該檔案。

**我能將設定保留在 `~/.claude` 中，而不提交到每個儲存庫嗎？**

目前不行。一位在多個儲存庫中執行 skills 的使用者已提出此請求，但不存在使用者層級模式。每個儲存庫都攜帶自己的 `docs/agents/`。

**擁有一個用於設定其他 skills 的 skill，這不是很奇怪嗎？**

一個長期存在的抱怨正是如此：*「擁有一個用來設定其他 skill 的 skill 讓我覺得不對勁：這表示 LLM 正在設定它自己的 skills。」* 這個權衡是真實存在的。如果沒有設定步驟，每個涉及 issues 的 skill 都需要一份 tracker 指示的副本。輸出的是你可以閱讀和編輯的 markdown，這限制了風險。你可以閱讀它寫入的每個檔案並手動修改。請以這種方式進行日常變更，而不是再執行一次。

<a id="its-working-if"></a>

## 如果符合以下條件，即表示運作正常

- `docs/agents/issue-tracker.md` 和 `docs/agents/domain.md` 存在，另外如果 `triage` 已安裝，則 `triage-labels.md` 也必須存在。
- 你的 harness 所讀取的指示檔案中出現了一個 `## Agent skills` 區段，其中包含一行摘要，指向上述每個檔案。
- 它建議的 tracker 與你使用的 remote 相符，且 label 字串與你 tracker 中存在的 labels 相符。
- 之後，`/to-tickets` 發布時不會詢問你 issues 的位置，而 `/triage` 會套用 labels 而不是自行發明。
- skill 檔案本身沒有任何變更。如果 setup 編輯了 `SKILL.md`，則表示出了問題。

<a id="where-it-fits"></a>

## 適用位置

`setup-matt-pocock-skills` 是工程流程的**一次性設定**，是所有其他步驟假設的前置條件，而非鏈條中的一個步驟。它的鄰居是它的讀者：[triage](https://aihero.dev/skills-triage)，套用在此處撰寫的 label 詞彙；[to-spec](https://aihero.dev/skills-to-spec) 和 [to-tickets](https://aihero.dev/skills-to-tickets)，發布到此處命名的 tracker；以及 [wayfinder](https://aihero.dev/skills-wayfinder)，讀取同一個 tracker 檔案中的「Wayfinding operations」區段，以學習如何儲存 maps 和子 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)。[domain-modeling](https://aihero.dev/skills-domain-modeling) 稍後會填入 setup 所記錄的 domain-doc 佈局。只有當你解決某個術語或決策時，它才會建立 `GLOSSARY.md` 和 ADR，因此設定完成後儲存庫中沒有 domain docs 是正常的。關於接下來該使用哪個 skill，[ask-matt](https://aihero.dev/skills-ask-matt) 會為整套流程進行路由。
