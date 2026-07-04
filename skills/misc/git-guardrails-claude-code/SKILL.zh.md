---
name: git-guardrails-claude-code
description: 設定 Claude Code hooks，在危險 git 指令（push、reset --hard、clean、branch -D 等）執行前攔截。用於：使用者想防止危險 git 操作、添加 git 安全 hooks，或在 Claude Code 中阻擋 git push/reset 時。
---

# Setup Git Guardrails（設定 Git 防護欄）

設定一個 PreToolUse hook，在 Claude 執行之前攔截並阻擋危險的 git 指令。

## 被阻擋的內容

- `git push`（所有變體，包括 `--force`）
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

被阻擋時，Claude 會看到一條訊息，告訴它沒有執行這些指令的權限。

## 步驟

### 1. 詢問範圍

詢問使用者：只安裝在**此專案**（`.claude/settings.json`）還是**所有專案**（`~/.claude/settings.json`）？

### 2. 複製 hook 腳本

捆綁的腳本在：[scripts/block-dangerous-git.sh](scripts/block-dangerous-git.sh)

根據範圍複製它到目標位置：

- **專案**：`.claude/hooks/block-dangerous-git.sh`
- **全局**：`~/.claude/hooks/block-dangerous-git.sh`

用 `chmod +x` 使其可執行。

### 3. 在設定中添加 hook

添加到適當的設定檔案：

**專案**（`.claude/settings.json`）：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

**全局**（`~/.claude/settings.json`）：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

如果設定檔案已存在，將 hook 合併到現有的 `hooks.PreToolUse` 陣列——不要覆蓋其他設定。

### 4. 詢問是否需要自訂

詢問使用者是否想從被阻擋列表中添加或移除任何模式。相應地編輯複製的腳本。

### 5. 驗證

執行快速測試：

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

應該以錯誤碼 2 退出，並在 stderr 輸出 BLOCKED 訊息。
