> [English](https://github.com/djpken/matt-skills/blob/c26b1aab3281344edfd32d1698cb58d30ca72dde/README.md) · [中文文件索引](./TRANSLATIONS.md)

<p>
  <a href="https://www.aihero.dev/s/skills-newsletter">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skills-repo-dark_2x.png">
      <source media="(prefers-color-scheme: light)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png">
      <img alt="Skills" src="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png" width="369">
    </picture>
  </a>
</p>

<a id="skills-for-real-engineers"></a>

# 給真正工程師的 Skills

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

繁體中文 · [English](https://github.com/djpken/matt-skills/blob/c26b1aab3281344edfd32d1698cb58d30ca72dde/README.md) · [情境技能路由](./docs/scenario-routing.md)

我每天都在用的 agent skills，用來做真正的工程，而不是 vibe coding。

開發真正的應用程式很困難。GSD、BMAD 和 Spec-Kit 等方法試圖透過掌控流程來協助。但這麼做的同時，它們奪走了你的控制權，並讓流程中的 bug 難以解決。

這些 skills 設計得小巧、易於調整且可組合。它們適用於任何 model。它們基於數十年的工程經驗。隨意改造它們。讓它們成為你自己的。享受吧。

如果你想追蹤這些 skills 的變更，以及我建立的任何新 skills，你可以加入我的 newsletter，那裡有約 60,000 名其他開發者：

[Sign Up To The Newsletter](https://www.aihero.dev/s/skills-newsletter)

<a id="installation-30-second-setup"></a>

## 安裝（30 秒設定）

Plugin 會自我更新。[skills.sh](https://skills.sh/mattpocock/skills) 會將可編輯的檔案複製到你的專案中，你需要手動更新它們。每個 agent 只選一個，因為同時安裝兩者會讓你擁有兩倍的 skills。

<a id="1-get-the-skills"></a>

### 1. 取得 skills

<details>
<summary><strong>Claude Code</strong></summary>

```bash
claude plugin install mattpocock-skills@claude-plugins-official
```

預設會自我更新。

</details>

<details>
<summary><strong>Codex</strong></summary>

```bash
codex plugin marketplace add mattpocock/skills
codex plugin add mattpocock-skills@mattpocock
```

啟動時會自我更新。

</details>

<details>
<summary><strong>GitHub Copilot</strong> (CLI and VS Code)</summary>

```bash
copilot plugin marketplace add mattpocock/skills
copilot plugin install mattpocock-skills@mattpocock
```

然後，只需一次，加入 `~/.copilot/settings.json`：

```json
{
  "extraKnownMarketplaces": {
    "mattpocock": { "source": { "source": "github", "repo": "mattpocock/skills" }, "autoUpdate": true }
  }
}
```

在 VS Code 中，執行 **Chat: Install Plugin From Source** 並輸入 `https://github.com/mattpocock/skills`。它每天更新。

</details>

<details>
<summary><strong>Gemini CLI</strong> (manual updates)</summary>

```bash
gemini skills install https://github.com/mattpocock/skills.git --path skills/engineering
gemini skills install https://github.com/mattpocock/skills.git --path skills/productivity
```

重新執行這兩個指令以更新。

</details>

<details>
<summary><strong>Any other agent, or editable files</strong> (manual updates)</summary>

```bash
npx skills@latest add mattpocock/skills -a <agent>  # cursor, opencode, devin, windsurf, amp, pi; omit -a to choose
```

當安裝程式詢問要採用哪些 skills 時，請包含 `setup-matt-pocock-skills`。若要更新，請執行 `npx skills@latest update`，並重新執行 `add` 以獲取新的 skills。

</details>

<a id="2-run-setup-matt-pocock-skills"></a>

### 2. 執行 `/setup-matt-pocock-skills`

在你的 agent 中，每個 repo 執行一次。它會：

- 詢問你想使用哪個 issue tracker（GitHub、GitLab、本地檔案，或你描述的任何其他工具）
- 詢問你在 triage tickets 時會套用哪些 labels（`/triage` 使用 labels）
- 詢問你想將我們建立的任何 docs 儲存在哪裡

<a id="3-bam---youre-ready-to-go"></a>

### 3. 完成，你準備好了。

<a id="why-these-skills-exist"></a>

## 為什麼這些 Skills 存在

我建立這些 skills 是為了修正我在 Claude Code、Codex 和其他 coding agents 中看到的常見失敗模式。

<a id="1-the-agent-didnt-do-what-i-want"></a>

### #1: Agent 沒有做我想要的事

> 「沒有人完全知道他們想要什麼」
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**問題**。軟體開發中最常見的失敗模式是認知不一致。你以為開發者知道你想要什麼。然後你看到他們建好的東西，你意識到他們完全沒有理解你。

在 AI 時代也是如此。你與 agent 之間存在溝通落差。解決這個問題的方法是進行一次**嚴謹的問答 session**，讓 agent 向你詢問關於你要建置內容的詳細問題。

**解決方案**是使用：

- [`/grill-me`](./skills/productivity/grill-me/SKILL.md) - 用於非程式碼用途
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) - 與 [`/grill-me`](./skills/productivity/grill-me/SKILL.md) 相同，但增加了更多額外功能（見下文）

這些是我最受歡迎的 skills。它們幫助你在開始之前與 agent 達成共識，並深入思考你要進行的變更。每次你想進行變更時，都要使用它們。

<a id="2-the-agent-is-way-too-verbose"></a>

### #2: Agent 過於冗長

> 透過通用語言，開發者之間的對話以及程式碼的表達都源自同一個 domain model。
>
> Eric Evans, [Domain-Driven-Design](https://www.amazon.co.uk/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)

**問題**：在專案初期，開發者與他們為之建置軟體的人（domain experts）通常使用不同的語言。

我與我的 agents 之間也感受到同樣的張力。Agents 通常被丟進專案中，並被要求邊做邊摸索術語。因此，它們在一個字就夠用的地方使用了 20 個字。

**解決方案**是建立共享語言。這是一份檔案，幫助 agents 解讀專案中使用的術語。

<details>
<summary>
範例
</summary>

這裡是一個 [glossary](https://github.com/mattpocock/course-video-manager/blob/076a5a7a182db0fe1e62971dd7a68bcadf010f1c/CONTEXT.md) 的範例（在該 pinned commit 時仍名為 `CONTEXT.md`，來自 skills 重新命名慣例之前），來自我的 `course-video-manager` repo。哪一個比較容易閱讀？

- **之前**：「當課程中某個 section 內的 lesson 被設為『真實』時（即在檔案系統中佔有一個位置），會出現問題」
- **之後**：「materialization cascade 有問題」

這種簡潔性在 session 中不斷帶來回報。

</details>

這已內建於 [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md)。這是一次嚴謹的問答 session，但它幫助你與 AI 建立共享語言，並用 ADR 記錄難以解釋的決策。

很難解釋這有多強大。它可能是這個 repo 中最酷的技巧。試試看，你會明白。

> [!TIP]
> 共享語言除了減少冗長之外，還有許多其他好處：
>
> - **變數、函式和檔案的命名保持一致**，使用共享語言
> - 因此，**codebase 對 agent 來說更容易導航**
> - Agent 也**在思考上花費更少的 tokens**，因為它可以使用更簡潔的語言

<a id="3-the-code-doesnt-work"></a>

### #3: 程式碼無法運作

> 「始終採取小而謹慎的步驟。回饋的速率是你的速度限制。永遠不要承擔太大的任務。」
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**問題**：假設你和 agent 在要建置什麼上已經達成共識。當 agent _仍然_ 產出垃圾時，會發生什麼事？

該檢視你的回饋迴路了。如果沒有關於程式碼實際執行方式的回饋，agent 就是在盲目飛行。

**解決方案**：你需要一組標準的回饋迴路：靜態型別、瀏覽器存取，以及自動化測試。

對於自動化測試，red-green-refactor 迴路至關重要。這是 agent 先寫一個失敗的測試，然後修復測試的過程。這有助於為 agent 提供一致的回饋水平，從而產生好得多的程式碼。

我建立了一個 **[`/tdd`](./skills/engineering/tdd/SKILL.md) skill**，你可以將其嵌入任何專案。它鼓勵 red-green-refactor，並為 agent 提供關於什麼是好測試和壞測試的大量指導。

對於除錯，我也建立了一個 **[`/diagnosing-bugs`](./skills/engineering/diagnosing-bugs/SKILL.md)** skill，將最佳除錯實務包裝成一個有紀律的迴路，並按階段進行閘控。

<a id="4-we-built-a-ball-of-mud"></a>

### #4: 我們建了一個泥球

> 「_每天_ 投資於系統的設計。」
>
> Kent Beck, [Extreme Programming Explained](https://www.amazon.co.uk/Extreme-Programming-Explained-Embrace-Change/dp/0321278658)

> 「最好的模組是深的。它們允許透過簡單的介面存取大量功能。」
>
> John Ousterhout, [A Philosophy Of Software Design](https://www.amazon.co.uk/Philosophy-Software-Design-2nd/dp/173210221X)

**問題**：大多數使用 agents 建置的應用程式都很複雜且難以變更。因為 agents 可以極大地加速編碼，它們也加速了軟體熵。Codebases 以前所未有的速度變得更加複雜。

**解決方案** 是採用一種全新的激進方法來進行 AI 驅動的開發：重視程式碼的設計。

這已內建於這些 skill 的每一個層級中：

- [`/to-spec`](./skills/engineering/to-spec/SKILL.md) 會在建立規格前，詢問您即將修改哪些模組

而且關鍵在於，[`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) 會掃描程式碼庫以尋找深化機會，並提供候選專案。我建議每隔幾天就在您的程式碼庫上執行一次。這是一項調查，而非救援：在真正陳舊的程式碼庫上，它會找到真正的候選專案，但不會替您理清混亂。

<a id="summary"></a>

### 摘要

軟體工程基礎比以往任何時候都更重要。這些 skill 是我將這些基礎精簡為可重複執行的實務的最佳努力，以協助您推出職業生涯中最好的應用程式。祝您使用愉快。

<a id="reference"></a>

## 參考

這些 skill 依一個軸線分類：誰可以呼叫它們。**使用者呼叫** 的 skill 僅在您輸入時才可觸及（例如 `/grill-me`）；其職責是進行協調。**模型呼叫** 的 skill 可由您呼叫，或在任務符合時由 agent 自動觸及；它們承載可重複使用的紀律。使用者呼叫的 skill 可以呼叫模型呼叫的 skill，但絕不能呼叫另一個使用者呼叫的 skill。

<a id="engineering"></a>

### 工程

我日常用於程式碼工作的 skill。

**使用者呼叫**

- **[ask-matt](./skills/engineering/ask-matt/SKILL.md)**：詢問哪種 skill 或流程適合您的情況。這是本儲存庫中使用者呼叫 skill 的路由器。
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)**：一場嚴格的審問會議，同時建立您專案的領域模型，精煉術語並即時更新 `GLOSSARY.md` 和 ADR。
- **[triage](./skills/engineering/triage/SKILL.md)**：透過分級角色的狀態機來處理 issue。
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)**：掃描程式碼庫以尋找深化機會，將其呈現為視覺化 HTML 報告，然後針對您選擇的專案進行嚴格的審問。
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.md)**：為工程 skill 設定此儲存庫（issue 追蹤器、分級標籤、領域檔案佈局）。在使用其他工程 skill 前，請在每個儲存庫上執行一次。
- **[to-spec](./skills/engineering/to-spec/SKILL.md)**：將目前的對話轉為規格並發布到 issue 追蹤器。沒有訪談，僅綜合您已討論的內容。
- **[to-tickets](./skills/engineering/to-tickets/SKILL.md)**：將任何計畫、規格或對話拆解為一組曳光彈 ticket，每個 ticket 都宣告其阻擋邊界，以文字形式寫入本地檔案，或在真實追蹤器上建立原生阻擋連結。
- **[implement](./skills/engineering/implement/SKILL.md)**：建立由規格或 ticket 集合所描述的工作，在預先協定的接縫處驅動 `/tdd`，並在 commit 前以 `/code-review` 收尾。
- **[implement-spec](./skills/engineering/implement-spec/SKILL.md)**：在單一整合分支上實作整個規格。將 ticket 視為任務圖，在就緒前沿執行 implementer subagent 以達到最大並行度，然後以 `/code-review` 收尾。
- **[wayfinder](./skills/engineering/wayfinder/SKILL.md)**：規劃一大塊工作，其規模超過單一 agent 會話所能容納，將其作為 issue 追蹤器上的決策 ticket 共享地圖，並逐一解決，直到通往目的地的路徑清晰為止。
- **[retro](./skills/engineering/retro/SKILL.md)**：在會話結束後，建議改進 coding agent 的環境（導航、自動化檢查、編碼標準、引導檔案、工具），最嚴重的問題優先。

**模型呼叫**

- **[prototype](./skills/engineering/prototype/SKILL.md)**：建立一次性原型以回答設計問題，可以是單一可分享的 HTML 檔案用於狀態或邏輯問題，或是多個截然不同的 UI 變體，可從單一路由切換。
- **[diagnosing-bugs](./skills/engineering/diagnosing-bugs/SKILL.md)**：針對困難 bug 和效能退化的紀律性診斷迴圈：建立一個在此 bug 上變紅的回饋迴圈 → 最小化 → 假設 → 插樁 → 修復 → 回歸測試。
- **[research](./skills/engineering/research/SKILL.md)**：針對高信任度的主要來源調查問題，並將發現作為帶引用的 Markdown 檔案記錄在儲存庫中，作為背景 agent 執行。
- **[tdd](./skills/engineering/tdd/SKILL.md)**：採用紅綠重構迴圈的測試驅動開發。一次建立一個垂直切片的功能或修復一個 bug。
- **[domain-modeling](./skills/engineering/domain-modeling/SKILL.md)**：積極建立並精煉專案的領域模型：挑戰術語是否符合詞彙表，以邊界情況場景進行壓力測試，並即時更新 `GLOSSARY.md` 和 ADR。
- **[codebase-design](./skills/engineering/codebase-design/SKILL.md)**：設計深度模組的共享紀律和詞彙：在小型介面背後隱藏大量行為，放置在乾淨的接縫處，並可透過該介面進行測試。
- **[code-review](./skills/engineering/code-review/SKILL.md)**：對自固定點以來的 diff 進行雙軸審查：**標準**（是否遵循儲存庫的編碼標準，加上 Fowler 異味基準？）和**規格**（是否忠實實作來源 issue/規格？），作為並行 sub-agent 執行，以免彼此汙染。
- **[pr](./skills/engineering/pr/SKILL.md)**：pull request 主體應具備的形狀：摘要作為使變更清晰的最小視覺元素，證明其運作的前後證據，以及合併風險評估（單向門或雙向門，加上影響範圍）。
- **[wizard](./skills/engineering/wizard/SKILL.md)**：產生一個互動式 bash wizard，引導人類完成只有他們才能執行的步驟：設定基礎設施、設定憑證或 CI 金鑰、導覽不熟悉的新第三方儀錶板，或執行一次性遷移或切換。

<a id="productivity"></a>

### 生產力

一般工作流程工具，非特定於程式碼。

**使用者呼叫**

- **[grill-me](./skills/productivity/grill-me/SKILL.md)**：針對計畫或設計進行嚴格的訪談，直到設計樹的每個分支都解決為止。
- **[handoff](./skills/productivity/handoff/SKILL.md)**：將目前的對話壓縮為交接檔案，以便另一個 agent 可以繼續工作。
- **[teach](./skills/productivity/teach/SKILL.md)**：透過多個會話教導使用者新的 skill 或概念，使用目前目錄作為有狀態的教學工作區。
- **[to-questionnaire](./skills/productivity/to-questionnaire/SKILL.md)**：將您無法獨自回答的決策轉為 Markdown 問卷，交給唯一能回答的人，可非同步填寫，或在會議中一起完成。它會就傳送物件（給誰、需要什麼回饋）向您提出嚴格的審問，而非就主題本身。
- **[wait-what](./skills/productivity/wait-what/SKILL.md)**：當訊息未能傳達時立即觸發此 skill。Agent 會用您缺少的背景資訊，以簡潔的英文重新陳述，並使用您的 `GLOSSARY.md` 詞彙。

**模型呼叫**

- **[grilling](./skills/productivity/grilling/SKILL.md)**：針對計畫、決策或想法對使用者進行嚴格的訪談，直到設計樹的每個分支都解決為止。這是 `grill-me`、`grill-with-docs`、`triage`、`wayfinder` 和 `improve-codebase-architecture` 背後的可重用訪談原語。
- **[writing-for-agents](./skills/productivity/writing-for-agents/SKILL.md)**：為 agent 撰寫檔案：skill、AGENTS.md/CLAUDE.md，以及任何 agent 透過指標觸及的檔案。
