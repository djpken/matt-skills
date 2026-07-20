---
name: setup-ts-deep-modules
description: 把 dependency-cruiser 接進 TypeScript repo，讓每個 package 都是一個深層模組——實作藏在子資料夾裡，只能透過它的 entry-point 檔案抵達。使用者呼叫。
disable-model-invocation: true
---

# Setup TS Deep Modules

把這個 repo 裡的每個 package 都變成一個**深層模組（deep module）**：用一個小介面承載大量行為。一個 package 的公開介面是它的 **entry points**——package 根目錄下的檔案——子資料夾裡的一切都是隱藏的。這個 skill 會安裝 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 和讓 entry points 成為唯一入口的規則，然後證明這些規則真的會咬人。

詞彙（deep module、interface、seam、depth）請執行 `/codebase-design` skill——全程使用它的用語。

## 這個規則強制的形狀

```
src/packages/
  <name>/
    index.ts        ← 一個 entry point（公開）。從外部 import 這個。
    client.ts       ← 另一個 entry point。Package 可以暴露「好幾個」。
    lib/             ← 實作：對外隱藏，彼此可以自由 import。
    tests/           ← 同址測試 + fixtures（一個子資料夾，所以是私有的）。
```

公開介面是 package 的**根目錄檔案**——不是指定的某一個 `index.ts`。慣例上實作放在 `lib/`、測試放在 `tests/`，讓每個 package 都有同樣的兩個資料夾形狀。不過規則本身是通用的：*任何*子資料夾裡的東西都是私有的，所以你永遠不需要為了新增資料夾去擴充設定。

四條規則，全部是 `error`：

1. **Entry-point 邊界**——package 外部的程式碼（app 程式碼或另一個 package）只能 import 該 package 的 entry points（它的根目錄檔案），絕不能 import 它子資料夾裡的任何東西。
2. **Package 內部自由**——package 自己的檔案可以彼此自由 import。
3. **測試透過 entry points**——`<pkg>/tests/` 底下的檔案可以 import 任何 package 的 entry points 和自己的 `tests/` fixtures，但絕不能 import 任何 package 子資料夾內部的東西（即使是自己的）。跨 package 的整合測試沒問題；深層 import 不行。
4. **沒有循環**——不能有依賴循環。

**Entry points，不是 barrel。** 因為公開介面是*每一個*根目錄檔案，一個 package 可以暴露好幾個小 entry points（`index.ts`、`client.ts`、`server.ts`），而不是把所有東西都灌進一個巨大的 `index.ts`。不鼓勵用會重新匯出整個子樹的 barrel 檔案——讓 entry points 保持小巧，把實作藏在子資料夾裡。

分層（哪些 package 可以依賴哪些）是*另一個*議題，設定檔裡留了一段註解 stub，給這個 repo 自行填寫。

## 步驟

### 1. 偵測環境

- **套件管理工具**——`pnpm-lock.yaml` → pnpm，`yarn.lock` → yarn，`bun.lockb` → bun，否則用 npm。下面每個指令都用它（`pnpm`/`yarn`/`npm run`/`bunx`）。
- **Packages 根目錄**——如果 `src/` 存在就用 `src/packages`，否則用 `packages`。如果 repo 已經有其他明顯的慣例，跟使用者確認這個選擇。
- **既有設定**——檢查是否有 `.dependency-cruiser.*` 檔案。如果已存在，**不要**覆蓋它：把四條規則和選項合併進去，並告訴使用者你加了什麼。

**完成標準：** 套件管理工具、packages 根目錄、既有設定狀態都已確定。

### 2. 安裝 dependency-cruiser

用偵測到的套件管理工具，把 `dependency-cruiser` 安裝為 devDependency。

**完成標準：** `dependency-cruiser` 在 `devDependencies` 裡。

### 3. 寫入設定

把 [`dependency-cruiser.config.cjs`](./dependency-cruiser.config.cjs) 複製到 repo 根目錄，命名為 `.dependency-cruiser.cjs`。把 `PACKAGES_ROOT` 設成步驟 1 偵測到的根目錄。這些規則是依 path-depth 判斷、與副檔名無關，所以不需要再調整其他東西。

