<a id="scope"></a>

# 範圍

此儲存庫是我每天使用的 skill 集合。它經過精心挑選：歡迎提出想法，每個想法都會根據以下標準進行評估。Issues 用於追蹤 skill 的變更。問題與討論請至 [GitHub Discussions](https://github.com/mattpocock/skills/discussions)。

<a id="the-bar"></a>

## 標準

一個想法或回饋 issue 只有在同時滿足以下**兩部分**時才會保持開啟：

1. **觀察到的失敗。** 它描述了真實 session 中發生的錯誤：你執行了什麼、skill 做了什麼、你預期什麼。假設性的改進（「如果……會更好」）無法滿足此部分。
2. **符合哲學。** 它不與 [`.out-of-scope/`](./.out-of-scope/) 或過去被拒絕的內容相符，也不是設定選項、特定 harness 的分支，或為了迎合個人工作流程而做的調整。這些屬於你自己的 `CLAUDE.md` / `AGENTS.md`，或屬於 fork（skills.sh 會提供可編輯的副本）。

規模無關緊要：一個字的修正與重寫面臨相同的標準。反應與 +1 留言不計入其中。

<a id="by-bucket"></a>

## 依分類

- **`engineering/`, `productivity/`, `in-progress/`**：上述標準。
- **`misc/`**：已凍結且不再維護。所有 issue 均已關閉。請參閱 [`frozen-misc-skills.md`](./.out-of-scope/frozen-misc-skills.md)。
- **新 skill**：提案與貢獻已關閉。如果行為可以由現有 skill 組合而成，則不會建立新的 skill。請參閱 [`new-skills.md`](./.out-of-scope/new-skills.md)。

<a id="already-decided"></a>

## 已決定

[`.out-of-scope/`](./.out-of-scope/) 中的每個檔案記錄了一個被拒絕的概念及其原因。提交前請先閱讀：

- [`frozen-misc-skills.md`](./.out-of-scope/frozen-misc-skills.md)
- [`harness-name-collisions.md`](./.out-of-scope/harness-name-collisions.md)
- [`mainstream-issue-trackers-only.md`](./.out-of-scope/mainstream-issue-trackers-only.md)
- [`native-question-tool.md`](./.out-of-scope/native-question-tool.md)
- [`new-skills.md`](./.out-of-scope/new-skills.md)
- [`question-limits.md`](./.out-of-scope/question-limits.md)
- [`setup-skill-verify-mode.md`](./.out-of-scope/setup-skill-verify-mode.md)
- [`subagent-recursion.md`](./.out-of-scope/subagent-recursion.md)

<a id="unclear-issues"></a>

## 不清楚的 issue

無法根據標準判斷的 issue 會獲得一輪提問並加上 `needs-info` 標籤。若報告者在 14 天內未回覆，該 issue 將被關閉。拒絕的 issue 將以「不計畫處理」關閉。
