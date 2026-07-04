---
name: setup-pre-commit
description: 在目前 repo 中設定 Husky pre-commit hooks，整合 lint-staged（Prettier）、型別檢查和測試。用於：使用者想添加 pre-commit hooks、設定 Husky、設定 lint-staged，或添加提交時的格式化/型別檢查/測試時。
---

# Setup Pre-Commit Hooks（設定 Pre-Commit Hooks）

## 設定內容

- **Husky** pre-commit hook
- **lint-staged** 對所有暫存檔案執行 Prettier
- **Prettier** 設定（如果缺少）
- pre-commit hook 中的 **typecheck** 和 **test** 腳本

## 步驟

### 1. 偵測套件管理器

檢查 `package-lock.json`（npm）、`pnpm-lock.yaml`（pnpm）、`yarn.lock`（yarn）、`bun.lockb`（bun）。使用存在的那個。如果不清楚，預設為 npm。

### 2. 安裝依賴

安裝為 devDependencies：

```
husky lint-staged prettier
```

### 3. 初始化 Husky

```bash
npx husky init
```

這會建立 `.husky/` 目錄，並在 package.json 中添加 `prepare: "husky"`。

### 4. 建立 `.husky/pre-commit`

寫入這個檔案（Husky v9+ 不需要 shebang）：

```
npx lint-staged
npm run typecheck
npm run test
```

**調整**：將 `npm` 替換為偵測到的套件管理器。如果 repo 在 package.json 中沒有 `typecheck` 或 `test` 腳本，省略那些行並告知使用者。

### 5. 建立 `.lintstagedrc`

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

### 6. 建立 `.prettierrc`（如果缺少）

只在沒有 Prettier 設定時建立。使用這些預設值：

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

### 7. 驗證

- [ ] `.husky/pre-commit` 存在且可執行
- [ ] `.lintstagedrc` 存在
- [ ] package.json 中的 `prepare` 腳本是 `"husky"`
- [ ] `prettier` 設定存在
- [ ] 執行 `npx lint-staged` 驗證它能運作

### 8. 提交

暫存所有更改/建立的檔案，並用訊息提交：`Add pre-commit hooks (husky + lint-staged + prettier)`

這將通過新的 pre-commit hooks 執行——很好的冒煙測試，確認一切正常。

## 注意事項

- Husky v9+ 在 hook 檔案中不需要 shebang
- `prettier --ignore-unknown` 跳過 Prettier 無法解析的檔案（圖片等）
- pre-commit 先執行 lint-staged（快速，只處理暫存的）、然後是完整型別檢查、最後是測試