**完成標準：** `.dependency-cruiser.cjs` 存在，`PACKAGES_ROOT` 正確，四條禁止規則都在。

### 4. 接進檢查流程

- 加一個 `lint:boundaries` script：`depcruise <packages-root>`（或 `depcruise src`）。
- 把它併入 repo 既有跑 typecheck 的總開關指令（例如 `check` / `ci` / `validate` script）。**不要**動 `tsconfig` 或加 path alias。
- 如果沒有總開關 script，就加上 `lint:boundaries`，並告訴使用者要把它納入 CI。

**完成標準：** `lint:boundaries` 存在，且和 typecheck 跑在同一個指令裡。

### 5. 建立範例 package

建立一個會被 commit 的 `<packages-root>/example/`，作為可複製的範本：

- `index.ts` —— 一個 entry point。匯出一個函式，委派給內部檔案（讓這個 package 明顯是*深層*的，不是個 pass-through）。
- `lib/impl.ts` —— **子資料夾**裡的內部檔案，被 `index.ts` import，外部無法抵達。
- `tests/example.test.ts` —— **只** import `../index`（一個 entry point），對公開函式做斷言。

告訴使用者這是可以複製或刪除的起始範本。

**完成標準：** 範例 package 存在，透過根目錄 entry point 暴露行為，並把 `impl` 藏在子資料夾裡。

### 6. 證明規則真的會咬人

這是整個 skill 的完成標準——一個違規時不會失敗的設定毫無價值。

1. 執行 `lint:boundaries`。在乾淨的範例上必須**通過**。
2. 暫時在 `tests/example.test.ts` 加一個深層 import（例如 `import { thing } from "../lib/impl"`）。再跑一次 `lint:boundaries`——必須以 `tests-through-entrypoints` **失敗**。
3. 還原那個深層 import。再跑一次——必須**通過**。

**完成標準：** 你已經觀察到通過、然後在深層 import 上失敗、然後再次通過。如果步驟 2 沒有失敗，代表規則沒接對——完成前要先修好。

### 7. 記錄慣例

**在 packages 資料夾裡**（`<packages-root>/README.md`）寫一份 `README.md`——放在它管轄的 packages 旁邊——內容涵蓋：`src/packages/<name>/` 的格局（entry points 在根目錄、`lib/` 放實作、`tests/` 放測試）、「只透過 package 的 entry points（它的根目錄檔案）import」，以及如何執行 `lint:boundaries`。**明確不鼓勵 barrel 檔案**——改用暴露好幾個小 entry points 的方式，而不是透過一個 index 重新匯出整個子樹。內容維持在可複製的片段加上四條規則各一段就好。

然後從 repo 的 agent 指示檔案——`CLAUDE.md`（若存在），否則 `AGENTS.md`（兩者都不存在就建立 `AGENTS.md`）——加一個 **context pointer** 指向它。一行就夠，例如：`Packages are deep modules — see [src/packages/README.md](./src/packages/README.md) before adding or importing one.` 這是讓 agent 主動發現邊界規則、而不是踩到才知道的關鍵。

**完成標準：** `<packages-root>/README.md` 存在且不鼓勵 barrel，repo 的 `CLAUDE.md`/`AGENTS.md` 有連結指向它。

## 注意事項

- 設定檔裡的 `$1` 反向參照（dependency-cruiser 的 group matching）是讓一個 package 能碰自己的內部、但外人不行的關鍵——不要把它們拆成各個 package 各自獨立的規則。
- 公開與私有是用**深度（depth）**判斷的：一個 package 的根目錄檔案是 entry points；任何子資料夾裡的東西都是私有的。慣例上的子資料夾是 `lib/`（實作）和 `tests/`，但規則不會寫死它們——任何子資料夾都是私有的，所以新增資料夾永遠不需要改設定。新增一個 entry point 只是新增一個根目錄檔案——不用 barrel。
- Package 是**扁平的**：根目錄下只有一層直接子項。一個 package 的內部可以任意深層巢狀；一個 package 不能包含另一個 package。
- 用 `.cjs`（不是 `.js`），這樣即使在 `"type": "module"` 的 repo 裡，設定檔的 `module.exports` 也能正常運作。
