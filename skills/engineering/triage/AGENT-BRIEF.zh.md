<a id="writing-agent-briefs"></a>

# 撰寫 Agent 摘要

Agent 摘要是一則結構化留言，當 GitHub issue 或 PR 移至 `ready-for-agent` 時發布。這是 AFK agent 將據以工作的權威規格說明。原始內容與討論僅供參考：Agent 摘要才是合約。

摘要說明 **agent 應該做什麼**，這涵蓋兩個層面：對於 issue，是指從零開始建置變更；對於 PR，則是指對 *現有 diff* 尚待完成的工作：完成它、補足缺口、處理審查意見。兩者遵循相同原則；下方的 PR 範例展示了差異。

<a id="principles"></a>

## 原則

<a id="durability-over-precision"></a>

### 永續性優先於精確性

Issue 可能在 `ready-for-agent` 停留數天或數週。期間程式碼庫會發生變化。撰寫摘要時，應確保即使檔案被重新命名、移動或重構，它仍然有用。

- **應該** 描述介面、型別與行為合約
- **應該** 指名 agent 應尋找或修改的特定型別、函式簽章或設定結構
- **不應該** 參考檔案路徑：它們會過時
- **不應該** 參考行號
- **不應該** 假設目前的實作結構會保持不變

<a id="behavioral-not-procedural"></a>

### 著重行為，而非程式

描述系統**應該做什麼**，而不是**如何**實作。Agent 會重新探索程式碼庫並自行做出實作決策。

- **良好範例：** "`SkillConfig` 型別應接受一個選用的 `schedule` 欄位，其型別為 `CronExpression`"
- **不良範例：** "開啟 src/types/skill.ts 並在第 42 行新增 schedule 欄位"
- **良好範例：** "當使用者執行 `/triage` 且未提供任何引數時，應顯示需要關注的 issue 摘要"
- **不良範例：** "在主處理函式中新增一個 switch 陳述式"

<a id="complete-acceptance-criteria"></a>

### 完整的驗收標準

Agent 需要知道何時算完成。每個 Agent 摘要都必須具備具體且可測試的驗收標準。每項標準都應可獨立驗證。

- **良好範例：** "執行 `gh issue list --label needs-triage` 會回傳已透過初步分類的 issue"
- **不良範例：** "分類功能應正常運作"

<a id="explicit-scope-boundaries"></a>

### 明確的範圍邊界

說明哪些內容不在範圍內。這可防止 agent 過度設計或對相鄰功能做出假設。

<a id="template"></a>

## 範本

```markdown
## Agent Brief

**Category:** bug / enhancement
**Summary:** one-line description of what needs to happen

**Current behavior:**
Describe what happens now. For bugs, this is the broken behavior.
For enhancements, this is the status quo the feature builds on.

**Desired behavior:**
Describe what should happen after the agent's work is complete.
Be specific about edge cases and error conditions.

**Key interfaces:**
- `TypeName`: what needs to change and why
- `functionName()` return type: what it currently returns vs what it should return
- Config shape: any new configuration options needed

**Acceptance criteria:**
- [ ] Specific, testable criterion 1
- [ ] Specific, testable criterion 2
- [ ] Specific, testable criterion 3

**Out of scope:**
- Thing that should NOT be changed or addressed in this issue
- Adjacent feature that might seem related but is separate
```

<a id="examples"></a>

## 範例

<a id="good-agent-brief-bug"></a>

### 良好的 Agent 摘要（Bug）

```markdown
## Agent Brief

**Category:** bug
**Summary:** Skill description truncation drops mid-word, producing broken output

**Current behavior:**
When a skill description exceeds 1024 characters, it is truncated at exactly
1024 characters regardless of word boundaries. This produces descriptions
that end mid-word (e.g. "Use when the user wants to confi").

**Desired behavior:**
Truncation should break at the last word boundary before 1024 characters
and append "..." to indicate truncation.

**Key interfaces:**
- The `SkillMetadata` type's `description` field: no type change needed,
  but the validation/processing logic that populates it needs to respect
  word boundaries
- Any function that reads SKILL.md frontmatter and extracts the description

**Acceptance criteria:**
- [ ] Descriptions under 1024 chars are unchanged
- [ ] Descriptions over 1024 chars are truncated at the last word boundary
      before 1024 chars
- [ ] Truncated descriptions end with "..."
- [ ] The total length including "..." does not exceed 1024 chars

**Out of scope:**
- Changing the 1024 char limit itself
- Multi-line description support
```

