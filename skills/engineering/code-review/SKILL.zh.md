---
name: code-review
description: 從固定點（commit、branch、tag 或 merge-base）以兩個軸線 review 變更：Standards（程式碼是否遵守 repo 文件化標準）與 Spec（是否符合來源 issue/spec）。並行執行兩個 sub-agents，並並列回報。用於 review branch、PR、WIP 變更，或使用者要求「review since X」時。
---

對使用者提供的固定點與 `HEAD` 之間的 diff 做雙軸 review：

- **Standards**：程式碼是否符合 repo 文件化的 coding standards？
- **Spec**：程式碼是否忠實實作來源 issue / spec？

兩個軸線用**並行 sub-agents**執行，避免污染彼此 context，然後由這個 skill 彙整 findings。

Issue tracker 應該已提供；如果缺少 `docs/agents/issue-tracker.md`，先執行 `/setup-matt-pocock-skills`。

## Process

### 1. Pin fixed point

使用者應提供固定點：commit SHA、branch name、tag、`main`、`HEAD~5` 等。如果沒有指定，就詢問。

先記下 diff 指令：`git diff <fixed-point>...HEAD`（三點，所以比較基準是 merge-base）。也用 `git log <fixed-point>..HEAD --oneline` 記下 commits 清單。

繼續前，確認固定點可解析（`git rev-parse <fixed-point>`）且 diff 非空。壞 ref 或空 diff 應在這裡失敗，不要丟給兩個並行 sub-agents。

### 2. Identify spec source

依序尋找來源 spec：

1. Commit messages 中的 issue references（`#123`、`Closes #45`、GitLab `!67` 等）：依 `docs/agents/issue-tracker.md` 的 workflow 取得。
2. 使用者以 argument 傳入的 path。
3. `docs/`、`specs/` 或 `.scratch/` 下符合 branch name / feature 的 spec file。
4. 如果找不到，問使用者 spec 在哪裡。如果使用者說沒有，**Spec** sub-agent 會跳過並回報「no spec available」。

### 3. Identify standards sources

找 repo 中任何描述程式碼應如何撰寫的文件，例如 `CODING_STANDARDS.md` 或 `CONTRIBUTING.md`。

除了 repo 自己文件化的標準外，Standards 軸永遠帶著下面的 **smell baseline**：一組固定的 Fowler code smells（_Refactoring_ 第 3 章），即使 repo 沒有任何標準文件也適用。兩條規則約束它：

- **Repo overrides.** 文件化的 repo standard 永遠優先；如果 repo standard 認可 baseline 會標記的東西，壓下該 smell。
- **Always a judgement call.** 每個 smell 都是標記過的 heuristic（例如 "possible Feature Envy"），永遠不是 hard violation；而且像這裡任何 standard 一樣，跳過 tooling 已經強制的東西。

每個 smell 都是「它是什麼」→「如何修」；對照 diff：

- **Mysterious Name**：function、variable 或 type 的名稱沒有揭示它做什麼或持有什麼。→ rename；如果找不到誠實的名字，design 可能不清楚。
- **Duplicated Code**：同樣的 logic shape 出現在變更中的多個 hunk 或 file。→ extract shared shape，兩邊都呼叫它。
- **Feature Envy**：method 伸手進另一個 object 的 data，比使用自己的 data 還多。→ 把 method 移到它羨慕的 data 上。
- **Data Clumps**：同幾個 fields 或 params 總是一起旅行（想出生的 type）。→ 包成一個 type，傳那個 type。
- **Primitive Obsession**：primitive 或 string 代表一個值得有自己 type 的 domain concept。→ 給該 concept 一個小 type。
- **Repeated Switches**：同一個 type 上的相同 `switch`/`if` cascade 在變更中重複出現。→ 改用 polymorphism，或用一個兩處共享的 map。
- **Shotgun Surgery**：一個 logical change 迫使 diff 中多個 files 分散修改。→ 把一起變的東西收進同一個 module。
- **Divergent Change**：一個 file 或 module 因多個不相關理由被修改。→ 拆開，讓每個 module 只因一個理由改變。
- **Speculative Generality**：為 spec 沒有的需求加入 abstraction、parameters 或 hooks。→ 刪掉它；inline 回去，直到有真實需求。
- **Message Chains**：caller 不該依賴的長串 `a.b().c().d()` navigation。→ 把 traversal 藏到第一個 object 的一個 method 後面。
- **Middle Man**：class 或 function 大多只是往下 delegate。→ 切掉它，直接呼叫真正目標。
- **Refused Bequest**：subclass 或 implementer 忽略或 override 大部分繼承來的東西。→ 放棄 inheritance，使用 composition。

### 4. Spawn both sub-agents in parallel

用單一訊息送出兩個 `Agent` tool calls。兩個都使用 `general-purpose` subagent。

**Standards sub-agent prompt** 應包含：

- 完整 diff command 和 commit list。
- 步驟 3 找到的 standards-source files 清單，**加上步驟 3 的 smell baseline 全文**；sub-agent 沒有其他方式取得它。
- brief：「Report — per file/hunk relevant — (a) place diff violates documented standard: cite standard (file + rule); (b) baseline smell you spot: name quote hunk. Distinguish hard violations judgement calls — documented-standard breaches can be hard, but baseline smells always judgement calls, documented repo standard overrides baseline. Skip anything tooling enforces. Under 400 words.」

**Spec sub-agent prompt** 應包含：

- diff command 和 commit list。
- spec path 或取得的 spec contents。
- brief：「Report: (a) requirements spec asked missing partial; (b) behaviour in diff wasn't asked (scope creep); (c) requirements look implemented but where implementation looks wrong. Quote spec line finding. Under 400 words.」

如果 spec 缺失，跳過 Spec sub-agent，並在 final report 註明。

### 5. Aggregate

在 `## Standards` 和 `## Spec` headings 下呈現兩份 reports，保留原意，只做輕微清理。**不要**合併或重新排序 findings：兩個軸線刻意分開（見 _Why two axes_）。

最後用一行 summary 結束：每個軸線的 finding 數量，以及各軸線中最嚴重的問題（如果有）。不要跨軸選單一最嚴重項目；分軸就是為了避免這種重新排序。

## Why two axes

一個變更可能通過某一軸、但在另一軸失敗：

- 程式碼符合每個 standard，但實作錯東西 → **Standards pass, Spec fail.**
- 程式碼完全符合 issue 要求，但破壞專案慣例 → **Spec pass, Standards fail.**

分開回報能避免其中一軸掩蓋另一軸。
