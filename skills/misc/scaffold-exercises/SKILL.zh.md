---
name: scaffold-exercises
description: 建立包含章節、題目、解答和解說的練習目錄結構，讓其通過 linting。用於：使用者想建立練習腳手架、建立練習存根，或設定新的課程章節時。
---

# Scaffold Exercises（建立練習腳手架）

建立通過 `pnpm ai-hero-cli internal lint` 的練習目錄結構，然後用 `git commit` 提交。

## 目錄命名

- **章節（Sections）**：在 `exercises/` 內的 `XX-section-name/`（例如，`01-retrieval-skill-building`）
- **練習（Exercises）**：在章節內的 `XX.YY-exercise-name/`（例如，`01.03-retrieval-with-bm25`）
- 章節編號 = `XX`，練習編號 = `XX.YY`
- 名稱使用 dash-case（小寫，連字號）

## 練習變體

每個練習至少需要以下子資料夾之一：

- `problem/` — 學生工作區，有 TODO
- `solution/` — 參考實作
- `explainer/` — 概念材料，沒有 TODO

建立存根時，預設為 `explainer/`，除非計畫另有說明。

## 必要檔案

每個子資料夾（`problem/`、`solution/`、`explainer/`）需要一個 `readme.md`，其中：

- **不為空**（必須有真實內容，即使只有一個標題行也可以）
- 沒有失效的連結

建立存根時，建立一個帶有標題和描述的最小 readme：

```md
# Exercise Title

Description here
```

如果子資料夾有程式碼，也需要一個 `main.ts`（超過 1 行）。但對於存根，只有 readme 的練習也可以。

## 工作流程

1. **解析計畫** — 提取章節名稱、練習名稱和變體類型
2. **建立目錄** — 對每個路徑使用 `mkdir -p`
3. **建立存根 readme** — 每個變體資料夾一個 `readme.md`，帶有標題
4. **執行 lint** — `pnpm ai-hero-cli internal lint` 驗證
5. **修復任何錯誤** — 反覆迭代直到 lint 通過

## Lint 規則摘要

linter（`pnpm ai-hero-cli internal lint`）檢查：

- 每個練習有子資料夾（`problem/`、`solution/`、`explainer/`）
- `problem/`、`explainer/` 或 `explainer.1/` 中至少存在一個
- `readme.md` 存在且在主要子資料夾中非空
- 沒有 `.gitkeep` 檔案
- 沒有 `speaker-notes.md` 檔案
- readme 中沒有失效的連結
- readme 中沒有 `pnpm run exercise` 指令
- 每個子資料夾需要 `main.ts`，除非它只有 readme

## 移動/重命名練習

重新編號或移動練習時：

1. 使用 `git mv`（不是 `mv`）重命名目錄——保留 git 歷史記錄
2. 更新數字前綴以維持順序
3. 移動後重新執行 lint

範例：

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## 範例：從計畫建立存根

給定如下計畫：

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

建立：

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

然後建立 readme 存根：

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
