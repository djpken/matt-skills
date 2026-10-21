<a id="guards-against-sub-agents-recursing-into-skills"></a>

# 防止子代理遞迴進入 skills

Skills 不會新增防止子代理遞迴呼叫 skills 或產生巢狀代理的保護機制（例如 `/code-review` 子代理重新呼叫 `/code-review`，或 `/research` 代理產生另一個代理）。要求新增葉節代理指令、深度限制或「不要呼叫此 skill」等字串的請求不在範圍內。

<a id="why-this-is-out-of-scope"></a>

## 為何此事項不在範圍內

harness 應負責停止無限的子代理迴圈，而非 skills。harness 決定子代理可以存取哪些工具和 skills，以及巢狀深度可以達到多深，因此在該處設定的限制可一次適用於所有 skills。寫入單一 skill 文字中的保護機制僅涵蓋該 skill，每次執行都會消耗 tokens，且仍依賴模型選擇服從該指令。

如果子代理無限制地遞迴或擴散，請向 harness 回報。

<a id="prior-requests"></a>

## 先前請求

- #530: 「research skill: 產生的背景代理可遞迴地重新產生另一個代理（不受控制的巢狀結構）」
- #573: 「code-review 子代理遞迴呼叫 /code-review 並擴散」（PR #1186，「code-review: 將 Standards 和 Spec 子代理設為葉節審查者」，已關閉）
