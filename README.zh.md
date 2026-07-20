<p>
  <a href="https://www.aihero.dev/s/skills-newsletter">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skills-repo-dark_2x.png">
      <source media="(prefers-color-scheme: light)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png">
      <img alt="Skills" src="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png" width="369">
    </picture>
  </a>
</p>

# 真正工程師的 Skills

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

> [English](./README.md) · 繁體中文 · [情境技能路由 →](./docs/scenario-routing.md)

我每天用來做真正工程的 agent skills，不是 vibe coding。

開發真實應用程式很難。GSD、BMAD、Spec-Kit 等方法試圖透過接管整個流程來幫你，但也會拿走你的控制權，讓流程裡的 bug 更難追查。

這些 skills 設計得小巧、容易調整，而且可以組合使用。它們適用於任何模型，來自多年工程經驗的萃取。你可以自由改造，讓它們成為自己的工具。

如果你想追蹤這些 skills 的更新，歡迎訂閱電子報，已有約 60,000 位開發者加入：

[訂閱電子報](https://www.aihero.dev/s/skills-newsletter)

## 快速開始（30 秒設定）

1. 執行 skills.sh 安裝工具：

```bash
npx skills@latest add mattpocock/skills
```

2. 選擇你要的 skills，以及想安裝到哪些 coding agent 上。**記得選取 `/setup-matt-pocock-skills`**。
3. 在你的 agent 中執行 `/setup-matt-pocock-skills`。它會詢問：
   - 你使用哪個 issue 追蹤工具（GitHub、Linear 或本地檔案）
   - 你在 triage 票據時套用哪些標籤（`/triage` 會使用）
   - 你想把產出的文件存在哪裡
4. 完成，可以開始使用。

## 以 Claude Code plugin 安裝

想要不用手動維護、即插即用的安裝方式？這些 skills 也以原生 [Claude Code plugin](https://code.claude.com/docs/en/plugins) 形式發布。跟複製可編輯檔案到你的 repo 不同，plugin 會把整組 skill 安裝成一個受管理的套件，隨我釋出新版本自動更新——你是訂閱，而不是 fork。

在 Claude Code 內：

```
/plugin marketplace add mattpocock/skills
/plugin install mattpocock-skills@mattpocock
```

或從你的 shell：

```bash
claude plugin marketplace add mattpocock/skills
claude plugin install mattpocock-skills@mattpocock
```

接著跟上面快速開始一樣，在每個 repo 執行一次 `/setup-matt-pocock-skills`。

兩種安裝方式，兩種哲學：

- **[skills.sh](https://skills.sh/mattpocock/skills)** 把 skills 複製進你的專案，讓你可以自行修改、變成自己的東西。
- **Plugin** 讓它們保持唯讀、隨時最新的套件，你不需要編輯——如果你只想讓我的這組 skills 能用、並隨著它演進，這是最佳選擇。

> 使用 Codex 或其他 agent？[skills.sh 安裝工具](https://skills.sh/mattpocock/skills)目前已經可以把這些 skills 安裝進 Codex 和其他符合 Agent-Skills 標準的 harness。原生 Codex plugin 已在規劃中——詳見 [`.agents/adr/0002-ship-as-a-claude-code-plugin.md`](./.agents/adr/0002-ship-as-a-claude-code-plugin.md)。

## 為什麼需要這些 Skills

我建立這些 skills，是為了解決我在 Claude Code、Codex 和其他 coding agent 上觀察到的常見失敗模式。

### 失敗模式 #1：Agent 沒做我要的東西

> 「沒有人確切知道自己想要什麼。」
>
> David Thomas & Andrew Hunt，[《The Pragmatic Programmer》](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**問題**：軟體開發中最常見的失敗模式是**對齊失敗**。你以為 dev 知道你要什麼，結果看到成品才發現完全不是那回事。

AI 時代也一樣。你和 agent 之間存在溝通落差。解法是一場**深度訪談（grilling session）**，讓 agent 主動問你大量細節問題。

**解法**：

- [`/grill-me`](./skills/productivity/grill-me/SKILL.zh.md)：適用於非程式碼場景
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.zh.md)：同上，但會額外建立文件

這是我最常用的兩個 skills。它們幫助你在動手前先和 agent 對齊，並深入思考要做的改動。每次要做改動前都應該用一次。

### 失敗模式 #2：Agent 說話太囉唆

> 在通用語言（ubiquitous language）下，開發者的對話和程式碼的表達都從同一個領域模型衍生出來。
>
> Eric Evans，[《Domain-Driven Design》](https://www.amazon.co.uk/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)

**問題**：一開始，開發者和領域專家通常說的是不同語言。Agent 被丟進一個專案，要自己摸索術語，結果用 20 個字說一個字就能講清楚的事。

**解法**：建立一套共同語言，也就是一份幫助 agent 解碼專案術語的文件。

<details>
<summary>範例</summary>

以下是 `course-video-manager` 的 [`CONTEXT.md`](https://github.com/mattpocock/course-video-manager/blob/076a5a7a182db0fe1e62971dd7a68bcadf010f1c/CONTEXT.md) 範例。哪一句比較容易讀？

- **之前**：「在課程某個章節裡的課程被『實體化』（也就是在檔案系統中分配位置）時出現問題」
- **之後**：「materialization cascade 有問題」

這種精確性在每次 session 都能持續節省 token。

</details>

這個功能內建在 [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.zh.md) 裡。它是一個 grilling session，同時幫你和 AI 建立共同語言，並以 ADR 格式記錄難以解釋的決策。

> [!TIP]
> 共同語言除了減少囉唆之外，還有許多好處：
>
> - **變數、函式和檔案命名一致**，都使用共同語言
> - **程式碼庫更容易被 agent 導覽**
> - Agent **思考時消耗更少 token**，因為有更精準的語言可用

### 失敗模式 #3：程式碼就是跑不起來

> 「永遠採取小而謹慎的步驟。反饋速率就是你的速度上限。永遠不要接下太大的任務。」
>
> David Thomas & Andrew Hunt，[《The Pragmatic Programmer》](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**問題**：假設你和 agent 已經對齊要建什麼，但 agent 產出的東西還是不能用。問題在於反饋迴路：沒有對程式碼實際執行情況的回饋，agent 就是在瞎飛。

**解法**：你需要完整的反饋迴路：靜態型別、瀏覽器存取、自動化測試。

對於自動化測試，紅綠重構迴路（red-green-refactor）至關重要。讓 agent 先寫一個失敗的測試，再修到通過。這能給 agent 穩定的反饋，大幅提升程式碼品質。

我建了 [`/tdd`](./skills/engineering/tdd/SKILL.zh.md) skill，可插入任何專案使用。它鼓勵紅綠重構，並給 agent 充分的測試好壞指引。

對於 debug，我也建了 [`/diagnosing-bugs`](./skills/engineering/diagnosing-bugs/SKILL.zh.md) skill，將最佳 debug 實踐包裝成一個簡單的迴路。

### 失敗模式 #4：我們蓋出了一堆爛泥巴

> 「每天都要投資系統的設計。」
>
> Kent Beck，[《Extreme Programming Explained》](https://www.amazon.co.uk/Extreme-Programming-Explained-Embrace-Change/dp/0321278658)

> 「最好的模組是深層的。它們讓大量功能可以透過簡單的介面存取。」
>
> John Ousterhout，[《A Philosophy Of Software Design》](https://www.amazon.co.uk/Philosophy-Software-Design-2nd/dp/173210221X)

**問題**：大多數用 agent 建出的應用程式都很複雜、難以修改。因為 agent 大幅加速了寫程式的速度，同時也加速了軟體熵的增長。程式碼庫以前所未有的速度變得越來越複雜。

**解法**：一個全新的 AI 開發方法：在意程式碼的設計。

這個理念貫穿這些 skills 的每一層：

- [`/to-spec`](./skills/engineering/to-spec/SKILL.zh.md) 在建立 spec 之前，會先問你要動哪些模組
- [`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.zh.md) 幫助你拯救已經變成爛泥巴的程式碼庫。我建議每隔幾天執行一次。

### 總結

軟體工程的基本功比以往任何時候都更重要。這些 skills 是我將基本功濃縮成可重複實踐的最佳嘗試，幫助你在職涯中交出更好的作品。

---

不確定該用哪個 skill？請看 [情境技能路由](./docs/scenario-routing.md)。

---

## 技能總覽

這些 skills 以一個軸線區分：**由誰呼叫**。**User-invoked（使用者呼叫）** 只有在你輸入時才會觸發（例如 `/grill-me`），任務是負責編排流程。**Model-invoked（模型呼叫）** 則可由你呼叫，也可在情境符合時由 agent 自動觸發，負責承載可重複使用的紀律。一個 user-invoked skill 可以呼叫 model-invoked skills，但不能呼叫另一個 user-invoked skill。

### Engineering（工程）

我每天寫程式時使用的 skills。

**User-invoked（使用者呼叫）**

- **[ask-matt](./skills/engineering/ask-matt/SKILL.zh.md)**：詢問目前情境該用哪個 skill 或流程。一個涵蓋本 repo 所有 user-invoked skills 的路由器。
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.zh.md)**：深度訪談 session，同時建立專案的領域模型，精煉術語並同步更新 `CONTEXT.md` 和 ADR。
- **[triage](./skills/engineering/triage/SKILL.zh.md)**：透過狀態機角色流程，對 issue 進行分類、驗證、必要時追問，並寫出 agent 可執行的 brief。
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.zh.md)**：掃描程式碼庫尋找深化機會，以視覺化 HTML 報告呈現，再針對你選的項目深度訪談。
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.zh.md)**：為本 repo 設定 engineering skills：issue 追蹤工具、triage 標籤和領域文件結構。使用其他 engineering skills 前，每個 repo 執行一次。
- **[to-spec](./skills/engineering/to-spec/SKILL.zh.md)**：將目前對話轉換成 spec 並提交至 issue 追蹤工具。不做訪談，直接綜合你已討論的內容。
- **[to-tickets](./skills/engineering/to-tickets/SKILL.zh.md)**：將任何計畫、spec 或對話拆成一組 tracer-bullet tickets，每張都宣告自己的 blocking edges——本地檔案用文字表示，真正的 tracker 則用 native blocking links。
- **[implement](./skills/engineering/implement/SKILL.zh.md)**：根據 spec 或一組 tickets 建置工作，在預先商定的接縫處驅動 `/tdd`，commit 前用 `/code-review` 收尾。
- **[wayfinder](./skills/engineering/wayfinder/SKILL.zh.md)**：規劃一大塊工作——大到超過一個 agent session 能裝下——把它變成 issue 追蹤工具上一張共享的調查 ticket 地圖，逐一解決，直到通往目的地的路徑清楚。

**Model-invoked（模型呼叫）**

- **[prototype](./skills/engineering/prototype/SKILL.zh.md)**：建立可拋棄的原型來釐清設計問題——可用於狀態／邏輯問題的可執行終端機應用程式，或同一路由下可切換的多個截然不同的 UI 版本。
- **[diagnosing-bugs](./skills/engineering/diagnosing-bugs/SKILL.zh.md)**：針對難搞 bug 和效能退化的紀律化診斷迴路：重現 → 最小化 → 假設 → 量測 → 修復 → 回歸測試。
- **[research](./skills/engineering/research/SKILL.zh.md)**：針對高信度的第一手資料調查問題，並在 repo 中留下一份有引用的 Markdown 檔案，以背景 agent 執行。
- **[tdd](./skills/engineering/tdd/SKILL.zh.md)**：測試驅動開發，採紅綠重構迴路。每次一個垂直切片地實作功能或修 bug。
- **[domain-modeling](./skills/engineering/domain-modeling/SKILL.zh.md)**：主動建立並精煉專案的領域模型，挑戰術語、壓力測試邊界案例，並同步更新 `CONTEXT.md` 和 ADR。
- **[codebase-design](./skills/engineering/codebase-design/SKILL.zh.md)**：設計深層模組的共享紀律與詞彙：用小介面承載大量行為，放在乾淨的接縫處，並可透過介面測試。
- **[code-review](./skills/engineering/code-review/SKILL.zh.md)**：對自固定基準點以來的 diff 做兩軸審查：**Standards**（是否遵循 repo 的程式碼標準，加上 Fowler smell baseline？）和 **Spec**（是否忠實實作了原始 issue／PRD？），以平行 sub-agents 執行，避免互相污染。
- **[resolving-merge-conflicts](./skills/engineering/resolving-merge-conflicts/SKILL.zh.md)**：逐個 hunk 處理進行中的 git merge 或 rebase 衝突，依循每一方的主要來源追溯意圖來解決，完成後結束該操作——絕不使用 `--abort`。

### Productivity（生產力）

通用工作流程工具，不限於寫程式。

**User-invoked（使用者呼叫）**

- **[grill-me](./skills/productivity/grill-me/SKILL.zh.md)**：針對計畫或設計進行無情訪談，直到每個決策分支都解決為止。
- **[handoff](./skills/productivity/handoff/SKILL.zh.md)**：將目前對話壓縮成交接文件，讓另一個 agent 可以接續工作。
- **[teach](./skills/productivity/teach/SKILL.zh.md)**：在多個 session 中教導使用者新技能或概念，以目前目錄作為有狀態的教學工作區。
- **[writing-great-skills](./skills/productivity/writing-great-skills/SKILL.zh.md)**：寫好與編輯 skill 的參考，說明讓 skill 行為可預測的詞彙與原則。

**Model-invoked（模型呼叫）**

- **[grilling](./skills/productivity/grilling/SKILL.zh.md)**：針對計畫、決策或想法進行無情訪談，直到每個決策分支都解決。這是 `grill-me` 與 `grill-with-docs` 背後可重複使用的迴路。
