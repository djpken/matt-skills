---
name: git-guardrails-claude-code
description: "設定 Claude Code hooks，以在執行前封鎖危險的 git 指令（push、reset --hard、clean、branch -D 等）。當使用者想要防止破壞性的 git 操作、新增 git 安全性 hooks，或在 Claude Code 中封鎖 git push/reset 時使用。"
---

<a id="setup-git-guardrails"></a>

# 設定 Git Guardrails

設定一個 PreToolUse hook，在 Claude 執行前攔截並封鎖危險的 git 指令。

<a id="what-gets-blocked"></a>

## 被封鎖的內容

- `git push`（包含 `--force` 在內的所有變體）
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

當被封鎖時，Claude 會看到一條訊息，告知其沒有許可權存取這些指令。

<a id="steps"></a>

## 步驟

<a id="1-ask-scope"></a>

### 1. 詢問範圍

詢問使用者：是安裝給**僅此專案**（`.claude/settings.json`）還是**所有專案**（`~/.claude/settings.json`）？

<a id="2-copy-the-hook-script"></a>

### 2. 複製 hook 指令碼

隨附的指令碼位於：[scripts/block-dangerous-git.sh](scripts/block-dangerous-git.sh)

根據範圍將其複製到目標位置：

- **專案**：`.claude/hooks/block-dangerous-git.sh`
- **全域**：`~/.claude/hooks/block-dangerous-git.sh`

使用 `chmod +x` 使其可執行。

<a id="3-add-hook-to-settings"></a>

### 3. 將 hook 新增至設定

新增至適當的設定檔：

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

**全域**（`~/.claude/settings.json`）：

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

如果設定檔已存在，請將 hook 合併至現有的 `hooks.PreToolUse` 陣列中。不要覆寫其他設定。

<a id="4-ask-about-customization"></a>

### 4. 詢問自訂需求

詢問使用者是否想要從封鎖清單中新增或移除任何模式。請相應地編輯複製的指令碼。

<a id="5-verify"></a>

### 5. 驗證

執行快速測試：

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

應以程式碼 2 結束，並向 stderr 列印 BLOCKED 訊息。
