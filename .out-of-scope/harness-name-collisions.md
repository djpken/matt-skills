<a id="renaming-skills-to-dodge-harness-built-in-commands"></a>

# 重新命名 skill 以規避 harness 內建命令

不會因為 harness 隨附了同名（`/prototype`、`/research`、`/code-review` 等）的內建命令或 skill 而變更 skill 名稱。要求重新命名 skill 或新增別名以避免衝突的請求不在處理範圍內。

<a id="why-this-is-out-of-scope"></a>

## 為何此請求不在處理範圍內

Harness 經常新增內建功能，且每個 harness 自行決定命名。若每次有 harness 使用我們已佔用的名稱時就重新命名，將意味著要在 Claude Code、Copilot、Devin 及其他工具之間追趕不斷變化的目標，並每次破壞所有使用者的肌肉記憶以及 skill 之間的交叉引用。

Harness 如何解析兩個同名專案屬於 harness 的行為，而非 skill 的行為。無論如何，skill 文字都是正確的。

解決方式是使用名稱空間呼叫。Claude Code plugin 會在其 plugin 名稱下公開每個 skill，因此您隨時可以明確地存取我們的 skill：

```
/mattpocock-skills:research
/mattpocock-skills:code-review
```

如果您使用 skills.sh 安裝，這些檔案屬於您：請重新命名資料夾以及您副本中的 `name:` 欄位。

如果某個 harness 完全沒有提供方式讓您存取具名稱空間或已重新命名的 skill，請向該 harness 回報此問題。

<a id="prior-requests"></a>

## 先前請求

- #423：「/code-review 遮蔽了 Claude Code 自己的 review skill」
- #483：「/code-review 與 Claude Code 內建功能名稱衝突」
- #857：「重新命名 research skill 以避免與 Copilot 內建 /research 命令衝突」
- #1019：「簡寫 `/prototype` 與 Claude Code 新的隱藏內建 skill `/prototype` 衝突」
- #1037：「Skill 名稱與 devin cli 內建命令重疊」
