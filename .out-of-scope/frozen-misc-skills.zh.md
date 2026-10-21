<a id="changes-to-misc-skills"></a>

# `misc/` skills 的變更

`skills/misc/` 下的 skills（`git-guardrails-claude-code`、`migrate-to-shoehorn`、`scaffold-exercises`、`setup-pre-commit`，以及任何後續加入此處的專案）已凍結。針對這些 skills 的 bug 報告、功能請求和 PR 將被關閉。

<a id="why-this-is-out-of-scope"></a>

## 為何此範圍外

`misc/` 是用來存放我保留但很少使用的 skills 的地方。這些 skills 不會被提升：它們不會隨 Claude Code plugin 發布，沒有檔案頁面，我也沒有在日常工作中使用它們，因此我無法判斷修復是否正確，或在某個修復導致其他問題時察覺到。若要將這些 skills 維護到與已提升 skills 相同的標準，其成本與已提升 skill 相同，但使用頻率卻低得多。

它們仍保留在儲存庫中，但不再維護，因為對某些人來說，現狀仍然可用。如果某個 skill 對您不起作用，請使用 skills.sh 安裝它並修復您的副本：這些檔案由您自行編輯。

如果某個 `misc/` skill 日後被重新提升回 `engineering/` 或 `productivity/`，則從該時點起重新納入範圍。

<a id="prior-requests"></a>

## 先前請求

- #14：「setup-pre-commit/SKILL.md 中可能的拼字錯誤」
- #301：「`block-dangerous-git.sh` 可被任何 git 全域選項（例如 `git -C <dir> push`）繞過」
- #465：「git-guardrails-claude-code 檔案缺少相對於 Claude Code 內建 deny 規則的優勢說明」
- #898：「git-guardrails-claude-code：hook 在沒有 jq 時會失敗並放過，且遺漏常見的 git 拼寫」