<a id="good-agent-brief-enhancement"></a>

### 良好的 Agent 摘要（增強功能）

```markdown
## Agent Brief

**Category:** enhancement
**Summary:** Add `.out-of-scope/` directory support for tracking rejected feature requests

**Current behavior:**
When a feature request is rejected, the issue is closed with a `wontfix` label
and a comment. There is no persistent record of the decision or reasoning.
Future similar requests require the maintainer to recall or search for the
prior discussion.

**Desired behavior:**
Rejected feature requests should be documented in `.out-of-scope/<concept>.md`
files that capture the decision, reasoning, and links to all issues that
requested the feature. When triaging new issues, these files should be
checked for matches.

**Key interfaces:**
- Markdown file format in `.out-of-scope/`: each file should have a
  `# Concept Name` heading, a `**Decision:**` line, a `**Reason:**` line,
  and a `**Prior requests:**` list with issue links
- The triage workflow should read all `.out-of-scope/*.md` files early
  and match incoming issues against them by concept similarity

**Acceptance criteria:**
- [ ] Closing a feature as wontfix creates/updates a file in `.out-of-scope/`
- [ ] The file includes the decision, reasoning, and link to the closed issue
- [ ] If a matching `.out-of-scope/` file already exists, the new issue is
      appended to its "Prior requests" list rather than creating a duplicate
- [ ] During triage, existing `.out-of-scope/` files are checked and surfaced
      when a new issue matches a prior rejection

**Out of scope:**
- Automated matching (human confirms the match)
- Reopening previously rejected features
- Bug reports (only enhancement rejections go to `.out-of-scope/`)
```

<a id="good-agent-brief-pr"></a>

### 良好的 Agent 摘要（PR）

對於 PR，"目前行為" 描述的是 diff 的狀態，摘要要求 agent 完成或修復它，而非從零開始建置。

```markdown
## Agent Brief

**Category:** enhancement
**Summary:** Finish the contributor's `--json` output flag for `triage list`

**Current behavior:**
The PR adds a `--json` flag that serializes the issue list to JSON. The happy
path works and the diff matches the project's command structure. Two gaps
remain: errors are still printed as human text (not JSON), and the new flag has
no test coverage.

**Desired behavior:**
With `--json`, all output (including errors) is well-formed JSON on stdout,
and the command's exit codes are unchanged. The existing human-readable output
is untouched when the flag is absent.

**Key interfaces:**
- The command's error path should emit `{ "error": string }` under `--json`
  instead of the plain-text error
- Reuse the existing serializer the PR already added; don't introduce a second

**Acceptance criteria:**
- [ ] `triage list --json` emits valid JSON for both success and error cases
- [ ] Exit codes match the non-JSON command
- [ ] A test covers the `--json` success output and one error case
- [ ] Default (non-JSON) output is byte-for-byte unchanged

**Out of scope:**
- Adding `--json` to any other command
- Changing the JSON shape of the success payload the PR already defined
```

<a id="bad-agent-brief"></a>

### 不良的 Agent 摘要

```markdown
## Agent Brief

**Summary:** Fix the triage bug

**What to do:**
The triage thing is broken. Look at the main file and fix it.
The function around line 150 has the issue.

**Files to change:**
- src/triage/handler.ts (line 150)
- src/types.ts (line 42)
```

這很糟糕，因為：
- 沒有分類
- 描述模糊（"分類功能壞掉了"）
- 參考了會過時的檔案路徑與行號
- 沒有驗收標準
- 沒有範圍邊界
- 沒有描述目前行為與期望行為
