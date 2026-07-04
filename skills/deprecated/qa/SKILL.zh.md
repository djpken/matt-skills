---
name: qa
description: 互動式 QA session，使用者用對話回報 bugs 或 issues，agent 建立 GitHub issues。背景探索 codebase 以取得 context 和 domain language。用於使用者想回報 bugs、做 QA、對話式開 issue，或提到「QA session」時。
---

# QA Session

執行互動式 QA session。使用者描述遇到的問題。你釐清問題、探索 codebase 取得 context，並建立耐久、使用者導向、使用專案 domain language 的 GitHub issues。

## For each issue the user raises

### 1. Listen and lightly clarify

讓使用者用自己的話描述問題。最多問 **2-3 個短釐清問題**，聚焦：

- 他們預期什麼、實際發生什麼
- 重現步驟（如果不明顯）
- 是否穩定發生或間歇發生

不要過度訪談。如果描述已足以開 issue，就繼續。

### 2. Explore the codebase in the background

與使用者對話時，在背景啟動 Agent（subagent_type=Explore）理解相關區域。目標不是找 fix，而是：

- 學習該區域使用的 domain language（檢查 `UBIQUITOUS_LANGUAGE.md`）
- 理解 feature 應該做什麼
- 識別 user-facing behavior boundary

這些 context 幫你寫出更好的 issue，但 issue 本身不應引用特定 files、line numbers 或 internal implementation details。

### 3. Assess scope: single issue or breakdown?

建立 issue 前，判斷這是**單一 issue**，還是需要**拆成多個 issues**。

需要拆分的情況：

- 修復跨越多個獨立區域（例如 form validation 錯、success message 缺失、redirect 壞掉）
- 有明確可分離的 concerns，可由不同人平行處理
- 使用者描述了多個 distinct failure modes 或 symptoms

保持單一 issue 的情況：

- 是同一個地方的一個錯誤 behavior
- symptoms 都源自同一個 root behavior

### 4. File the GitHub issue(s)

使用 `gh issue create` 建立 issues。不要先要求使用者 review；直接建立並分享 URLs。

Issues 必須**耐久**，即使大幅 refactor 後仍然有意義。從使用者角度撰寫。

#### For a single issue

使用此 template：

```markdown
## What happened

[Describe the actual behavior the user experienced, in plain language]

## What I expected

[Describe the expected behavior]

## Steps to reproduce

1. [Concrete, numbered steps a developer can follow]
2. [Use domain terms from the codebase, not internal module names]
3. [Include relevant inputs, flags, or configuration]

## Additional context

[Any extra observations from the user or from codebase exploration that help frame the issue — e.g. "this only happens when using the Docker layer, not the filesystem layer" — use domain language but don't cite files]
```

#### For a breakdown (multiple issues)

依 dependency order 建立 issues（blockers first），這樣可以引用真實 issue numbers。

每個 sub-issue 使用此 template：

```markdown
## Parent issue

#<parent-issue-number> (if you created a tracking issue) or "Reported during QA session"

## What's wrong

[Describe this specific behavior problem — just this slice, not the whole report]

## What I expected

[Expected behavior for this specific slice]

## Steps to reproduce

1. [Steps specific to THIS issue]

## Blocked by

- #<issue-number> (if this issue can't be fixed until another is resolved)

Or "None — can start immediately" if no blockers.

## Additional context

[Any extra observations relevant to this slice]
```

拆分時：

- **Prefer many thin issues over few thick ones**：每個都應可獨立修復和驗證
- **Mark blocking relationships honestly**：如果 B 真的要等 A 才能測，說清楚；如果獨立，就標 "None — can start immediately"
- **Create issues in dependency order**，才能在 "Blocked by" 引用真實 issue numbers
- **Maximize parallelism**：目標是多人或多 agents 可同時認領不同 issues

#### Rules for all issue bodies

- **No file paths or line numbers**：它們會過期
- **Use the project's domain language**（如果存在，檢查 `UBIQUITOUS_LANGUAGE.md`）
- **Describe behaviors, not code**：寫 "the sync service fails to apply the patch"，不要寫 "applyPatch() throws on line 42"
- **Reproduction steps are mandatory**：如果無法判定，就問使用者
- **Keep it concise**：developer 應能在 30 秒內讀完 issue

建立後，印出所有 issue URLs（並摘要 blocking relationships），然後問：「Next issue, or are we done?」

### 5. Continue the session

持續進行直到使用者說完成。每個 issue 都獨立處理，不要 batch。
