---
name: setup-ts-deep-modules
description: "將 dependency-cruiser 整合到 TypeScript 儲存庫中，使每個套件都成為深層模組，將實作隱藏在子資料夾中，並僅能透過其入口點檔案存取。由使用者呼叫。"
disable-model-invocation: true
---

<a id="setup-ts-deep-modules"></a>

# 設定 TS 深層模組

將此儲存庫中的每個套件都變成**深層模組**：在小型介面背後隱藏大量行為。套件的公開介面是其**入口點**（位於套件根目錄的檔案），而其子資料夾中的所有內容都是隱藏的。此 skill 會安裝 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 以及使入口點成為唯一進入方式的規則，然後證明這些規則確實有效。

對於術語（深層模組、介面、縫隙、深度），請呼叫 Skill 工具並輸入 "codebase-design"，並在整個過程中使用其語言。

<a id="the-shape-this-enforces"></a>

## 此設定強制執行的結構

```
src/packages/
  <name>/
    index.ts        ← an entry point (public). Import this from outside.
    client.ts       ← another entry point. Packages may expose SEVERAL.
    lib/            ← implementation: hidden from outside, free to import each other.
    tests/          ← co-located tests + fixtures (a subfolder, so private).
```

公開介面是套件的**根目錄檔案**，而非單一指定的 `index.ts`。依慣例，實作位於 `lib/`，測試位於 `tests/`，使每個套件都具有相同的雙資料夾結構。不過，規則本身是通用的：*任何* 子資料夾中的*任何*內容都是私有的，因此您永遠不需要擴充設定來新增資料夾。

四條規則，全部為 `error`：

1. **入口點邊界**：套件外部的程式碼（應用程式程式碼或其他套件）只能匯入該套件的入口點（其根目錄檔案），絕不能匯入其子資料夾中的任何內容。
2. **套件內自由**：套件自身的檔案可以自由地互相匯入。
3. **透過入口點進行測試**：`<pkg>/tests/` 下的檔案可以匯入任何套件的入口點及其自身的 `tests/` 測試資料，但絕不能匯入任何套件的子資料夾內部（即使是其自身的也不行）。跨套件的整合測試是可以的；深層匯入則不行。
4. **無迴圈**：不得有相依性迴圈。

**入口點，而非 barrel。** 由於公開介面是*所有*根目錄檔案，套件可以公開多個小型入口點（`index.ts`、`client.ts`、`server.ts`），而不是將所有內容都匯入一個巨大的 `index.ts`。不建議使用重新匯出整個子樹的 barrel 檔案；請保持入口點小巧，並將實作隱藏在子資料夾中。

分層（哪些套件可以相依於哪些套件）是一個*不同*的關注點，在此儲存庫的設定中保留為註解存根，以供後續填入。

<a id="steps"></a>

## 步驟

<a id="1-detect-the-environment"></a>

### 1. 偵測環境

- **套件管理器**：`pnpm-lock.yaml` → pnpm，`yarn.lock` → yarn，`bun.lockb` → bun，否則為 npm。在以下所有命令中使用它（`pnpm`/`yarn`/`npm run`/`bunx`）。
- **套件根目錄**：如果 `src/` 存在，則使用 `src/packages`，否則使用 `packages`。如果儲存庫已有其他明顯的慣例，請與使用者確認此選擇。
- **現有設定**：檢查是否存在 `.dependency-cruiser.*` 檔案。如果存在，請**不要**覆蓋它：將四條規則和選項合併進去，並告知使用者您新增了什麼。

**完成條件：** 已知套件管理器、套件根目錄以及現有設定的狀態。

<a id="2-install-dependency-cruiser"></a>

### 2. 安裝 dependency-cruiser

使用偵測到的套件管理器將 `dependency-cruiser` 安裝為 devDependency。

**完成條件：** `dependency-cruiser` 已位於 `devDependencies` 中。

<a id="3-write-the-config"></a>

### 3. 撰寫設定

將 [`dependency-cruiser.config.cjs`](./dependency-cruiser.config.cjs) 複製到儲存庫根目錄，命名為 `.dependency-cruiser.cjs`。將 `PACKAGES_ROOT` 設定為步驟 1 中偵測到的根目錄。這些規則基於路徑深度且與副檔名無關，因此無需進行其他調整。

**完成條件：** `.dependency-cruiser.cjs` 存在且具有正確的 `PACKAGES_ROOT`，且四條禁止規則已存在。

