---
name: triage
description: 透過 triage 角色的狀態機移動 issues 和外部 PR——分類、驗證、在需要時深度訪談，並撰寫 agent 就緒的簡報。
disable-model-invocation: true
---

# Triage（分類）

透過一個小型 triage 角色狀態機移動專案 issue 追蹤器上的 issues。

如果這個 repo 將外部 pull requests 視為請求表面（見 issue 追蹤器設定），triage 也涵蓋它們：**PR 是帶有附加程式碼的 issue**——相同的角色、相同的狀態、相同的機器，在下面標記「對於 PR」的幾個差異。將裸露的 `#42` 按追蹤器設定解析為 issue 或 PR。

在 triage 期間發布到 issue 追蹤器的每條評論或 issue **必須**以這個免責聲明開頭：

```
> *這是由 AI 在 triage 期間生成的。*
```

## 參考文件

- [AGENT-BRIEF.md](AGENT-BRIEF.md) — 如何撰寫持久的 agent 簡報
- [OUT-OF-SCOPE.md](OUT-OF-SCOPE.md) — `.out-of-scope/` 知識庫的運作方式

## 角色

兩個**分類**角色：

- `bug` — 某些東西壞了
- `enhancement` — 新功能或改善

五個**狀態**角色：

- `needs-triage` — 維護者需要評估
- `needs-info` — 等待報告者提供更多資訊
- `ready-for-agent` — 完全指定，準備好給 AFK agent
- `ready-for-human` — 需要人類實作
- `wontfix` — 不會被處理

對於 PR，相同的狀態針對附加的程式碼解讀：`ready-for-agent` 意味著附上了簡報，agent 應該對 diff 採取下一步；`ready-for-human` 意味著準備好讓人類合併。

每個已 triage 的 issue 應該恰好攜帶一個分類角色和一個狀態角色。如果狀態角色衝突，標記並在做任何其他事情之前詢問維護者。

這些是規範角色名稱——issue 追蹤器中使用的實際標籤字串可能不同。映射應該已提供給你——如果沒有，執行 `/setup-matt-pocock-skills`。

狀態轉換：未標記的 issue 通常先進入 `needs-triage`；從那裡它移到 `needs-info`、`ready-for-agent`、`ready-for-human` 或 `wontfix`。一旦報告者回覆，`needs-info` 返回 `needs-triage`。維護者可以隨時覆蓋——標記看起來不尋常的轉換，並在繼續之前詢問。

## 呼叫

維護者呼叫 `/triage` 並用自然語言描述他們想要什麼。解釋請求並行動。範例：

- 「給我看任何需要我注意的東西」
- 「我們來看看 #42」（issue 或 PR）
- 「把 #42 移到 ready-for-agent」
- 「什麼準備好給 agent 接手了？」

## 顯示需要注意的內容

查詢 issue 追蹤器並呈現三個桶，最舊的先：

1. **未標記** — 從未被 triage。
2. **`needs-triage`** — 評估進行中。
3. **`needs-info` 且報告者在最後一次 triage 記錄後有活動** — 需要重新評估。

當 PR 在範圍內時，在這些桶中包含外部 PR，並給每行標記 `[PR]` 或 `[issue]`。發現只浮出*外部* PR（追蹤器設定定義誰算外部）——協作者的進行中 PR 不是 triage 工作。此過濾器僅用於發現；明確命名的 PR 無論作者是誰都要 triage。

顯示計數和每個項目的一行摘要。讓維護者挑選。

## 對特定 issue 或 PR 進行 Triage

1. **收集背景。** 讀取完整的 issue 或 PR（正文、評論、標籤、作者、日期；對 PR 也讀 diff）。解析任何先前的 triage 記錄，這樣你就不會重新詢問已解決的問題。使用專案的領域術語表探索程式碼庫，遵循該區域的 ADR。對程式碼庫執行兩項檢查：(a) **冗餘** — 透過領域概念（不只是請求的措辭）搜尋請求行為的現有實作，並報告你在哪裡查找。如果找到，它是一個已實作的 `wontfix`（步驟 5）。(b) **先前拒絕** — 讀取 `.out-of-scope/*.md` 並浮出任何類似此請求的內容。

2. **建議。** 告訴維護者你的分類和狀態建議及理由，加上與請求相關的簡短程式碼庫摘要——包括它是否已實作。等待指示。

3. **驗證聲明。** 在任何深度訪談之前，確認聲明成立。對於 bug，從報告者的步驟重現它。對於 PR，確認 diff 做了它聲稱的——查看它、執行相關測試或指令。報告發生了什麼：確認（帶程式碼路徑）、失敗，或細節不足（強烈的 `needs-info` 訊號）。確認的驗證使 agent 簡報更強。

4. **深度訪談（如果需要）。** 如果請求需要補充完善，一起執行 `/grilling` 和 `/domain-modeling` skills——一次一個問題地將其訪談成形，同時在決策落定時磨練領域術語並即時更新 `CONTEXT.md`/ADR。

5. **應用結果：**
   - `ready-for-agent` — 發布 agent 簡報評論（[AGENT-BRIEF.md](AGENT-BRIEF.md)）。
   - `ready-for-human` — 與 agent 簡報相同的結構，但注明為什麼它不能被委派（判斷呼叫、外部存取、設計決策、手動測試）。
   - `needs-info` — 發布 triage 記錄（下方模板）。
   - `wontfix` — 關閉，評論根據*原因*而異：
     - **已實作** — 變更已存在於程式碼庫中。指出它在哪裡；**不要**寫到 `.out-of-scope/`（那個 KB 是給*拒絕*的請求，不是已建立的）。
     - **拒絕（bug）** — 禮貌的解釋，然後關閉。
     - **拒絕（enhancement）** — 寫到 `.out-of-scope/`，從評論連結到它，然後關閉（[OUT-OF-SCOPE.md](OUT-OF-SCOPE.md)）。
   - `needs-triage` — 應用角色。如果有部分進展，可選評論。

## 快速狀態覆蓋

如果維護者說「把 #42 移到 ready-for-agent」，信任他們並直接應用角色。確認你要做什麼（角色變更、評論、關閉），然後行動。跳過深度訪談。如果在沒有深度訪談的情況下移到 `ready-for-agent`，詢問他們是否想要寫一個 agent 簡報。

## Needs-info 模板

```markdown
## Triage 記錄

**我們到目前為止建立的：**

- 要點 1
- 要點 2

**我們還需要你提供的（@reporter）：**

- 問題 1
- 問題 2
```

在「到目前為止建立的」下捕獲在深度訪談中解決的所有內容，這樣工作就不會丟失。問題必須是具體且可操作的，而不是「請提供更多資訊」。

## 恢復先前的 session

如果 issue 或 PR 上存在先前的 triage 記錄，讀取它們，檢查報告者是否回答了任何未解決的問題，並在繼續之前呈現更新的圖景。不要重新詢問已解決的問題。
