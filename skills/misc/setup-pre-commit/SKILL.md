---
name: setup-pre-commit
description: "在目前儲存庫中設定 Husky pre-commit hooks，並搭配 lint-staged (Prettier)、型別檢查與測試。當使用者想要新增 pre-commit hooks、設定 Husky、設定 lint-staged，或在 commit 時加入格式化、型別檢查或測試時使用。"
---

<a id="setup-pre-commit-hooks"></a>

# 設定 Pre-Commit Hooks

<a id="what-this-sets-up"></a>

## 此設定包含的內容

- **Husky** pre-commit hook
- **lint-staged** 對所有已暫存的檔案執行 Prettier
- **Prettier** 設定（若不存在）
- pre-commit hook 中的 **typecheck** 與 **test** 指令碼

<a id="steps"></a>

## 步驟

<a id="1-detect-package-manager"></a>

### 1. 偵測套件管理員

檢查是否存在 `package-lock.json` (npm)、`pnpm-lock.yaml` (pnpm)、`yarn.lock` (yarn)、`bun.lockb` (bun)。使用存在的那一個。若無法判斷，預設使用 npm。

<a id="2-install-dependencies"></a>

### 2. 安裝相依套件

安裝為 devDependencies：

```
husky lint-staged prettier
```

<a id="3-initialize-husky"></a>

### 3. 初始化 Husky

```bash
npx husky init
```

這會建立 `.husky/` 資料夾，並將 `prepare: "husky"` 加入 package.json。

<a id="4-create-huskypre-commit"></a>

### 4. 建立 `.husky/pre-commit`

寫入此檔案（Husky v9+ 不需要 shebang）：

```
npx lint-staged
npm run typecheck
npm run test
```

**調整**：將 `npm` 替換為偵測到的套件管理員。若儲存庫的 package.json 中沒有 `typecheck` 或 `test` 指令碼，請省略那些行並告知使用者。

<a id="5-create-lintstagedrc"></a>

### 5. 建立 `.lintstagedrc`

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

<a id="6-create-prettierrc-if-missing"></a>

### 6. 建立 `.prettierrc`（若不存在）

僅在沒有 Prettier 設定時建立。使用以下預設值：

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

<a id="7-verify"></a>

### 7. 驗證

- [ ] `.husky/pre-commit` 存在且可執行
- [ ] `.lintstagedrc` 存在
- [ ] package.json 中的 `prepare` 指令碼為 `"husky"`
- [ ] `prettier` 設定存在
- [ ] 執行 `npx lint-staged` 以驗證其運作正常

<a id="8-commit"></a>

### 8. 提交

暫存所有已變更或建立的檔案，並使用以下訊息提交：`Add pre-commit hooks (husky + lint-staged + prettier)`

這將透過新的 pre-commit hooks 執行：這是一個良好的冒煙測試，可確認一切運作正常。

<a id="notes"></a>

## 注意事項

- Husky v9+ 的 hook 檔案不需要 shebang
- `prettier --ignore-unknown` 會跳過 Prettier 無法解析的檔案（例如圖片）
- pre-commit 會先執行 lint-staged（快速，僅限已暫存檔案），然後執行完整的型別檢查與測試