<a id="4-wire-it-into-the-checks"></a>

### 4. 將其整合到檢查中

- 新增一個 `lint:boundaries` 指令碼：`depcruise <packages-root>`（或 `depcruise src`）。
- 將其納入儲存庫的總體檢查命令，即已經執行 typecheck 的那個命令（例如 `check` / `ci` / `validate` 指令碼）。請**不要**觸碰 `tsconfig` 或新增路徑別名。
- 如果沒有總體指令碼，請新增 `lint:boundaries` 並告知使用者將其包含在 CI 中。

**完成條件：** `lint:boundaries` 存在，並作為與 typecheck 相同命令的一部分執行。

<a id="5-scaffold-the-example-package"></a>

### 5. 建立範例套件骨架

建立一個已提交的 `<packages-root>/example/` 作為複製範本：

- `index.ts` 是一個入口點。匯入一個函式，該函式委派給內部檔案（使套件明顯是*深層*的，而非僅為轉發）。
- `lib/impl.ts`：位於**子資料夾**中的內部檔案，由 `index.ts` 匯入，外部無法存取。
- `tests/example.test.ts` 僅匯入 `../index`（一個入口點），並針對公開函式進行斷言。

告知使用者這是一個起始範本，可以複製或刪除。

**完成條件：** 範例套件存在，透過根目錄入口點公開其行為，並將 `impl` 隱藏在子資料夾中。

<a id="6-prove-the-rules-bite"></a>

### 6. 證明規則確實有效

這是整個 skill 的完成標準：一個在違規時不會失敗的設定毫無價值。

1. 執行 `lint:boundaries`。它必須在乾淨的範例上**透過**。
2. 暫時在 `tests/example.test.ts` 中新增一個深層匯入（例如 `import { thing } from "../lib/impl"`）。再次執行 `lint:boundaries`；它必須**失敗**並顯示 `tests-through-entrypoints`。
3. 還原深層匯入。再執行一次，它必須**透過**。

**完成條件：** 您已觀察到一次透過，然後在深層匯入上失敗，然後再次透過。如果步驟 2 未失敗，則表示規則未正確整合，請在結束前進行修正。

<a id="7-document-the-convention"></a>

### 7. 記錄慣例

在**套件資料夾**中撰寫 `README.md`（`<packages-root>/README.md`，位於其所管轄的套件旁邊），涵蓋：`src/packages/<name>/` 結構（根目錄為入口點，`lib/` 用於實作，`tests/` 用於測試）、"僅透過套件的入口點（其根目錄檔案）匯入"，以及如何執行 `lint:boundaries`。**明確不建議使用 barrel 檔案**：公開多個小型入口點，而不是透過單一 index 重新匯出整個子樹。請將其限制為複製範本片段加上四條規則，每條規則一段。

然後從儲存庫的 agent 指令檔案（如果存在 `CLAUDE.md` 則使用它，否則使用 `AGENTS.md`，如果兩者都不存在則建立 `AGENTS.md`）新增一個**上下文指標**指向它。一行就足夠了，例如 `Packages are deep modules: see [src/packages/README.md](./src/packages/README.md) before adding or importing one.` 這能讓 agent 發現邊界規則，而不是意外觸犯它。

**完成條件：** `<packages-root>/README.md` 存在且不建議使用 barrels，且儲存庫的 `CLAUDE.md`/`AGENTS.md` 連結到它。

<a id="notes"></a>

## 備註

- 設定中的 `$1` 反向引用（dependency-cruiser 的群組匹配）使得套件可以存取其自身的內部，而外部人員則不能。不要將它們扁平化為每個套件獨立的規則。
- 公開與私有的判定依據是**深度**：套件的根目錄檔案是入口點；子資料夾中的任何內容都是私有的。慣用的子資料夾是 `lib/`（實作）和 `tests/`，但規則並未硬編碼它們：任何子資料夾都是私有的，因此新增資料夾永遠不需要變更設定。新增入口點只需新增一個根目錄檔案（無需 barrel）。
- 套件是**扁平**的：根目錄下只有一層直接子專案。套件內部可以任意深度巢狀；但套件不得包含另一個套件。
- 請使用 `.cjs`（而非 `.js`），以便設定中的 `module.exports` 即使在 `"type": "module"` 儲存庫中也能正常運作。
